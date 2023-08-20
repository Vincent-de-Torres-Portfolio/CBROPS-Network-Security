## INterface

```
enable
configure terminal

! Configure GigabitEthernet0/0/0 interface
interface GigabitEthernet0/0/0
 description Link to Main Router NY Branch
 ip address 10.102.100.1 255.255.255.252
 no shutdown
 exit

! Configure Serial0/3/0 interface
interface Serial0/3/0
 description Link to ISP NY Branch
 ip address 4.4.2.2 255.255.255.252
 no shutdown
 exit

exit
```

enable
configure terminal

router eigrp 1
 eigrp router-id 112.112.112.112
 passive-interface default
 no passive-interface GigabitEthernet0/0/0
 network 4.4.2.0 0.0.0.3
 network 10.102.100.0 0.0.0.3
 no auto-summary

exit

# NAT

enable
configure terminal

! Access list defining the source addresses to be translated
access-list 1 permit 10.102.0.0 0.0.255.255

! NAT configuration
ip nat pool NY-Pool 4.4.2.2 4.4.2.2 netmask 255.255.255.252
ip nat inside source list 1 pool NY-Pool overload

! Apply NAT to the interface
interface GigabitEthernet0/0/0
 ip nat inside
 exit

interface Serial0/3/0
 ip nat outside
 exit



