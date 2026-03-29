# Network Troubleshooting Guide

## 🔍 COMPREHENSIVE TROUBLESHOOTING COMMANDS

### Routing Issues
```bash
show ip route
show ip ospf neighbor
show ip ospf database
show ip protocols
show running-config | section router ospf
```

### DHCP Problems
```bash
show ip dhcp binding
show ip dhcp conflict
show ip dhcp server statistics
show running-config | section ip dhcp
debug ip dhcp server events
```

### Security Issues
```bash
show access-lists
show port-security interface FastEthernet0/1
show port-security address
show mac address-table
show running-config | include access-list
```

### Connectivity Issues
```bash
show ip interface brief
show interfaces status
show vlan brief
ping [ip-address]
traceroute [ip-address]
show cdp neighbors
```

### Internet/NAT Issues
```bash
show ip nat translations
show ip nat statistics
show running-config | include nat
debug ip nat
```

---

## 🚨 COMMON PROBLEMS & SOLUTIONS

### Problem 1: Inter-VLAN Routing Not Working
**Symptoms**: Devices in different VLANs cannot communicate

**Troubleshooting Steps**:
```bash
! 1. Check router sub-interfaces
show running-config interface GigabitEthernet0/0.10
show running-config interface GigabitEthernet0/0.20
show running-config interface GigabitEthernet0/0.30

! 2. Verify VLAN configuration on switch
show vlan brief
show interfaces trunk

! 3. Check routing table
show ip route
show ip route connected

! 4. Test connectivity
ping 10.0.10.1  ! From VLAN 10 device
ping 10.0.20.1  ! From VLAN 20 device
```

**Common Solutions**:
- Missing encapsulation on sub-interfaces
- Incorrect VLAN assignments on switch ports
- Router sub-interface shutdown
- Missing IP addresses on sub-interfaces

---

### Problem 2: OSPF Neighbors Not Forming
**Symptoms**: OSPF adjacency stuck in INIT or DOWN state

**Troubleshooting Steps**:
```bash
! 1. Check OSPF configuration
show running-config | section router ospf
show ip ospf interface brief

! 2. Verify interface connectivity
ping [neighbor-ip]
show ip interface brief

! 3. Check OSPF timers
show ip ospf interface [interface]
show ip ospf neighbor detail

! 4. Verify network statements
show running-config | include network
```

**Common Solutions**:
- Mismatched OSPF network statements
- Incorrect subnet masks in network statements
- Interface authentication mismatch
- OSPF process not running on one router

---

### Problem 3: DHCP Clients Not Getting IP Addresses
**Symptoms**: Devices show 169.254.x.x or no IP address

**Troubleshooting Steps**:
```bash
! 1. Check DHCP configuration
show running-config | section ip dhcp
show ip dhcp binding
show ip dhcp conflict

! 2. Verify DHCP exclusions
show running-config | include excluded-address

! 3. Check DHCP pool status
show ip dhcp pool
show ip dhcp server statistics

! 4. Test DHCP relay (if applicable)
debug ip dhcp server events
debug ip dhcp server packet
```

**Common Solutions**:
- DHCP pool exhausted
- Incorrect network statement in DHCP pool
- DHCP conflicts not cleared
- DHCP service not enabled

---

### Problem 4: Internet Access Not Working
**Symptoms**: Internal network works, no internet connectivity

**Troubleshooting Steps**:
```bash
! 1. Check NAT configuration
show running-config | include nat
show ip nat translations
show ip nat statistics

! 2. Verify default route
show ip route
show running-config | include ip route

! 3. Test internet connectivity
ping 8.8.8.8
traceroute 8.8.8.8

! 4. Check ACLs blocking NAT
show access-lists
show run | include access-list
```

**Common Solutions**:
- Missing NAT statement
- Incorrect ACL for NAT
- Missing default route
- ACL blocking outbound traffic

---

### Problem 5: Port Security Violations
**Symptoms**: Legitimate devices cannot connect, port security violations

**Troubleshooting Steps**:
```bash
! 1. Check port security status
show port-security
show port-security interface [interface]
show port-security address

! 2. Clear violations if needed
clear port-security interface [interface]
clear port-security all

! 3. Adjust port security settings
interface [interface]
 switchport port-security maximum 5
 switchport port-security violation shutdown
```

**Common Solutions**:
- Port security maximum too low
- Sticky MAC addresses not cleared after device changes
- Violation mode too restrictive
- MAC address table full

---

## 🔧 ADVANCED TROUBLESHOOTING

### Layer 1 Issues (Physical)
```bash
! Check interface status
show interfaces status
show interfaces [interface] counters

! Check for errors
show interfaces [interface]
show logging | include [interface]
```

### Layer 2 Issues (Data Link)
```bash
! Check VLAN assignments
show vlan brief
show mac address-table

! Check trunk configuration
show interfaces trunk
show spanning-tree
```

### Layer 3 Issues (Network)
```bash
! Check IP configuration
show ip interface brief
show running-config | include ip address

! Check routing
show ip route
show ip protocols
```

### Layer 4+ Issues (Transport/Application)
```bash
! Check ACLs
show access-lists
show ip access-lists

! Check NAT
show ip nat translations
show ip nat statistics
```

---

## 📋 SYSTEMATIC TROUBLESHOOTING METHODOLOGY

### Step 1: Define the Problem
- **What is not working?** (Be specific)
- **When did it stop working?**
- **What changed recently?**
- **Who is affected?** (Single user, department, entire network)

### Step 2: Gather Information
- **Collect symptoms** from multiple users
- **Check logs** for error messages
- **Verify recent changes** to configuration
- **Document current state** of the network

### Step 3: Formulate Hypothesis
- **Identify likely causes** based on symptoms
- **Prioritize most probable causes**
- **Consider recent changes** as potential factors

### Step 4: Test Hypothesis
- **Isolate variables** by testing one change at a time
- **Use ping/traceroute** to test connectivity
- **Verify configuration** against working examples
- **Document results** of each test

### Step 5: Implement Solution
- **Apply the fix** to resolve the issue
- **Test thoroughly** to confirm resolution
- **Document the solution** for future reference
- **Update documentation** if needed

### Step 6: Verify and Monitor
- **Confirm the fix** works for all affected users
- **Monitor for recurrence** of the problem
- **Update monitoring systems** if needed
- **Schedule follow-up** to ensure stability

---

## 🚨 EMERGENCY TROUBLESHOOTING

### Network Outage Response
1. **Immediate Assessment**: Determine scope and impact
2. **Quick Wins**: Check obvious issues (cables, power)
3. **Recent Changes**: Roll back recent configuration changes
4. **Backup Restoration**: Use last known good configuration
5. **Vendor Support**: Contact Cisco TAC if needed

### Critical Command References
```bash
! Save current config before changes
write memory
copy running-config startup-config

! Roll back to startup config
copy startup-config running-config
reload

! Clear specific configurations
no router ospf 1
no ip nat inside source list 1 interface GigabitEthernet0/1 overload
```

---

## 📊 MONITORING AND PREVENTION

### Proactive Monitoring Commands
```bash
! Monitor OSPF neighbors
show ip ospf neighbor | exclude FULL

! Monitor interface errors
show interfaces | include line protocol|errors

! Monitor CPU utilization
show processes cpu sorted

! Monitor memory usage
show processes memory sorted
```

### Automated Monitoring Setup
```bash
! Set up logging
logging buffered 100000
logging trap debugging
logging 192.168.1.100  ! Syslog server

! Set up SNMP (if needed)
snmp-server community public RO
snmp-server enable traps
```

---

## 📚 TROUBLESHOOTING REFERENCE

### Quick Command Reference
| Issue | Primary Commands |
|-------|------------------|
| No connectivity | `ping`, `traceroute`, `show ip interface brief` |
| Routing problems | `show ip route`, `show ip ospf neighbor` |
| DHCP issues | `show ip dhcp binding`, `debug ip dhcp server` |
| Security problems | `show access-lists`, `show port-security` |
| Performance issues | `show processes cpu`, `show interfaces` |

### Common Error Messages
- **"Interface is down, line protocol is down"**: Physical layer issue
- **"OSPF: Neighbor not found"**: OSPF configuration problem
- **"DHCP: No free addresses"**: DHCP pool exhausted
- **"Port security violation"**: Unauthorized device connected

---

*Document: 06 - Troubleshooting Guide*
