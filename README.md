# Cisco ISE 802.1X & MAB Authentication Lab
<img src="https://i.imgur.com/E6Sk9GW.png" height="80%" alt="ISE Dot1x and MAB" />
## Overview
This lab demonstrates the configuration of **Cisco Identity Services Engine (ISE) 3.0** for **802.1X authentication and MAC Authentication Bypass (MAB)** using **EVE-NG**. It includes **Active Directory (AD) integration, authentication policies, network access control (NAC), switch AAA setup, and client authentication verification**.

## Lab Setup
The topology consists of:
- **Cisco ISE 3.0**
- **Windows Server 2019** (AD, DNS, NTP)
- **Cisco Switch (AAA & DOT1X configuration)**
- **Windows 10 & Windows 7 Clients** (Domain PCs)
- **Android Device** (BYOD authentication)
- **ISP Router (Simulated network)**

## Objectives
- Configure **Active Directory (AD), DNS, and NTP Server**
- Integrate **Cisco ISE with Active Directory** for authentication
- Set up **802.1X authentication policies**
- Configure **MAC Authentication Bypass (MAB)** for non-802.1X devices
- Implement **Access Control Lists (ACLs) & Authorization Profiles**
- Configure **AAA on switches** for ISE integration
- **Test and verify authentication methods** for different client devices

## Configuration Steps

### **1. Configure Active Directory & DNS**
#### **Windows Server 2019 Configuration**
```shell
IP Address: 10.1.1.201/24
Gateway: 10.1.1.254
DNS: 8.8.8.8, 8.8.4.4
```
#### **Install Active Directory & DNS**
```shell
Add Active Directory Domain Services role
Create Domain: eve.lab
Configure Reverse Lookup Zones
Add ISE Host Record (A) - 10.1.1.200
```

### **2. Configure Cisco ISE Pre-Stage & Join to AD**
```shell
Hostname: ise
IP Address: 10.1.1.200/24
Domain: eve.lab
DNS Server: 10.1.1.201
NTP Server: 10.1.1.201
```
#### **Join ISE to AD**
```shell
Navigate to ISE → Administration → Identity Management
Add Active Directory → eve.lab
Authenticate with AD Admin Credentials
Join & Retrieve Domain Groups
```

### **3. Configure AAA on Switches**
```shell
radius server ISE
 address ipv4 10.1.1.200 auth-port 1812 acct-port 1813
 key eve1

aaa group server radius ISE-GROUP
 server name ISE

aaa authentication dot1x default group ISE-GROUP
aaa authorization network default group ISE-GROUP
aaa accounting dot1x default start-stop group ISE-GROUP
```

### **4. Configure Authorization Policies & ACLs**
#### **Downloadable ACLs (DACLs)**
```shell
EVE_DHCP_ACL: permit udp any eq 68 any eq 67
PERMIT_AD_ONLY: permit ip any host 10.1.1.201
WIRED_PERMIT_ALL: permit ip any any
```

### **5. Configure 802.1X & MAB Authentication Policies**
```shell
Navigate to ISE → Policy → Policy Sets
Create Policy Set: EVE-POLICY
Match Conditions: Device Type - LAN Switches, Location - My LAN
Authentication Source: AD Identity Sequence
```
#### **Authorization Policies**
```shell
AD_PC_RULE: Apply WIRED_AD_ONLY Profile for Domain PCs
AD_USER_ACCESS: Apply WIRED_PERMIT_ALL Profile after user login
MAB_RULE: Apply MAB_DHCP_PROFILE for non-802.1X devices
```

### **6. Configure Switch Ports for DOT1X & MAB**
```shell
interface Ethernet1/0
 description win10 node
 switchport access vlan 20
 switchport mode access
 authentication host-mode multi-auth
 authentication port-control auto
 mab
 dot1x pae authenticator
 spanning-tree portfast edge
```

### **7. Verify Authentication**
#### **Check Authenticated Sessions on Switch**
```shell
show authentication sessions
```
#### **Verify ISE Live Logs**
```shell
Navigate to ISE → Operations → Live Logs
Check authentication success/failures
```

## Repository Structure
```
├── configs/
│   ├── ISE_config.txt   # Configuration for Cisco ISE
│   ├── Switch_config.txt   # Configuration for Cisco Switch
├── README.md          # Documentation
├── screenshots/       # Screenshots of verification outputs
└── topology.png       # Network diagram
```

## **Conclusion**
This project provides a hands-on approach to implementing **802.1X and MAB authentication using Cisco ISE**. It demonstrates **secure network access control, policy enforcement, and AD integration**, making it an essential lab for security professionals.

---
**Author:** Travis Johnson  
**Company:** 10Digit Solutions LLC  
**GitHub Repository:** [Add link when available]
