# GRE-over-IP-SEC: MI-2901 &mdash; Device Configuration

```
enable
configure terminal

! Disable DNS lookup and set hostname
no ip domain-lookup
hostname MI-2901

! Create admin user
username admin secret cisco

! Configure console settings
line console 0
logging synchronous
exit

! Configure domain name and generate RSA key
ip domain-name MI.synapsetechnologies.com
crypto key generate rsa general-keys modulus 1024

! Set banner message and enable secret
banner motd $Welcome to MI Branch.$
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

exit
```
### Interface Configuration

```plaintext
enable
configure terminal

interface GigabitEthernet0/1
description Link to Main Router MI Branch
ip address 10.100.100.1 255.255.255.252
no shutdown
exit

interface GigabitEthernet0/0
description Link to ISP Router MI Branch
ip address 4.4.1.2 255.255.255.252
no shutdown
exit

exit
```

### EIGRP Configuration

```plaintext
enable
configure terminal

router eigrp 100
eigrp router-id 100.100.100.100
passive-interface default
no passive-interface GigabitEthernet0/1
no passive-interface Tunnel 300
no passive-interface Tunnel 400
no auto-summary

network 10.100.100.0
network 172.16.100.0
network 172.16.100.0


exit
```

### NAT Configuration

```plaintext
enable
configure terminal

! Access list defining the source addresses to be translated
access-list 1 permit 10.100.0.0 0.0.255.255

! NAT configuration
ip nat pool MI-Pool 4.4.1.2 4.4.1.2 netmask 255.255.255.252
ip nat inside source list 1 pool MI-Pool overload

! Apply NAT to the interfaces
interface GigabitEthernet0/0/0
ip nat inside
exit

interface Serial0/3/0
ip nat outside
exit

! Configure default route
ip route 0.0.0.0 0.0.0.0 4.4.1.1

exit
```

### EIGRP Configuration

```plaintext
enable
configure terminal

router eigrp 100
eigrp router-id 111.111.111.111
passive-interface default
no passive-interface GigabitEthernet0/0/0
network 10.100.100.0
network 172.16.100.0
no auto-summary

exit

router eigrp 100
eigrp router-id 33.33.33.33
passive-interface default
no passive-interface GigabitEthernet0/1
no passive-interface Tunnel 300
no passive-interface TUnnel 400
no auto-summary
network 10.3.100.0
network 172.16.100.0
network 172.16.200.0
```

## GRE
```
interface Tunnel 100
description GRE Tunnel to LA
ip address 172.16.100.2 255.255.255.252
tunnel source Serial 0/3/0
tunnel destination 2.2.1.2
exit

interface Tunnel 200
description GRE Tunnel to NY
ip address 172.16.200.1 255.255.255.252
tunnel source Serial 0/3/0
tunnel destination 4.4.2.2
exit
```


## IPsec

```

crypto isakmp policy 1
encr aes
authentication pre-share
group 2


crypto isakmp key P@ssw0rd address 2.2.1.2

crypto ipsec transform-set ESP-AES-SHA esp-aes esp-sha-hmac

crypto map IPSEC-MAP 30 ipsec-isakmp
set peer 2.2.1.2
set transform-set ESP-AES-SHA
match address GRE-to-IPSEC

ip access-list extended GRE-to-IPSEC
permit gre host 4.4.1.2 host 2.2.1.2

interface GigabitEthernet0/0
crypto map IPSEC-MAP
no shutdown


```