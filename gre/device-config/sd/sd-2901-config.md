## Interface COnfiguration
```
enable
configure terminal

! Configure GigabitEthernet0/0/0 interface
interface GigabitEthernet0/0/0
 description Link to Main Router SD Branch
 ip address 10.2.100.1 255.255.255.252
 no shutdown
 exit

! Configure Serial0/3/0 interface
interface Serial0/3/0
 description Link to ISP : SD Branch
 ip address 2.2.2.2 255.255.255.252
 no shutdown
 exit

exit
```

# NAT
```
enable
configure terminal

! Access list defining the source addresses to be translated
access-list 1 permit 10.2.0.0 0.0.255.255
access-list 1 permit 192.168.1.0 0.0.0.3
access-list 1 permit 192.168.2.0 0.0.0.3
access-list 1 permit 192.168.3.0 0.0.0.3

! NAT configuration
ip nat pool SD-Pool 2.2.2.2 2.2.2.2 netmask 255.255.255.252
ip nat inside source list 1 pool SD-Pool overload

! Apply NAT to the interface
interface GigabitEthernet0/0/0
 ip nat inside
 exit

interface Serial0/3/0
 ip nat outside
 exit
 ```


 # EIGRP

```
enable
configure terminal

router eigrp 1
 eigrp router-id 22.22.22.22
 passive-interface default
 no passive-interface GigabitEthernet0/0/0
 network 10.2.100.0 0.0.0.3
 network 2.2.2.0 0.0.0.3
 no auto-summary

exit


```

# GRE
```
enable
configure terminal

interface Tunnel2
description $GRE Tunnel to LA$
ip address 192.168.3.2 255.255.255.252
 tunnel source GigabitEthernet0/0/0
 tunnel destination 2.2.1.2
 exit

exit
```