## COnfigure interfaces:
```CiscoIOS
enable
configure terminal

! Configure Serial0/0/0 interface
interface Serial0/0/0
 description Link to ISP
 ip address 2.2.1.1 255.255.255.252
 no shutdown
 exit

! Configure Serial0/0/1 interface
interface Serial0/0/1
 description Another Link to ISP
 ip address 2.2.2.1 255.255.255.252
 no shutdown
 exit

! Configure Serial0/1/0 interface
interface Serial0/1/0
 description Internal Network Link
 ip address 2.2.3.1 255.255.255.252
 no shutdown
 exit

! Repeat the above steps for other interfaces

exit
 
```