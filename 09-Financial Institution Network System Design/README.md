# Financial Institution Network System Design

## Jubilee Financial Services Ltd (JFSL) - Enterprise Network Infrastructure

**Project:** Corutech Solutions Enterprise Networking Project #9  
**Tool:** Cisco Packet Tracer  
**Design Model:** Hierarchical (Three-Layer: Core, Distribution, Access)

---

## Overview

This project covers the full network design and implementation for Jubilee Financial Services Ltd (JFSL), a financial services company based in Nairobi, Kenya. The company operates across the 7th and 8th floors of an eleven-story building and requires a secure, redundant, and scalable network infrastructure connecting its headquarters, an external server-side site, and two ISPs.

The design enforces strict separation between departments via VLANs, routes traffic securely between HQ and the server-side site using IPsec VPN, and implements access control through ACLs, SSH restrictions, and port security.

---

## Network Topology

### Sites

| Site | Description |
|---|---|
| JFSL HQ Network | 7th and 8th floors, five departments, two multilayer switches |
| JFSL Server-Side Network | External site hosting DHCP, DNS, WEB, and EMAIL servers |
| ISP Area | Safaricom ISP and JTL ISP providing dual-link redundancy |
| Voice Gateway | Cisco 2811 router providing VoIP telephony services |

### Devices Used

| Device | Role | Quantity |
|---|---|---|
| Cisco 2911 Router | HQ Router, Server-Side Router | 2 |
| Cisco 2811 Router | HQ VoIP Gateway Router | 1 |
| Cisco 2911 Router | Safaricom ISP, JTL ISP | 2 |
| Cisco 3560 Multilayer Switch | HQ Core/Distribution (HQ-ML-SW1, HQ-ML-SW2) | 2 |
| Cisco 2960 Access Switch | One per department | 6 |
| Cisco 2960 Access Switch | Server-Side Switch | 1 |
| Wireless Access Point | One per department | 6 |
| IP Phones | Deployed per department | Multiple |
| Servers | DHCP, DNS, WEB, EMAIL | 4 |

---

## IP Addressing Scheme

### Address Spaces

| Purpose | Network |
|---|---|
| Data (LAN) | 192.168.20.0/24 |
| Voice (VoIP) | 10.10.10.0/24 |
| Public (ISP-facing) | 190.200.100.0/24 |
| Server-Side LAN | 192.168.21.0/24 |
| Voice Gateway LAN | 10.10.10.0/24 |

### VLAN and Subnet Allocation (Data - 192.168.20.0/24 subnetted)

| Department | Floor | VLAN ID | Subnet | Hosts |
|---|---|---|---|---|
| HR | 7th | 10 | 192.168.20.0/26 | 40+ |
| CS | 7th | 20 | 192.168.20.64/26 | 40+ |
| MK | 7th | 30 | 192.168.20.128/26 | 40+ |
| LM | 8th | 40 | 192.168.20.192/27 | 20+ |
| IT | 8th | 50 | 192.168.20.224/27 | 20+ |
| Voice (all) | Both | 120 | 10.10.10.0/24 | All IP phones |

### ISP Link Addresses (Public)

| Link | Network |
|---|---|
| Safaricom ISP to HQ Router | 190.200.100.0/30 |
| JTL ISP to HQ Router | 190.200.100.4/30 |
| Safaricom ISP to Server-Side Router | 190.200.100.8/30 |
| JTL ISP to Server-Side Router | 190.200.100.12/30 |

### Inter-Router Links

| Link | Network |
|---|---|
| HQ Router to HQ-ML-SW1 | 192.168.21.16/30 |
| HQ Router to HQ-ML-SW2 | 192.168.21.20/30 |

---

## Features Implemented

### 1. Hierarchical Network Design
Three-layer hierarchical model with redundancy at every layer. The two multilayer switches provide dual uplinks to the HQ router, and each ISP router connects to both the HQ and Server-Side routers for full redundancy.

### 2. VLANs and Inter-VLAN Routing
Each department is placed in a dedicated VLAN and subnet. Inter-VLAN routing is handled by the multilayer switches using Switched Virtual Interfaces (SVIs). Trunk links carry all VLANs between the multilayer switches and the access layer switches.

Voice VLAN ID 120 is consistent across the entire network.

### 3. DHCP
All end-user devices obtain IP addresses dynamically from the DHCP server located at the server-side site. DHCP relay (ip helper-address) is configured on each SVI of the multilayer switches to forward DHCP discovery packets across the routed network to the remote server.

IP phones are assigned addresses from the 10.10.10.0/24 pool via a separate voice VLAN DHCP scope.

### 4. VoIP (Telephony Service)
The Cisco 2811 router is configured as the CUCME (Cisco Unified Communications Manager Express) voice gateway. It is connected to one of the multilayer switches at HQ. IP phones register to this router and are assigned dial numbers in the format 4xxx.

### 5. Wireless (Wi-Fi)
Each department has a wireless access point connected to its access switch. Wireless clients connect to the same VLAN and subnet as the wired hosts in the same department.

### 6. OSPF Routing
OSPF is used as the routing protocol across all routers and multilayer switches. All internal subnets, inter-router links, and server-side networks are advertised via OSPF to ensure full reachability.

### 7. NAT/PAT
PAT (Port Address Translation) is configured on the HQ router to translate internal private addresses to the outbound public interface IP. An ACL defines which traffic is eligible for NAT.

### 8. Site-to-Site IPsec VPN
A site-to-site IPsec VPN tunnel is established between the HQ router and the Server-Side router. This secures all traffic traversing the WAN between headquarters and the external server-side site. An ACL defines the interesting traffic (HQ LAN to Server-Side LAN) that triggers the VPN.

### 9. SSH Remote Access
SSH is configured on all routers and Layer 3 switches for secure remote management. Telnet is disabled. A standard ACL restricts SSH access on VTY lines to allow only the IT department subnet.

### 10. Access Control Lists (ACL)
- Standard ACL on VTY lines: permits SSH only from IT department (192.168.20.224/27)
- Extended ACL for NAT: permits HQ LAN traffic to be translated for internet access
- Extended ACL for IPsec VPN: defines interesting traffic between HQ and server-side site

### 11. Port Security (Server-Side Switch)
Port security is configured on all active ports of the server-side access switch:
- Maximum of 1 MAC address per port
- Sticky MAC learning
- Violation mode: shutdown

### 12. Basic Device Hardening
Configured on all routers and switches:
- Hostname
- Console and enable passwords (encrypted via `service password-encryption`)
- MOTD banner
- `no ip domain-lookup`
- SSH version 2

---

## Device Configuration Summary

### HQ Router (2911)
```
- Dual ISP uplinks (Safaricom, JTL) for redundancy
- OSPF for internal and external route advertisement
- NAT/PAT on outbound ISP interfaces
- IPsec VPN to Server-Side Router
- SSH enabled, ACL restricts VTY to IT subnet
```

### Multilayer Switches (HQ-ML-SW1, HQ-ML-SW2)
```
- SVIs for each VLAN (HR, CS, MK, LM, IT, Voice)
- Inter-VLAN routing enabled (ip routing)
- DHCP relay (ip helper-address) on each SVI
- OSPF participation for LAN subnet advertisement
- Trunk ports to access switches and uplinks to HQ Router
- SSH enabled
```

### Access Switches (per department)
```
- Access ports assigned to department VLAN
- Voice VLAN 120 on ports with IP phones
- Trunk uplinks to both multilayer switches
- Basic hardening applied
```

### VoIP Gateway (Cisco 2811)
```
- telephony-service block configured
- max-ephones and max-dn set
- auto-assign ephones to directory numbers
- Dial plan: 4xxx format
- Connected to a multilayer switch at HQ
```

### Server-Side Router (2911)
```
- OSPF to advertise server-side LAN
- IPsec VPN termination from HQ
- SSH enabled
```

### Server-Side Switch (2960)
```
- Port security: sticky, max 1 MAC, shutdown on violation
- Static IP addresses on all servers
```

---

## Server Static IP Assignments

| Server | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|
| DHCP Server | 192.168.21.2 | 255.255.255.0 | 192.168.21.1 |
| DNS Server | 192.168.21.3 | 255.255.255.0 | 192.168.21.1 |
| WEB Server | 192.168.21.4 | 255.255.255.0 | 192.168.21.1 |
| EMAIL Server | 192.168.21.5 | 255.255.255.0 | 192.168.21.1 |

---

## Security Summary

| Feature | Implementation |
|---|---|
| VLAN segmentation | Five data VLANs, one voice VLAN |
| ACL - SSH restriction | Standard ACL on VTY, permits IT subnet only |
| ACL - NAT | Permits HQ LAN for PAT |
| ACL - IPsec | Defines HQ-to-server interesting traffic |
| IPsec VPN | AES/SHA site-to-site tunnel, HQ to Server-Side |
| Port Security | Sticky, max 1 MAC, shutdown - server-side switch only |
| Password Encryption | service password-encryption on all devices |
| SSH v2 | All routers and L3 switches |
| No Telnet | VTY lines transport input set to SSH only |

---

## Testing Checklist

- PC to PC communication within the same VLAN
- PC to PC communication across VLANs (inter-VLAN routing)
- PC to server (DHCP assignment, DNS resolution, WEB access, EMAIL)
- IP phone to IP phone calls within and across departments (4xxx dial plan)
- Wireless client connectivity per department
- SSH login from IT department to routers and switches
- SSH login blocked from non-IT departments
- NAT/PAT: internal host internet access via ISP
- IPsec VPN: encrypted traffic between HQ and server-side site
- Port security: violation triggered by second MAC on server-side switch port
- OSPF neighbor adjacencies verified on all routers and L3 switches

---

## Tools and Technologies

| Technology | Purpose |
|---|---|
| Cisco Packet Tracer | Network simulation and implementation |
| OSPF | Dynamic routing protocol |
| VLANs | Layer 2 segmentation per department |
| 802.1Q Trunking | VLAN transport between switches |
| DHCP Relay | Remote DHCP server support |
| PAT | Internet access for private hosts |
| IPsec VPN | Secure WAN connectivity to server-side |
| CUCME (2811) | VoIP telephony service |
| SSH v2 | Secure remote management |
| ACL | Traffic filtering and access control |
| STP (default) | Loop prevention at Layer 2 |
| Port Security | Server-side MAC-level access control |

---

## Repository Structure

```
.
├── README.md
└── Financial_Institution_Network.pkt   # Cisco Packet Tracer project file
```

---

## Author

Designed and implemented as part of Corutech Solutions Enterprise Networking Project #9.
