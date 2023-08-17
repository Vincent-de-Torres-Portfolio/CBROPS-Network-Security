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