# Albion University Network Design Project

A Cisco Packet Tracer network simulation project implementing a multi-campus university network with VLANs, RIPv2 routing, DHCP, and inter-building connectivity.

---

## 📋 Project Overview

This project designs and configures a full enterprise-grade network for **Albion University**, which spans two campuses located 20 miles apart. The network supports four faculties and various administrative departments, providing end-to-end connectivity including access to internal servers and an externally hosted email server.

---

## 🏫 Network Topology

### Main Campus

| Building | Departments / Faculties |
|----------|------------------------|
| **Building A** | Management, HR, Finance (Admin), Faculty of Business |
| **Building B** | Faculty of Engineering & Computing, Faculty of Art & Design |
| **Building C** | Students' Labs, IT Department, Web Server, Other Internal Servers |

### Smaller Campus

| Floor | Description |
|-------|-------------|
| **Floor 1** | Faculty of Health & Sciences – Staff |
| **Floor 2** | Faculty of Health & Sciences – Students' Labs |

### External
- Email server hosted on the **cloud** (external network)

---

## 🗂️ Project Tasks

### Task 1 – Network Topology Design
- Plan and prototype the full network topology in Cisco Packet Tracer
- Include all buildings, campuses, servers, and end devices
- Apply logical grouping using VLANs per department/faculty

### Task 2 – Network Configuration
- Configure core switches, routers, and selected end devices
- Implement VLANs and inter-VLAN routing
- Set up DHCP on routers for Building A dynamic addressing
- Configure **RIPv2** for internal routing between routers
- Configure **static routing** for the external email server
- Apply switch security settings

### Task 3 – Evaluation Report
- Max **1500 words**
- Evaluate the proposed network design
- Critical appraisal covering performance, scalability, and design decisions

---

## 🌐 IP Addressing Scheme

Each department/faculty is on its own separate IP network (subnetting applied per VLAN):

| Department / Faculty | VLAN ID | Network (Example) |
|----------------------|---------|-------------------|
| Management | 10 | 192.168.10.0/24 |
| HR | 20 | 192.168.20.0/24 |
| Finance | 30 | 192.168.30.0/24 |
| Faculty of Business | 40 | 192.168.40.0/24 |
| Engineering & Computing | 50 | 192.168.50.0/24 |
| Art & Design | 60 | 192.168.60.0/24 |
| Students' Labs (Main) | 70 | 192.168.70.0/24 |
| IT Department | 80 | 192.168.80.0/24 |
| Health & Sciences – Staff | 90 | 192.168.90.0/24 |
| Health & Sciences – Students | 100 | 192.168.100.0/24 |

> ⚠️ Adjust IP ranges to fit your actual subnetting plan.

---

## ⚙️ Key Configurations

### VLANs
```bash
vlan 10
 name Management
vlan 20
 name HR
vlan 30
 name Finance
# ... (repeat for all departments)
```

### Trunk Ports (between switches and router)
```bash
interface gig1/0/1
 switchport mode trunk
```

### DHCP (Router – Building A)
```bash
ip dhcp pool MANAGEMENT
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 dns-server 8.8.8.8

ip dhcp excluded-address 192.168.10.1 192.168.10.10
```

### RIPv2 (Internal Routing)
```bash
router rip
 version 2
 network 192.168.0.0
 no auto-summary
```

### Static Route (External Email Server)
```bash
ip route 0.0.0.0 0.0.0.0 <ISP_gateway_IP>
```

### Switch Port Security (Example)
```bash
interface fa0/1
 switchport mode access
 switchport port-security
 switchport port-security maximum 1
 switchport port-security violation restrict
 switchport port-security mac-address sticky
```

---

## 🛠️ Tools & Technologies

| Tool | Purpose |
|------|---------|
| Cisco Packet Tracer | Network simulation and configuration |
| Cisco 3650 / 2960 Switches | Layer 3 / Layer 2 switching |
| Cisco 2911 Router | Routing, DHCP, inter-VLAN routing |
| RIPv2 | Dynamic internal routing protocol |
| 802.1Q (dot1q) | VLAN trunking |
| DHCP | Dynamic IP assignment (Building A) |

---

## 📁 File Structure

```
albion-university-network/
│
├── README.md                   # This file
├── albion_network.pkt          # Cisco Packet Tracer topology file
└── report/
    └── evaluation_report.docx  # Task 3 – Evaluation report (max 1500 words)
```

---

## 🔗 Connectivity Summary

- ✅ End-to-end connectivity across both campuses
- ✅ Access to internal web server (Building C)
- ✅ Access to external email server (cloud)
- ✅ Dynamic IP addressing for Building A devices
- ✅ VLAN isolation per department/faculty
- ✅ Secure switch port configurations

---

## 👤 Author

**Student Name:** _Your Name Here_  
**Module:** Computer Networks / Network Design  
**Institution:** Albion University (Simulated)  
**Submission:** Cisco Packet Tracer + Evaluation Report
