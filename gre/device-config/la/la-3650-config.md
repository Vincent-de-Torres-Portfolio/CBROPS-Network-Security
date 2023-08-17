```

enable
configure terminal

! Disable DNS lookup and set hostname
no ip domain-lookup
hostname LA_3650

! Create admin user
username admin secret cisco

! Configure console settings
line console 0
 logging synchronous
 exit

! Configure domain name and generate RSA key
conf t
 ip domain-name la.synapsetechnologies.com
 crypto key generate rsa general-keys modulus 1024

! Set banner message and enable secret
banner motd $Welcome to HQ Branch.$
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

VTP mode server
vtp domain LA

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
 ip address 10.1.10.1 255.255.255.0
 exit

interface Vlan20
 ip address 10.1.20.1 255.255.255.0
 exit

interface Vlan30
 ip address 10.1.30.1 255.255.254.0
 exit

interface Vlan40
 ip address 10.1.40.1 255.255.254.0
 exit

interface Vlan50
 ip address 10.1.50.1 255.255.252.0
 exit

interface GigabitEthernet1/0/24
 no switchport
 ip address 10.1.100.2 255.255.255.252
 no shutdown
 exit

end

write memory

interface Gigabit 3/0
switchport mode acess
switchport access V20
no shutdown


interface Gigabit 3/1
switchport mode acess
switchport access V30
no shutdown


interface Gigabit 3/2
switchport mode acess
switchport access V40
no shutdown

interface Gigabit 3/3
switchport mode acess
switchport access V50
no shutdown

enable
configure terminal

ip routing 
router eigrp 1
 router-id 1.1.1.1
 passive-interface default
 no auto-summary
 no passive-interface GigabitEthernet1/0/24
 no passive-interface GigabitEthernet1/1/1
 network 10.100.0.14 0.0.0.3
 network 10.100.0.10 0.0.0.3
 network 10.2.0.0 0.0.255.255
 exit

end
