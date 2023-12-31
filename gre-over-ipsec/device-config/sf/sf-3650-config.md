# GRE-over-IPSec: SF-3650 &mdash; Device Configuration

## Initial Settings

```

enable
configure terminal

! Disable DNS lookup and set hostname
no ip domain-lookup
hostname SF-3650

! Create admin user
username admin secret cisco

! Configure console settings
line console 0
 logging synchronous
 exit

! Configure domain name and generate RSA key
conf t
 ip domain-name SF.synapsetechnologies.com
 crypto key generate rsa general-keys modulus 1024

! Set banner message and enable secret
banner motd $Welcome to SF Branch.$
 enable secret cisco

! Configure console password
line console 0
 password cisco
 login
 exit

! Configure SSH for remote management
line vty 0 4
 login local
 transport input ssh
 ip ssh version 2
 
 ```

---
```
VTP mode server
vtp domain SF

vlan 10
 name NATIVE
vlan 20
 name MGT
vlan 30
 name SRV
vlan 40
 name IT
vlan 50
 name INT


interface Vlan10
no shutdown
 ip address 10.3.10.1 255.255.255.0
 exit

interface Vlan20
no shutdown

 ip address 10.3.20.1 255.255.255.0
 exit

interface Vlan30
no shutdown
 ip address 10.3.30.1 255.255.254.0
 exit

interface Vlan40
no shutdown
 ip address 10.3.40.1 255.255.254.0
 exit

interface Vlan50
no shutdown
 ip address 10.3.50.1 255.255.252.0
 exit

interface GigabitEthernet0/0
 no switchport
 ip address 10.3.100.2 255.255.255.252
 no shutdown
 exit

end

write memory
```
```
interface Gigabit 0/1
switchport mode access
switchport access Vlan 20
no shutdown

interface Gigabit 0/2
switchport mode access
switchport access Vlan 30
no shutdown


interface Gigabit 0/3
switchport mode access
switchport access Vlan 40
no shutdown

interface Gigabit 1/0
switchport mode access
switchport access Vlan 50
no shutdown


interface range G 1/0/1, GigabitEthernet 1/0/6-23
!Disable DTP
switchport mode acceess
!set as accessport of unused vlan
switchport access vlan 9
shutdown


```

## DHCP
```
enable
configure terminal

ip dhcp pool VLAN20_POOL
 network 10.3.20.0 255.255.255.0
 default-router 10.3.20.1
 dns-server 10.3.30.100
 exit
ip dhcp excluded-address 10.3.20.1 10.3.20.100

ip dhcp pool VLAN30_POOL
 network 10.3.30.0 255.255.254.0
 default-router 10.3.30.1
 dns-server 10.3.30.100
 exit
ip dhcp excluded-address 10.3.30.1 10.3.30.100

ip dhcp pool VLAN40_POOL
 network 10.3.40.0 255.255.254.0
 default-router 10.3.40.1
 dns-server 10.3.30.100
 exit
ip dhcp excluded-address 10.3.40.1 10.3.40.100

ip dhcp pool VLAN50_POOL
 network 10.3.50.0 255.255.252.0
 default-router 10.3.50.1
 dns-server 10.3.30.100
 exit
ip dhcp excluded-address 10.3.50.1 10.3.50.100

end
copy running-config startup-config
```

```
enable
configure terminal

ip routing
ip route 0.0.0.0 0.0.0.0 10.3.100.1

router eigrp 100
 router-id 3.3.3.3
 passive-interface default
 no auto-summary
 no passive-interface GigabitEthernet0/0
 network 10.3.100.0
 network 10.3.10.0
 network 10.3.20.0
 network 10.3.30.0
 network 10.3.40.0
 network 10.3.50.0
 exit

end
```
