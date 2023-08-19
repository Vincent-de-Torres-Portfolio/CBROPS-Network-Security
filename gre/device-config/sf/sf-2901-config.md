## Interface
```
enable
configure terminal

! Configure GigabitEthernet0/0/0 interface
interface GigabitEthernet0/0/0
 description Link to Main Router SF Branch
 ip address 10.3.100.1 255.255.255.252
 no shutdown
 exit

! Configure Serial0/3/0 interface
interface Serial0/3/0
 description Link to ISP:  SF Branch
 ip address 2.2.3.2 255.255.255.252
 no shutdown
 exit

exit
```

# NAT
```
enable
configure terminal

! Access list defining the source addresses to be translated
access-list 1 permit 10.3.0.0 0.0.255.255

! NAT configuration
ip nat pool SF-Pool 2.2.3.2 2.2.3.2 netmask 255.255.255.252
ip nat inside source list 1 pool SF-Pool overload

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
 eigrp router-id 33.33.33.33
 passive-interface default
 no passive-interface GigabitEthernet0/0/0
 network 10.3.100.0 0.0.0.3
 network 2.2.3.0 0.0.0.3
 redistribute static
 no auto-summary

exit
```

enable
configure terminal

interface Tunnel1
 ip address 192.168.2.2 255.255.255.0
 tunnel source Ser0/3/0
 tunnel destination 2.2.1.2
 exit

exit
