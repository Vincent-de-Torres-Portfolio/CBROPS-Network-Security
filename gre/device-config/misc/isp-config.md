# ISP Router
```
enable
configure terminal

! Configure interface G0/0/0 to LA
interface GigabitEthernet0/0/0
 description 'to LA'
 ip address 2.2.1.1 255.255.255.252
 no shutdown
 exit

! Configure interface G0/1/0 to SD
interface GigabitEthernet0/1/0
 description 'to SD'
 ip address 2.2.2.1 255.255.255.252
 no shutdown
 exit

! Configure interface G0/2/0 to SF
interface GigabitEthernet0/2/0
 description to SF
 ip address 2.2.3.1 255.255.255.252
 no shutdown
 exit

! Configure interface Se0/3/0 to MI
interface Serial0/3/0
 description to MI
 ip address 4.4.1.1 255.255.255.252
 no shutdown
 exit

! Configure interface Se0/3/1 to NY
interface Serial0/3/1
 description to NY
 ip address 4.4.2.2 255.255.255.252
 no shutdown
 exit

exit
```
