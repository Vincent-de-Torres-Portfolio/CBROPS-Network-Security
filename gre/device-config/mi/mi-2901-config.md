## Interface
```
enable
configure terminal

! Configure GigabitEthernet0/0/0 interface
interface GigabitEthernet0/0/0
 description Link to Main Router MI Branch
 ip address 10.101.100.1 255.255.255.252
 no shutdown
 exit

! Configure Serial0/3/0 interface
interface Serial0/3/0
 description Link to ISP Router MI Branch
 ip address 4.4.1.2 255.255.255.252
 no shutdown
 exit

exit
```

# EIGRP

```
enable
configure terminal

router eigrp 1
 eigrp router-id 111.111.111.111
 passive-interface default
 no passive-interface GigabitEthernet0/0/0
 network 10.101.100.0 0.0.0.3
 network 4.4.1.0 0.0.0.3
 redistribute static
 no auto-summary

exit

```


enable
configure terminal

! Access list defining the source addresses to be translated
access-list 1 permit 10.101.0.0 0.0.255.255

! NAT configuration
ip nat pool MI-Pool 4.4.1.2 4.4.1.2 netmask 255.255.255.252
ip nat inside source list 1 pool MI-Pool overload

! Apply NAT to the interface
interface GigabitEthernet0/0/0
 ip nat inside
 exit

interface Serial0/3/0
 ip nat outside
 exit


 ip route 0.0.0.0 0.0.0.0 4.4.1.1




enable
configure terminal

router eigrp 1
 eigrp router-id 111.111.111.111
 passive-interface default
 no passive-interface GigabitEthernet0/0/0
 network 10.101.100.0 0.0.0.3
 network 4.4.1.0 0.0.0.3
 no auto-summary

exit