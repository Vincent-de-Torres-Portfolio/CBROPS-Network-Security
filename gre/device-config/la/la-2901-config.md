## Interface Configuration 

```
enable
configure terminal

! Configure GigabitEthernet0/0/0 interface
interface GigabitEthernet0/0/0
 description Link to Main Router LA Branch
 ip address 10.1.100.1 255.255.255.252
 no shutdown
 exit

! Configure Serial0/3/0 interface
interface Serial0/3/0
 description Link to ISP LA Branch
 ip address 2.2.1.2 255.255.255.252
 no shutdown
 exit

exit
```

# NAT

```
enable
configure terminal

! Access list defining the source addresses to be translated
access-list 1 permit 10.1.0.0 0.0.255.255

! NAT configuration
ip nat pool LA-Pool 2.2.1.2 2.2.1.2 netmask 255.255.255.252
ip nat inside source list 1 pool LA-Pool overload

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
 eigrp router-id 11.11.11.11
 passive-interface default
 no passive-interface GigabitEthernet0/0/0
 network 10.1.100.0 0.0.0.3
 network 2.2.1.0 0.0.0.3
 no auto-summary

exit


```

# GRE
enable
configure terminal

interface Tunnel0
 description GRE Tunnel to NY
 ip address 192.168.1.1 255.255.255.252
 tunnel source Se0/3/0
 tunnel destination 4.4.2.2 
 exit

interface Tunnel1
 description GRE Tunnel to SF
 ip address 192.168.2.1 255.255.255.252
 tunnel source Se0/3/0
 tunnel destination 2.2.3.2
 exit

interface Tunnel2
 description GRE Tunnel to SD
 ip address 192.168.3.1 255.255.255.252
 tunnel source Serial/0/0
 tunnel destination 2.2.2.2
 exit

exit
