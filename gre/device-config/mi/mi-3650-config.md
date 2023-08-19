```
enable
configure terminal

interface Vlan10
 ip address 10.101.10.1 255.255.255.0
 exit

interface Vlan20
 ip address 10.101.20.1 255.255.255.0
 exit

interface Vlan30
 ip address 10.101.30.1 255.255.254.0
 exit

interface Vlan40
 ip address 10.101.40.1 255.255.254.0
 exit

interface Vlan50
 ip address 10.101.50.1 255.255.252.0
 exit

interface Vlan60
 ip address 10.101.60.1 255.255.248.0
 exit

interface Vlan70
 ip address 10.101.70.1 255.255.252.0
 exit

interface GigabitEthernet1/1/1
 no switchport
 ip address 10.101.100.2 255.255.255.252
 no shutdown
 exit

interface GigabitEthernet1/0/24
switchport
shutdown


end
```

# DHCP
```
ip dhcp pool VLAN20_POOL
 network 10.101.20.0 255.255.255.0
 default-router 10.101.20.1
 dns-server 10.101.30.100
 exit
ip dhcp excluded-address 10.101.20.1 10.101.20.100

ip dhcp pool VLAN30_POOL
 network 10.101.30.0 255.255.254.0
 default-router 10.101.30.1
 dns-server 10.101.30.100
 exit
ip dhcp excluded-address 10.101.30.1 10.101.30.100

ip dhcp pool VLAN40_POOL
 network 10.101.40.0 255.255.254.0
 default-router 10.101.40.1
 dns-server 10.101.30.100
 exit
ip dhcp excluded-address 10.101.40.1 10.101.40.100

ip dhcp pool VLAN50_POOL
 network 10.101.50.0 255.255.252.0
 default-router 10.101.50.1
 dns-server 10.101.30.100
 exit
ip dhcp excluded-address 10.101.50.1 10.101.50.100

ip dhcp pool VLAN60_POOL
 network 10.101.60.0 255.255.248.0
 default-router 10.101.60.1
 dns-server 10.101.30.100
 exit
ip dhcp excluded-address 10.101.60.1 10.101.60.100

ip dhcp pool VLAN70_POOL
 network 10.101.70.0 255.255.252.0
 default-router 10.101.70.1
 dns-server 10.101.30.100
 exit
ip dhcp excluded-address 10.101.70.1 10.101.70.100
```


no ip dhcp excluded-address 10.4.20.1 10.4.20.100
no ip dhcp excluded-address 10.4.30.1 10.4.30.100
no ip dhcp excluded-address 10.4.40.1 10.4.40.100
no ip dhcp excluded-address 10.4.50.1 10.4.50.100
no ip dhcp excluded-address 10.4.60.1 10.4.60.100
no ip dhcp excluded-address 10.4.70.1 10.4.70.100


enable
configure terminal

router eigrp 1
 eigrp router-id 101.101.101.101
 passive-interface default
 no passive-interface GigabitEthernet1/1/1
 network 10.101.0.0 0.0.0.255
 redistribute static
 no auto-summary

exit

