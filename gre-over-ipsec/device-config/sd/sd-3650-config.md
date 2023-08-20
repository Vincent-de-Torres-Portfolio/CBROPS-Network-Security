# GRE: SD-3650 - Device Configuration

## VTP and VLAN Configuration

```plaintext
enable
configure terminal

! Set VTP mode and domain
vtp mode server
vtp domain SD

! Create VLANs with names
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

! Configure VLAN interfaces
interface Vlan10
 ip address 10.2.10.2 255.255.255.0
 exit

interface Vlan20
 ip address 10.2.20.1 255.255.255.0
 exit

interface Vlan30
 ip address 10.2.30.1 255.255.254.0
 exit

interface Vlan40
 ip address 10.2.40.1 255.255.254.0
 exit

interface Vlan50
 ip address 10.2.50.1 255.255.252.0
 exit

! Configure interface to Main Router
interface GigabitEthernet1/0/24
 no switchport
 ip address 10.2.100.2 255.255.255.252
 no shutdown
 exit

end
write memory
```

## Access Port Configuration

```plaintext
interface GigabitEthernet1/0/2
 switchport mode access
 switchport access vlan 20
 no shutdown

interface GigabitEthernet1/0/3
 switchport mode access
 switchport access vlan 30
 no shutdown

interface GigabitEthernet1/0/4
 switchport mode access
 switchport access vlan 40
 no shutdown

interface GigabitEthernet1/0/5
 switchport mode access
 switchport access vlan 50
 no shutdown
```

## DHCP Configuration

```plaintext
enable
configure terminal

! VLAN20 DHCP pool
ip dhcp pool VLAN20_POOL
 network 10.2.20.0 255.255.255.0
 default-router 10.2.20.1
 dns-server 10.2.30.100
 exit
ip dhcp excluded-address 10.2.20.1 10.2.20.100

! VLAN30 DHCP pool
ip dhcp pool VLAN30_POOL
 network 10.2.30.0 255.255.254.0
 default-router 10.2.30.1
 dns-server 10.2.30.100
 exit
ip dhcp excluded-address 10.2.30.1 10.2.30.100

! VLAN40 DHCP pool
ip dhcp pool VLAN40_POOL
 network 10.2.40.0 255.255.254.0
 default-router 10.2.40.1
 dns-server 10.2.30.100
 exit
ip dhcp excluded-address 10.2.40.1 10.2.40.100

! VLAN50 DHCP pool
ip dhcp pool VLAN50_POOL
 network 10.2.50.0 255.255.252.0
 default-router 10.2.50.1
 dns-server 10.2.30.100
 exit
ip dhcp excluded-address 10.2.50.1 10.2.50.100

end
copy running-config startup-config
```

## EIGRP Routing Configuration

```plaintext
enable
configure terminal

! Enable IP routing
ip routing

! Configure EIGRP
router eigrp 100
 router-id 1.1.1.1
 passive-interface default
 no auto-summary
 no passive-interface GigabitEthernet1/0/24
 network 10.2.100.0
 network 10.2.10.0
 network 10.2.20.0
 network 10.2.30.0
 network 10.2.40.0
 network 10.2.50.0
 exit

end
```