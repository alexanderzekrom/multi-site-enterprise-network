# Switches Configuration Guide

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

## 🏢 HQ SWITCH DESIGN

### VLAN Distribution Strategy
| VLAN | Name | Purpose | Port Range |
|------|------|---------|------------|
| 10 | IT | IT Department | fa0/1-8 |
| 20 | SALES | Sales Department | fa0/9-16 |
| 30 | ADMIN | Admin Department | fa0/17-24 |

### Trunk Configuration (if applicable)
```bash
interface GigabitEthernet0/1
 switchport mode trunk
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30
```

---

## 🔐 PORT SECURITY IMPLEMENTATION

### Port Security Configuration (All Switches)
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

### Advanced Port Security Options
```bash
! Optional: Configure aging for sticky MAC addresses
interface range FastEthernet0/1-24
 switchport port-security aging time 120
 switchport port-security aging type inactivity
```

---

## 🌐 SWITCH NETWORK DESIGN

### HQ Switch Configuration
```bash
! Basic switch setup
hostname HQ-SW1
enable secret 12345
service password-encryption

! Management interface
interface Vlan1
 ip address 192.168.1.2 255.255.255.0
 no shutdown

! Default gateway
ip default-gateway 192.168.1.1

! VLAN creation
vlan 10
 name IT_DEPARTMENT
vlan 20
 name SALES_DEPARTMENT
vlan 30
 name ADMIN_DEPARTMENT

! Port assignments
interface range FastEthernet0/1-8
 switchport mode access
 switchport access vlan 10
 switchport port-security
 switchport port-security maximum 3
 switchport port-security violation restrict
 switchport port-security mac-address sticky

interface range FastEthernet0/9-16
 switchport mode access
 switchport access vlan 20
 switchport port-security
 switchport port-security maximum 3
 switchport port-security violation restrict
 switchport port-security mac-address sticky

interface range FastEthernet0/17-24
 switchport mode access
 switchport access vlan 30
 switchport port-security
 switchport port-security maximum 3
 switchport port-security violation restrict
 switchport port-security mac-address sticky
```

### Branch Switch Configuration (Simplified)
```bash
! Branch 1 Switch
hostname BRANCH1-SW1
enable secret 12345
service password-encryption

! Management interface
interface Vlan1
 ip address 192.168.2.2 255.255.255.0
 no shutdown

! Default gateway
ip default-gateway 192.168.2.1

! All ports in single VLAN (simplified branch design)
interface range FastEthernet0/1-24
 switchport mode access
 switchport access vlan 1
 switchport port-security
 switchport port-security maximum 3
 switchport port-security violation restrict
 switchport port-security mac-address sticky
```

---

## 📊 SWITCH VERIFICATION COMMANDS

### VLAN Verification
```bash
show vlan brief
show vlan id 10
show interfaces trunk
show interfaces status
```

### Port Security Verification
```bash
show port-security
show port-security interface FastEthernet0/1
show port-security address
show port-security violation
```

### MAC Address Table
```bash
show mac address-table
show mac address-table address [mac-address]
show mac address-table dynamic
show mac address-table static
```

### Interface Status
```bash
show interfaces status
show interfaces FastEthernet0/1
show ip interface brief
```

### SSH Configuration Verification
```bash
show ip ssh
show run | include ssh
show run | include crypto key
```

---

## 🔧 SWITCH TROUBLESHOOTING

### Common Issues and Solutions

#### VLAN Issues
```bash
! Problem: Device can't communicate with other VLANs
show vlan brief
show interfaces trunk
show running-config interface [interface]

! Solution: Verify VLAN membership and trunk configuration
```

#### Port Security Issues
```bash
! Problem: Legitimate device blocked by port security
show port-security interface [interface]
show port-security address

! Solution: Clear sticky MAC addresses or adjust maximum
```

#### Connectivity Issues
```bash
! Problem: No connectivity to default gateway
show ip interface brief
ping [gateway-ip]
show running-config interface Vlan1
```

---

## 📋 SWITCH CONFIGURATION CHECKLIST

### Basic Switch Setup
- [ ] Hostname configured
- [ ] Enable secrets set
- [ ] Password encryption enabled
- [ ] Management IP address configured
- [ ] Default gateway configured

### VLAN Configuration (HQ Switches)
- [ ] VLAN 10 created (IT)
- [ ] VLAN 20 created (Sales)
- [ ] VLAN 30 created (Admin)
- [ ] Ports assigned to correct VLANs
- [ ] Trunk ports configured (if needed)

### Port Security Implementation
- [ ] Port security enabled on all access ports
- [ ] Maximum MAC addresses set (recommended: 2-3)
- [ ] Violation mode set to restrict
- [ ] Sticky MAC learning enabled
- [ ] Port security aging configured (optional)

### SSH Management Setup
- [ ] IP domain name configured
- [ ] RSA crypto keys generated
- [ ] SSH enabled on VTY lines
- [ ] Local user accounts created
- [ ] SSH access tested

### Verification and Testing
- [ ] VLAN membership verified
- [ ] Port security status checked
- [ ] MAC address table populated
- [ ] SSH connectivity tested
- [ ] Inter-VLAN routing verified

---

## 🚀 SWITCH NETWORK BEST PRACTICES

### Security Recommendations
- **Enable port security** on all access ports
- **Use SSH only** for management access
- **Disable unused ports** to prevent unauthorized access
- **Implement storm control** to prevent broadcast storms
- **Configure BPDU guard** on edge ports

### Performance Optimization
- **Use appropriate port speeds** and duplex settings
- **Configure QoS** for voice/video traffic
- **Implement link aggregation** for high-bandwidth needs
- **Monitor port utilization** regularly

### Maintenance Procedures
- **Regular backup** of switch configurations
- **Document port assignments** and device connections
- **Monitor MAC address tables** for unknown devices
- **Review port security violations** regularly
- **Update switch firmware** as needed

---

## 📈 SCALING SWITCH NETWORKS

### Adding New Switches
1. **Configure basic settings** (hostname, passwords)
2. **Set up management access** (IP, SSH)
3. **Create necessary VLANs** (consistent across switches)
4. **Configure trunk links** between switches
5. **Apply port security** consistently
6. **Test connectivity** thoroughly

### VLAN Design Considerations
- **Keep VLAN count manageable** (recommended: < 20 per switch)
- **Use consistent naming conventions**
- **Document VLAN purposes** and assignments
- **Plan for future growth** in VLAN numbering

---

*Document: 05 - Switches Configuration*
