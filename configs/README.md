# Network Configuration Files

This directory contains ready-to-use configuration files for the multi-site enterprise network.

## 📁 File Structure

```
configs/
├── README.md                 # This file
├── r1-hq-router.txt         # HQ Router (R1) complete config
├── r3-branch1-router.txt    # Branch 1 Router (R3) complete config
├── r4-branch2-router.txt    # Branch 2 Router (R4) complete config
├── hq-switch.txt            # HQ Switch complete config
├── branch1-switch.txt       # Branch 1 Switch complete config
├── branch2-switch.txt       # Branch 2 Switch complete config
└── templates/               # Configuration templates
    ├── base-security.txt
    ├── dhcp-template.txt
    ├── ospf-template.txt
    └── port-security.txt
```

## 🚀 Quick Deployment

### Using These Configurations

1. **Copy and Paste**: Copy the desired configuration into your device CLI
2. **File Transfer**: Use TFTP/FTP to upload to devices
3. **Configuration Merge**: Use `configure replace` or merge mode

### Before You Start
- Review all IP addresses for your environment
- Update passwords and security settings
- Verify interface names match your hardware
- Test in lab environment first

## 📋 Configuration Details

### R1 - HQ Router
- **File**: `r1-hq-router.txt`
- **Features**: OSPF Area 0, DHCP, NAT, Inter-VLAN routing
- **Interfaces**: GigabitEthernet0/0-3

### R3 - Branch 1 Router
- **File**: `r3-branch1-router.txt`
- **Features**: OSPF Area 1, Local DHCP
- **Interfaces**: GigabitEthernet0/0-1

### R4 - Branch 2 Router
- **File**: `r4-branch2-router.txt`
- **Features**: OSPF Area 2, Local DHCP
- **Interfaces**: GigabitEthernet0/0-1

### HQ Switch
- **File**: `hq-switch.txt`
- **Features**: VLANs 10/20/30, Port Security, SSH
- **Ports**: FastEthernet0/1-24

### Branch Switches
- **Files**: `branch1-switch.txt`, `branch2-switch.txt`
- **Features**: Simplified single VLAN, Port Security
- **Ports**: FastEthernet0/1-24

## 🔧 Customization

### IP Address Changes
Find and replace these patterns:
- `10.0.10.0` → Your IT network
- `10.0.20.0` → Your Sales network
- `10.0.30.0` → Your Admin network
- `10.1.10.0` → Your Branch 1 network
- `10.2.10.0` → Your Branch 2 network

### Password Updates
Update these security settings:
- `enable secret 12345` → Your enable secret
- `password 1234` → Your console/VTY password
- `secret 1234` → Your user passwords

### Interface Names
Common interface name variations:
- `GigabitEthernet0/0` → `Gi0/0`, `Gig0/0`
- `FastEthernet0/1` → `Fa0/1`, `Fast0/1`

## ⚠️ Important Notes

### Security
- Change all default passwords before production use
- Update the domain name for your environment
- Consider implementing additional security measures

### Compatibility
- Tested with Cisco IOS 15.x and 16.x
- May require adjustments for different platforms
- Verify command syntax for your specific IOS version

### Backup
- Save original configurations before applying
- Test rollback procedures
- Document any customizations made

## 📞 Support

For configuration issues:
1. Check the troubleshooting guide: `../06-troubleshooting-guide.md`
2. Verify interface names and IP addresses
3. Review security configurations
4. Test connectivity step by step

---

*Use these configurations as templates for your network deployment*
