# GRE: NY-2901 - Device Configuration
### Interface Configuration

```CiscoIOS
enable
configure terminal

! Configure GigabitEthernet0/0 interface
interface GigabitEthernet0/0
description Link to Main Router NY Branch
ip address 10.200.100.1 255.255.255.252
no shutdown
exit

interface Se0/3/0
description Link to ISP NY Branch
ip address 4.4.2.2 255.255.255.252
no shutdown
exit

exit
```

### EIGRP Configuration

```CiscoIOS
enable
configure terminal

router eigrp 1
eigrp router-id 112.112.112.112
passive-interface default
no passive-interface GigabitEthernet0/0/0
no auto summary
network 172.16.200.0
network 10.200.100.0 


exit
```

### NAT Configuration

```CiscoIOS
enable
configure terminal

! Access list defining the source addresses to be translated
access-list 1 permit 10.200.0.0 0.0.255.255

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
```
## GRE
```
interface Tunnel 200
description GRE Tunnel to MI
ip address 172.16.200.2 255.255.255.252
tunnel source Serial 0/3/0
tunnel destination 4.4.1.2
exit```