# Multi-Site Enterprise Network

## Quick Reference
- **3 Sites**: HQ + 2 Branch Offices
- **Technology**: OSPF, VLANs, NAT, DHCP
- **Security**: ACLs, Port Security, SSH
- **Level**: CCNA/CCNP Enterprise

---

## 🏗️ NETWORK ARCHITECTURE

### Physical Layout
```
Internet
    |
  [R1-HQ]
   / | \
  /  |  \
[R3] [R4] [Switches]
 |    |
Branch1 Branch2
```

### IP Address Summary
| Location | Network | Purpose |
|----------|---------|---------|
| HQ-VLAN10 | 10.0.10.0/24 | IT Department |
| HQ-VLAN20 | 10.0.20.0/24 | Sales Department |
| HQ-VLAN30 | 10.0.30.0/24 | Admin Department |
| Branch1 | 10.1.10.0/24 | Branch Office 1 |
| Branch2 | 10.2.10.0/24 | Branch Office 2 |
| WAN Link 1 | 10.0.1.0/30 | HQ-Branch1 |
| WAN Link 2 | 10.0.2.0/30 | HQ-Branch2 |
| Internet | 200.1.1.2/30 | External Connection |

---

## 🔐 SECURITY SETUP

### Step 1: Base Security (All Devices)
```bash
configure terminal
enable secret 12345
service password-encryption

line console 0
 password 1234
 login

line vty 0 4
 password 1234
 login
 transport input ssh

username admin privilege 15 secret 1234
```

### Step 2: Port Security (All Switches)
```bash
interface range FastEthernet0/1-24
 switchport mode access
 switchport port-security
 switchport port-security maximum 3
 switchport port-security violation restrict
 switchport port-security mac-address sticky
```

---

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

### DHCP Service for HQ
```bash
ip dhcp excluded-address 10.0.10.1 10.0.10.10
ip dhcp excluded-address 10.0.20.1 10.0.20.10
ip dhcp excluded-address 10.0.30.1 10.0.30.10

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

### OSPF Routing
```bash
router ospf 1
 router-id 1.1.1.1
 network 10.0.10.0 0.0.0.255 area 0
 network 10.0.20.0 0.0.0.255 area 0
 network 10.0.30.0 0.0.0.255 area 0
 network 10.0.1.0 0.0.0.3 area 0
 network 10.0.2.0 0.0.0.3 area 0
```

### Access Control Lists
```bash
! SSH access restricted to IT VLAN
access-list 10 permit 10.0.10.0 0.0.0.255
line vty 0 4
 access-class 10 in
 transport input ssh
 login local

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

## 🏬 BRANCH 1 (R3) CONFIGURATION

### Interfaces
```bash
interface GigabitEthernet0/0
 ip address 10.0.1.2 255.255.255.252
 no shutdown

interface GigabitEthernet0/1
 ip address 10.1.10.1 255.255.255.0
 no shutdown
```

### DHCP Service
```bash
ip dhcp excluded-address 10.1.10.1 10.1.10.10

ip dhcp pool BRANCH1
 network 10.1.10.0 255.255.255.0
 default-router 10.1.10.1
 dns-server 8.8.8.8
```

### OSPF Routing
```bash
router ospf 1
 router-id 3.3.3.3
 network 10.1.10.0 0.0.0.255 area 1
 network 10.0.1.0 0.0.0.3 area 0
```

---

## 🏬 BRANCH 2 (R4) CONFIGURATION

### Interfaces
```bash
interface GigabitEthernet0/0
 ip address 10.0.2.2 255.255.255.252
 no shutdown

interface GigabitEthernet0/1
 ip address 10.2.10.1 255.255.255.0
 no shutdown
```

### DHCP Service
```bash
ip dhcp excluded-address 10.2.10.1 10.2.10.10

ip dhcp pool BRANCH2
 network 10.2.10.0 255.255.255.0
 default-router 10.2.10.1
 dns-server 8.8.8.8
```

### OSPF Routing
```bash
router ospf 1
 router-id 4.4.4.4
 network 10.2.10.0 0.0.0.255 area 2
 network 10.0.2.0 0.0.0.3 area 0
```

---

## 🖧 SWITCH CONFIGURATION

### VLAN Setup (HQ Switches)
```bash
vlan 10
 name IT
vlan 20
 name SALES
vlan 30
 name ADMIN
```

### SSH Management
```bash
hostname SWX
ip domain-name enterprise.local
crypto key generate rsa modulus 1024

username admin secret 1234

line vty 0 4
 login local
 transport input ssh
```

### Port Assignment Example
```bash
interface FastEthernet0/1
 switchport mode access
 switchport access vlan 10

interface FastEthernet0/2
 switchport mode access
 switchport access vlan 20

interface FastEthernet0/3
 switchport mode access
 switchport access vlan 30
```

---

## 🔍 TROUBLESHOOTING COMMANDS

### Routing Issues
```bash
show ip route
show ip ospf neighbor
show ip ospf database
show ip protocols
```

### DHCP Problems
```bash
show ip dhcp binding
show ip dhcp conflict
show ip dhcp server statistics
```

### Security Issues
```bash
show access-lists
show port-security interface FastEthernet0/1
show port-security address
show mac address-table
```

### Connectivity Issues
```bash
show ip interface brief
show interfaces status
show vlan brief
ping [ip-address]
traceroute [ip-address]
```

### Internet/NAT Issues
```bash
show ip nat translations
show ip nat statistics
show run | include nat
```

---

## 📋 QUICK CHECKLIST

### Initial Setup
- [ ] Base security configured on all devices
- [ ] Port security enabled on all switch ports
- [ ] VLANs created and assigned correctly
- [ ] Trunk links configured (if applicable)

### Router Configuration
- [ ] Interface IP addresses assigned
- [ ] OSPF routing configured and neighbors up
- [ ] DHCP pools created and exclusions set
- [ ] NAT configured for internet access
- [ ] ACLs applied for security

### Verification
- [ ] Inter-VLAN routing working
- [ ] Branch office connectivity established
- [ ] Internet access functional
- [ ] SSH management access working
- [ ] DHCP clients receiving addresses

---

## 🎯 KEY FEATURES SUMMARY

### What This Network Provides
✅ **Multi-site connectivity** with OSPF routing
✅ **Network segmentation** with VLANs and ACLs
✅ **Internet access** with NAT overload
✅ **Automatic IP assignment** with DHCP
✅ **Secure management** with SSH only
✅ **Port protection** with port security
✅ **Traffic filtering** between departments

### Technologies Demonstrated
- **OSPF Multi-Area Routing** (Areas 0, 1, 2)
- **Router-on-a-Stick** for inter-VLAN routing
- **Network Address Translation** (PAT/NAT overload)
- **Dynamic Host Configuration Protocol** (DHCP)
- **Access Control Lists** (standard and extended)
- **Port Security** with sticky MAC addresses
- **VLAN segmentation** and trunking
- **SSH secure management**

---

## 📈 SKILL LEVEL

### Cisco Certification Level
- **CCNA**: All topics covered
- **CCNP**: Advanced routing and security
- **Enterprise**: Real-world deployment

### Industry Relevance
- **Small/Medium Business**: Perfect fit
- **Enterprise Ready**: Scalable architecture
- **Security Focused**: Defense-in-depth
- **Best Practices**: Industry standards

---

*Documentation optimized for quick reference and practical implementation*
