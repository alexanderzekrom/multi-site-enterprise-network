# Multi-Site Enterprise Network - Overview

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

## 📋 IMPLEMENTATION CHECKLIST

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

*Document: 01 - Network Overview*
