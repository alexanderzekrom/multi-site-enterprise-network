# Security Configuration Guide

## 🔐 SECURITY SETUP

### Step 1: Base Security (All Devices)
Applied to all network devices for consistent security baseline.

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

### Security Features Implemented
- **Enable secret encryption** for privileged EXEC access
- **Password encryption** service for all stored passwords
- **Console access protection** with password authentication
- **VTY access control** with SSH transport only
- **Local user database** with privilege level 15 for administrators

---

### Step 2: Port Security (All Switches)
Applied to all access ports across all switches for network protection.

```bash
interface range FastEthernet0/1-24
 switchport mode access
 switchport port-security
 switchport port-security maximum 3
 switchport port-security violation restrict
 switchport port-security mac-address sticky
```

### Port Security Features
- **Maximum 3 MAC addresses** per port
- **Violation mode**: Restrict (blocks unauthorized traffic without shutting down port)
- **Sticky MAC learning**: Automatically learns and saves MAC addresses
- **Access mode enforcement**: Ensures ports operate in access mode only

### Port Security Benefits
- Prevents MAC flooding attacks
- Limits unauthorized device connections
- Provides visibility into connected devices
- Maintains network availability during security events

---

## 🛡️ ADVANCED SECURITY FEATURES

### SSH Management Setup
```bash
hostname SWX
ip domain-name enterprise.local
crypto key generate rsa modulus 1024

username admin secret 1234

line vty 0 4
 login local
 transport input ssh
```

### Access Control Lists

#### SSH Access Restriction (HQ Router)
```bash
! SSH access restricted to IT VLAN only
access-list 10 permit 10.0.10.0 0.0.0.255

line vty 0 4
 access-class 10 in
 transport input ssh
 login local
```

#### Inter-VLAN Security (HQ Router)
```bash
! Block traffic between Sales and Admin departments
access-list 101 deny ip 10.0.20.0 0.0.0.255 10.0.30.0 0.0.0.255
access-list 101 deny ip 10.0.30.0 0.0.0.255 10.0.20.0 0.0.0.255
access-list 101 permit ip any any

interface GigabitEthernet0/0.20
 ip access-group 101 in

interface GigabitEthernet0/0.30
 ip access-group 101 in
```

---

## 🔍 SECURITY VERIFICATION COMMANDS

### Security Issues
```bash
show access-lists
show port-security interface FastEthernet0/1
show port-security address
show mac address-table
show run | include ssh
```

### Port Security Status
```bash
show port-security
show port-security interface
show port-security address
```

### Access List Verification
```bash
show access-lists
show ip access-lists
show run | section access-list
```

---

## 🚨 SECURITY BEST PRACTICES

### Network Security
- **ACL-based traffic filtering** between sensitive VLANs
- **Port security** prevents unauthorized device connections
- **SSH-only management** eliminates clear-text protocols
- **NAT** hides internal network structure

### Access Control
- **Role-based access** with privilege levels
- **VLAN segmentation** isolates departmental traffic
- **Management restrictions** to authorized networks only
- **Password encryption** protects stored credentials

### Monitoring and Enforcement
- **MAC address tracking** for device identification
- **Violation logging** for security incidents
- **Real-time port status** monitoring
- **Automated threat response** via port security

---

## 📋 SECURITY IMPLEMENTATION CHECKLIST

### Initial Security Setup
- [ ] Enable secrets configured on all devices
- [ ] Password encryption enabled
- [ ] Console access secured
- [ ] VTY access limited to SSH
- [ ] Local user accounts created

### Port Security Implementation
- [ ] Port security enabled on all access ports
- [ ] Maximum MAC addresses set (recommended: 2-3)
- [ ] Violation mode set to restrict
- [ ] Sticky MAC learning enabled
- [ ] Default port security verified

### Access Control Implementation
- [ ] SSH access restricted to authorized networks
- [ ] Inter-VLAN ACLs applied where needed
- [ ] ACL rules tested and verified
- [ ] Management access secured

### Ongoing Security Maintenance
- [ ] Regular security audits scheduled
- [ ] MAC address tables reviewed
- [ ] ACL logs monitored
- [ ] Port security violations investigated
- [ ] Configuration backups secured

---

*Document: 02 - Security Configuration*
