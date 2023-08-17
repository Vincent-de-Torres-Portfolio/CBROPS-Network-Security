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