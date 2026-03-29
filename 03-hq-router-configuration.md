# HQ Router (R1) Configuration

## 🏢 HEADQUARTERS (R1) CONFIGURATION

### VLAN Interfaces (Router-on-a-Stick)
```bash
interface GigabitEthernet0/0
 no shutdown

interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 10.0.10.1 255.255.255.0

interface GigabitEthernet0/0.20
 encapsulation dot1Q 20
 ip address 10.0.20.1 255.255.255.0

interface GigabitEthernet0/0.30
 encapsulation dot1Q 30
 ip address 10.0.30.1 255.255.255.0
```

### WAN & Internet Setup
```bash
interface GigabitEthernet0/1
 ip address 200.1.1.2 255.255.255.252
 ip nat outside
 no shutdown

interface GigabitEthernet0/0
 ip nat inside

access-list 1 permit 10.0.0.0 0.255.255.255
ip nat inside source list 1 interface GigabitEthernet0/1 overload
```

### Branch Office Connections
```bash
interface GigabitEthernet0/2
 ip address 10.0.1.1 255.255.255.252
 no shutdown

interface GigabitEthernet0/3
 ip address 10.0.2.1 255.255.255.252
 no shutdown
```

---

## 🌐 DHCP SERVICE FOR HQ

### DHCP Exclusions
```bash
ip dhcp excluded-address 10.0.10.1 10.0.10.10
ip dhcp excluded-address 10.0.20.1 10.0.20.10
ip dhcp excluded-address 10.0.30.1 10.0.30.10
```

### DHCP Pools
```bash
ip dhcp pool IT
 network 10.0.10.0 255.255.255.0
 default-router 10.0.10.1
 dns-server 8.8.8.8

ip dhcp pool SALES
 network 10.0.20.0 255.255.255.0
 default-router 10.0.20.1
 dns-server 8.8.8.8

ip dhcp pool ADMIN
 network 10.0.30.0 255.255.255.0
 default-router 10.0.30.1
 dns-server 8.8.8.8
```

---

## 🛰️ OSPF ROUTING CONFIGURATION

### OSPF Process Setup
```bash
router ospf 1
 router-id 1.1.1.1
 network 10.0.10.0 0.0.0.255 area 0
 network 10.0.20.0 0.0.0.255 area 0
 network 10.0.30.0 0.0.0.255 area 0
 network 10.0.1.0 0.0.0.3 area 0
 network 10.0.2.0 0.0.0.3 area 0
```

### OSPF Network Distribution
| Network | Area | Purpose |
|---------|------|---------|
| 10.0.10.0/24 | Area 0 | IT VLAN |
| 10.0.20.0/24 | Area 0 | Sales VLAN |
| 10.0.30.0/24 | Area 0 | Admin VLAN |
| 10.0.1.0/30 | Area 0 | Link to Branch 1 |
| 10.0.2.0/30 | Area 0 | Link to Branch 2 |

---

## 🔐 ACCESS CONTROL LISTS

### SSH Access Restriction
```bash
! SSH access restricted to IT VLAN
access-list 10 permit 10.0.10.0 0.0.0.255

line vty 0 4
 access-class 10 in
 transport input ssh
 login local
```

### Inter-VLAN Security
```bash
! Block traffic between Sales and Admin
access-list 101 deny ip 10.0.20.0 0.0.0.255 10.0.30.0 0.0.0.255
access-list 101 deny ip 10.0.30.0 0.0.0.255 10.0.20.0 0.0.0.255
access-list 101 permit ip any any

interface GigabitEthernet0/0.20
 ip access-group 101 in

interface GigabitEthernet0/0.30
 ip access-group 101 in
```

---

## 📊 TRAFFIC FLOW DESIGN

### Routing Responsibilities
1. **Inter-VLAN Routing**: Handles all HQ VLAN routing
2. **Branch Communication**: Routes traffic to/from branch offices
3. **Internet Gateway**: Provides NAT for all locations
4. **Management Access**: Controls SSH access to network devices

### Network Segmentation
- **IT VLAN (10)**: Full network access, management privileges
- **Sales VLAN (20)**: Limited access, blocked from Admin
- **Admin VLAN (30)**: Limited access, blocked from Sales

---

## 🔍 VERIFICATION COMMANDS

### Interface Status
```bash
show ip interface brief
show interfaces GigabitEthernet0/0
show interfaces GigabitEthernet0/1
show interfaces GigabitEthernet0/2
show interfaces GigabitEthernet0/3
```

### Routing Verification
```bash
show ip route
show ip ospf neighbor
show ip ospf database
show ip protocols
```

### DHCP Verification
```bash
show ip dhcp binding
show ip dhcp conflict
show ip dhcp server statistics
```

### NAT Verification
```bash
show ip nat translations
show ip nat statistics
show run | include nat
```

### ACL Verification
```bash
show access-lists
show ip access-lists
show run | section access-list
```

---

## 📋 HQ ROUTER CHECKLIST

### Interface Configuration
- [ ] GigabitEthernet0/0 configured for VLAN trunking
- [ ] GigabitEthernet0/1 configured for internet/WAN
- [ ] GigabitEthernet0/2 configured for Branch 1 link
- [ ] GigabitEthernet0/3 configured for Branch 2 link
- [ ] All interfaces in "up/up" status

### VLAN Configuration
- [ ] Sub-interface GigabitEthernet0/0.10 created (VLAN 10)
- [ ] Sub-interface GigabitEthernet0/0.20 created (VLAN 20)
- [ ] Sub-interface GigabitEthernet0/0.30 created (VLAN 30)
- [ ] All VLAN sub-interfaces have correct IP addresses
- [ ] VLAN encapsulation configured correctly

### DHCP Configuration
- [ ] DHCP exclusions configured for all VLANs
- [ ] IT DHCP pool created with correct settings
- [ ] Sales DHCP pool created with correct settings
- [ ] Admin DHCP pool created with correct settings
- [ ] DHCP bindings showing active clients

### OSPF Configuration
- [ ] OSPF process 1 enabled
- [ ] Router ID set to 1.1.1.1
- [ ] All networks advertised in correct areas
- [ ] OSPF neighbors established with branch routers
- [ ] OSPF routes present in routing table

### NAT Configuration
- [ ] Inside/outside interfaces marked
- [ ] NAT ACL configured correctly
- [ ] NAT overload configured
- [ ] NAT translations working for internet access

### Security Configuration
- [ ] SSH access ACL applied to VTY lines
- [ ] Inter-VLAN ACLs configured and applied
- [ ] ACL rules permitting required traffic
- [ ] Management access restricted to IT VLAN

---

*Document: 03 - HQ Router Configuration*
