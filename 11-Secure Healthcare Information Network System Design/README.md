# Secure Healthcare Information Network System Design

## Overview

This project presents the design and implementation of a secure, scalable, and highly available enterprise network for Dr. Devi Shetty Labs Limited, a healthcare diagnostics company headquartered in Mumbai, India. The network spans three floors (35th, 36th, and 37th) of the Great Namaste Towers, supporting departments including Pharmacy, Medical Labs, Reception, Doctors and Consultancy, Procurement, HR, Finance, Internal Audit, Corporate, and IT.

The design follows a hierarchical network model with redundancy, firewall-based security zones, wireless infrastructure, VoIP services, and AWS cloud integration.

---

## Project Details

| Field | Details |
|---|---|
| Tool | Cisco Packet Tracer |
| Model | Hierarchical (Core, Distribution, Access) |
| Firewall | Cisco ASA 5500-X |
| Routing Protocol | OSPF |
| Redundancy | HSRP, EtherChannel (LACP) |
| Wireless | Cisco WLC + 10 Lightweight Access Points |
| VoIP | Cisco 2811 Router (CME) |
| Cloud | AWS Cloud Integration |
| ISP | Airtel |

---

## IP Addressing Scheme

| Network | Subnet |
|---|---|
| LAN | 192.168.0.0/20 |
| WLAN | 10.10.0.0/16 |
| Voice | 172.16.0.0/20 |
| DMZ | 10.20.10.0/26 |
| Public | 197.200.100.0/30 |

---

## VLAN Design

| VLAN ID | Name | Purpose |
|---|---|---|
| 10 | LAN | Wired user devices |
| 50 | WLAN | Wireless user devices |
| 99 | VoIP | IP phones |

---

## Technologies Implemented

- STP
- EtherChannel
- HSRP
- VLAN
- Trunking
- Inter-VLAN Routing
- OSPF
- DHCP
- NAT/PAT
- ASA Firewall
- DMZ
- ACL
- SSH
- Wireless (WLC + LAP)
- VoIP / CME
- Subnetting
- Static Routing
- AWS Cloud Connectivity

---

## Network Architecture

### Floor Layout

| Floor | Departments | Approximate Users |
|---|---|---|
| 35th | Pharmacy, Medical Labs, Reception, Guest Area | 1200 |
| 36th | Doctors and Consultancy, Procurement, HR, Finance | 500 |
| 37th | Internal Audit, Corporate, IT Department | 420 |

### Core Components

| Device | Role |
|---|---|
| Cisco ASA 5500-X | Perimeter firewall, security zones, DMZ |
| Cisco Catalyst 3850 (x2) | Core multilayer switches, inter-VLAN routing, HSRP |
| Cisco Catalyst 2960 (x8) | Access layer switches |
| Cisco 2811 Router | WAN router, VoIP / CME |
| Cisco WLC | Centralized wireless LAN controller |
| Lightweight APs (x10) | Wireless access per department |
| HP ProLiant DL380 Gen10 (x2) | Virtualization servers (VMware ESXi), AD, DNS, DHCP |
| NetApp Storage (x2) | Primary and secondary network storage |

---

## Security Design

### ASA Firewall Zones

| Zone | Interface | Security Level |
|---|---|---|
| OUTSIDE | GigabitEthernet0/0 | 0 |
| INSIDE | GigabitEthernet0/1 | 100 |
| DMZ | GigabitEthernet0/2 | 50 |

### DMZ Servers

| Server | Role |
|---|---|
| Health Information System | Internal healthcare application |
| Email Server | Corporate mail services |
| File Server | Shared file storage |

### Access Control

- Standard ACL applied to VTY lines to restrict SSH access to the Senior Network Security Engineer PC only
- Firewall inspection policies applied for traffic entering and leaving each zone
- BPDUguard and PortFast configured on all access ports

---

## Routing and Redundancy

- OSPF configured on the ASA firewall, WAN router, and both multilayer switches
- HSRP configured on multilayer switches for gateway redundancy and load balancing
- EtherChannel (LACP) configured between core switches for link aggregation

---

## DHCP

- All LAN and WLAN devices obtain IP addresses dynamically from the Active Directory servers in the server farm
- IP helper-address configured on multilayer switch SVIs to relay DHCP requests to the server farm

---

## VoIP

- Cisco 2811 router configured as Call Manager Express (CME)
- IP phones deployed in every department
- Dial numbers assigned in the format 3XXX
- VLAN 99 dedicated to voice traffic

---

## Wireless

- One Lightweight Access Point deployed per department
- All APs managed centrally by the Cisco WLC
- WLAN traffic isolated in VLAN 50

---

## Cloud Integration

- Network connected to AWS cloud platform
- Cloud resources accessible to developers and cloud engineers within the IT department
- Connectivity established through the ISP and ASA firewall

---

## Basic Device Hardening (Applied to All Devices)

- Hostname configured
- Console and enable passwords set
- Password encryption enabled
- Banner MOTD configured
- IP domain-lookup disabled
- SSH enabled with ACL restriction on VTY lines

---

## Repository Structure

```
secure-healthcare-network/
├── README.md
├── topology/
│   └── Secure_Healthcare_Network.pkt
├── docs/
│   └── Problem-Secure_Healthcare_Information_Network_System_Design.pdf
└── configs/
    ├── ASA-Firewall.txt
    ├── Core-Switch-Active.txt
    ├── Core-Switch-Standby.txt
    ├── WAN-Router.txt
    └── Access-Switches/
```

---

## Author

Hajri Habib
Network and Systems Engineering Student, ISIMA Mahdia
GitHub: [github.com/elhabibelhajri](https://github.com/elhabibelhajri)
LinkedIn: [linkedin.com/in/elhabibelhajri](https://linkedin.com/in/elhabibelhajri)
