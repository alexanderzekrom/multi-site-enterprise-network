# Branch Routers Configuration

## 🏬 BRANCH 1 (R3) CONFIGURATION

### Interface Configuration
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

### Interface Configuration
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

## 🌐 BRANCH NETWORK DESIGN

### Branch 1 Network Details
| Item | Configuration |
|------|---------------|
| **Router ID** | 3.3.3.3 |
| **Local Network** | 10.1.10.0/24 |
| **WAN Link** | 10.0.1.0/30 |
| **OSPF Areas** | Local: Area 1, WAN: Area 0 |
| **DHCP Range** | 10.1.10.11-10.1.10.254 |

### Branch 2 Network Details
| Item | Configuration |
|------|---------------|
| **Router ID** | 4.4.4.4 |
| **Local Network** | 10.2.10.0/24 |
| **WAN Link** | 10.0.2.0/30 |
| **OSPF Areas** | Local: Area 2, WAN: Area 0 |
| **DHCP Range** | 10.2.10.11-10.2.10.254 |

---

## 🛣️ OSPF MULTI-AREA DESIGN

### Area Distribution Strategy
```
Area 0 (Backbone)
    ├── 10.0.1.0/30 (HQ-Branch1)
    ├── 10.0.2.0/30 (HQ-Branch2)
    ├── 10.0.10.0/24 (HQ-VLAN10)
    ├── 10.0.20.0/24 (HQ-VLAN20)
    └── 10.0.30.0/24 (HQ-VLAN30)

Area 1 (Branch 1)
    └── 10.1.10.0/24 (Branch1 LAN)

Area 2 (Branch 2)
    └── 10.2.10.0/24 (Branch2 LAN)
```

### OSPF Benefits in Multi-Area Design
- **Scalability**: Each area maintains its own LSDB
- **Route Summarization**: Reduced routing table sizes
- **Fault Isolation**: Issues contained within areas
- **Flexibility**: Easy to add new branches as new areas

---

## 📊 TRAFFIC FLOW PATTERNS

### Branch 1 Traffic Flow
1. **Local Traffic**: Stays within 10.1.10.0/24
2. **HQ Communication**: Via 10.0.1.0/30 link
3. **Internet Access**: Through HQ NAT
4. **Branch 2 Communication**: Via HQ as transit

### Branch 2 Traffic Flow
1. **Local Traffic**: Stays within 10.2.10.0/24
2. **HQ Communication**: Via 10.0.2.0/30 link
3. **Internet Access**: Through HQ NAT
4. **Branch 1 Communication**: Via HQ as transit

---

## 🔍 BRANCH VERIFICATION COMMANDS

### Interface Status (Both Branches)
```bash
show ip interface brief
show interfaces GigabitEthernet0/0
show interfaces GigabitEthernet0/1
```

### OSPF Verification (Both Branches)
```bash
show ip ospf neighbor
show ip ospf database
show ip ospf interface brief
show ip route ospf
```

### DHCP Verification (Both Branches)
```bash
show ip dhcp binding
show ip dhcp conflict
show ip dhcp server statistics
```

### Connectivity Testing
```bash
ping 10.0.1.1  ! Test HQ link from Branch 1
ping 10.0.2.1  ! Test HQ link from Branch 2
ping 8.8.8.8   ! Test internet access
traceroute 10.0.10.1  ! Test path to HQ VLANs
```

---

## 📋 BRANCH ROUTERS CHECKLIST

### Branch 1 (R3) Configuration
- [ ] GigabitEthernet0/0 configured (10.0.1.2/30)
- [ ] GigabitEthernet0/1 configured (10.1.10.1/24)
- [ ] DHCP exclusions configured (10.1.10.1-10)
- [ ] DHCP pool BRANCH1 created
- [ ] OSPF process 1 enabled
- [ ] Router ID set to 3.3.3.3
- [ ] Local network in Area 1
- [ ] WAN link in Area 0
- [ ] OSPF neighbor relationship with HQ

### Branch 2 (R4) Configuration
- [ ] GigabitEthernet0/0 configured (10.0.2.2/30)
- [ ] GigabitEthernet0/1 configured (10.2.10.1/24)
- [ ] DHCP exclusions configured (10.2.10.1-10)
- [ ] DHCP pool BRANCH2 created
- [ ] OSPF process 1 enabled
- [ ] Router ID set to 4.4.4.4
- [ ] Local network in Area 2
- [ ] WAN link in Area 0
- [ ] OSPF neighbor relationship with HQ

### Inter-Branch Connectivity
- [ ] Branch 1 can reach HQ networks
- [ ] Branch 2 can reach HQ networks
- [ ] Branch 1 can reach Branch 2 via HQ
- [ ] Both branches have internet access
- [ ] DHCP clients receiving addresses
- [ ] OSPF routing tables converged

### Service Verification
- [ ] Local DHCP services functional
- [ ] Inter-area routing working
- [ ] Default route pointing to HQ
- [ ] DNS resolution working
- [ ] Ping connectivity to all networks

---

## 🚀 BRANCH NETWORK EXPANSION

### Adding New Branches
1. **Assign new OSPF area** (Area 3, Area 4, etc.)
2. **Configure WAN link** to HQ
3. **Set up local DHCP** service
4. **Advertise networks** in appropriate areas
5. **Test connectivity** and routing

### Branch Network Scaling Considerations
- **OSPF Area Design**: Keep areas manageable
- **Address Planning**: Reserve space for growth
- **Redundancy**: Consider backup links to HQ
- **Security**: Apply consistent security policies
- **Monitoring**: Implement branch network monitoring

---

*Document: 04 - Branch Routers Configuration*
