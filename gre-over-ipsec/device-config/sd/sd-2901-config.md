# GRE-ver-IPSec: SD-2901 &mdash; Device Configuration

## Interface Configuration

```plaintext
enable
configure terminal

! Configure GigabitEthernet0/1 interface
interface GigabitEthernet0/1
 description Link to Main Router SD Branch
 ip address 10.2.100.1 255.255.255.252
 no shutdown
 exit

! Configure GigabitEthernet0/0 interface
interface GigabitEthernet0/0
 description Link to ISP: SD Branch
 ip address 2.2.2.2 255.255.255.252
 no shutdown
 exit

exit
```

## NAT Configuration

```plaintext
enable
configure terminal

! Access list defining the source addresses to be translated
access-list 1 permit 10.2.0.0 0.0.255.255

! NAT configuration
ip nat pool SD-Pool 2.2.2.2 2.2.2.2 netmask 255.255.255.252
ip nat inside source list 1 pool SD-Pool overload

! Apply NAT to the interface
interface GigabitEthernet0/1
 ip nat inside
 exit

interface GigabitEthernet0/0
 ip nat outside
 exit
```

## EIGRP Configuration

```plaintext
enable
configure terminal

router eigrp 100
 eigrp router-id 22.22.22.22
 passive-interface default
 no passive-interface GigabitEthernet0/1
 no passive-interface Tunnel100
 no auto-summary
 network 10.2.100.0
 network 192.168.100.0
 exit
```

## GRE Configuration

```plaintext
enable
configure terminal

interface Tunnel100
 ip address 192.168.100.2 255.255.255.252
 tunnel source GigabitEthernet0/0
 tunnel destination 2.2.1.2
 exit

exit
```

## IP-SEC

```

crypto isakmp policy 1
encr aes
authentication pre-share
group 2

crypto isakmp key P@ssw0rd address 2.2.1.2

crypto ipsec transform-set ESP-AES-SHA esp-aes esp-sha-hmac


crypto map IPSEC-MAP 20 ipsec-isakmp
set peer 2.2.1.2
set transform-set ESP-AES-SHA
match address GRE-to-IPSEC


ip access-list extended GRE-to-IPSEC
permit gre host 2.2.2.2 host 2.2.1.2

interface GigabitEthernet0/0
ip address 2.2.1.1 255.255.255.252
crypto map IPSEC-MAP
no shutdown

```