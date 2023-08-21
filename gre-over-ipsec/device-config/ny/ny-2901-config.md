# GRE-over-IP-SEC: NY-2901 &mdash; Device Configuration

## Basic Configuration

```plaintext
enable
configure terminal

! Disable DNS lookup and set hostname
no ip domain-lookup
hostname NY-2901

! Create admin user
username admin secret cisco

! Configure console settings
line console 0
logging synchronous
exit

! Configure domain name and generate RSA key
ip domain-name NY.synapsetechnologies.com
crypto key generate rsa general-keys modulus 1024

! Set banner message and enable secret
banner motd $Welcome to NY Branch.$
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
## INterface

```
enable
configure terminal

! Configure GigabitEthernet0/1 interface
interface GigabitEthernet0/1
description Link to Main Router NY Branch
ip address 10.200.100.1 255.255.255.252
no shutdown
exit

interface G0/0
description Link to ISP NY Branch
ip address 4.4.2.2 255.255.255.252
no shutdown
exit

exit
```

enable
configure terminal

router eigrp 100
eigrp router-id 200.200.200.200
passive-interface default
no passive-interface GigabitEthernet0/1
no passive-interface tunnel 400
no auto-summary
network 10.200.100.0 
network 172.16.200.0
no auto-summary

exit

# NAT

enable
configure terminal

! Access list defining the source addresses to be translated
access-list 1 permit 10.200.0.0 0.0.255.255

! NAT configuration
ip nat pool NY-Pool 4.4.2.2 4.4.2.2 netmask 255.255.255.252
ip nat inside source list 1 pool NY-Pool overload

! Apply NAT to the interface
interface GigabitEthernet0/1
ip nat inside
exit

interface  GigabitEthernet0/0
ip nat outside
exit



## IPSec

```

```