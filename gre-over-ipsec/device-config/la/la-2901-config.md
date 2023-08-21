# GRE-over-IP-SEC:  LA-2901 &mdash; Device Configuration
## Basic Configuration

```plaintext
enable
configure terminal

! Disable DNS lookup and set hostname
no ip domain-lookup
hostname LA_2901

! Create admin user
username admin secret cisco

! Configure console settings
line console 0
logging synchronous
exit

! Configure domain name and generate RSA key
conf t
ip domain-name la.synapsetechnologies.com
crypto key generate rsa general-keys modulus 1024

! Set banner message and enable secret
banner motd $Welcome to LA Branch.$
enable secret cisco

! Configure console password
line console 0
password cisco
login
exit

! Configure SSH for remote management
line vty 0 4
login local
transport input ssh
ip ssh version 2
```

## Interface Configuration

```plaintext
enable
configure terminal

! Configure GigabitEthernet0/1 interface
interface GigabitEthernet0/1
description Link to Main Router LA Branch
ip address 10.1.100.1 255.255.255.252
no shutdown
exit

! Configure GigabitEthernet0/0 interface
interface GigabitEthernet0/0
description Link to ISP LA Branch
ip address 2.2.1.2 255.255.255.252
no shutdown
exit

exit
```

## NAT Configuration

```plaintext
enable
configure terminal

! Access list defining the source addresses to be translated
access-list 1 permit 10.1.0.0 0.0.255.255

! NAT configuration
ip nat pool LA-Pool 2.2.1.2 2.2.1.2 netmask 255.255.255.252
ip nat inside source list 1 pool LA-Pool overload

! Apply NAT to the interface
interface GigabitEthernet0/1
ip nat inside
exit

interface GigabitEthernet 0/0
ip nat outside
exit
```

## EIGRP Configuration

```plaintext
enable
configure terminal

router eigrp 100
eigrp router-id 11.11.11.11
passive-interface default
no passive-interface GigabitEthernet0/1
no auto-summary
network 10.1.100.0
network 192.168.100.0
network 192.168.200.0
network 172.16.100.0

exit
```

## Default Route 
```
ip route 0.0.0.0 0.0.0.0 2.2.1.1
```

## GRE Configuration

```plaintext
enable
configure terminal

interface Tunnel100
description GRE Tunnel to SD
ip address 192.168.100.1 255.255.255.252
tunnel source GigabitEthernet0/0
tunnel destination 2.2.2.2
exit

interface Tunnel200
description GRE Tunnel to SF
ip address 192.168.200.1 255.255.255.252
tunnel source GigabitEthernet0/0
tunnel destination 2.2.3.2
exit

interface Tunnel300
description GRE Tunnel to MI
ip address 172.16.100.1 255.255.255.252
tunnel source GigabitEthernet0/0
tunnel destination 4.4.1.2
exit

exit
```

## IP-SEC

```

crypto isakmp policy 1
encr aes
authentication pre-share
group 2

crypto isakmp key P@ssw0rd address 2.2.2.2
crypto isakmp key P@ssw0rd address 2.2.3.2
crypto isakmp key P@ssw0rd address 4.4.1.2

crypto ipsec transform-set ESP-AES-SHA esp-aes esp-sha-hmac

crypto map IPSEC-MAP 10 ipsec-isakmp
set peer 2.2.2.2
set transform-set ESP-AES-SHA
match address GRE-to-IPSEC


crypto map IPSEC-MAP 20 ipsec-isakmp
set peer 2.2.3.2
set transform-set ESP-AES-SHA
match address GRE-to-IPSEC-2

crypto map IPSEC-MAP 30 ipsec-isakmp
set peer 4.4.1.2
set transform-set ESP-AES-SHA
match address GRE-to-IPSEC-3


ip access-list extended GRE-to-IPSEC
permit gre host 2.2.1.2 host 2.2.2.2

ip access-list extended GRE-to-IPSEC-2
permit gre host 2.2.1.2 host 2.2.3.2

ip access-list extended GRE-to-IPSEC-3
permit gre host 2.2.1.2 host 4.4.1.2

interface GigabitEthernet0/0
ip address 2.2.1.1 255.255.255.252
crypto map IPSEC-MAP
no shutdown


```