# &#x20;Hospital Network Design — Melbourne Health Services

### Enterprise Networking Project #7 | Cisco Packet Tracer

\---

## Project Overview

Melbourne Health Services operates across two locations within the same city — a **Headquarters (HQ)** hospital and a **Branch (BR)** hospital, approximately 20km apart. This project designs and implements a secure, redundant enterprise network connecting both sites using Cisco technologies.

The network follows a **hierarchical design model** with full inter-VLAN routing, centralized DHCP, OSPF dynamic routing, IPSec Site-to-Site VPN, NAT/PAT, and SSH management access.

\---

## Network Topology Summary

```
\[HQ Departments]          \[SSS Server Room]         \[BR Departments]
  MLOCS  - VLAN 10            DHCP Server              NSO  - VLAN 80
  MER    - VLAN 20            DNS Server               HL   - VLAN 90
  MRM    - VLAN 30            Email Server             HR   - VLAN 100
  IT     - VLAN 40            Web Server               MK   - VLAN 110
  CS     - VLAN 50                                     FIN  - VLAN 120
  GWA    - VLAN 60                                     GWA  - VLAN 130
       |                          |                         |
  HQ-Multilayer1/2           HQ-Router ══════════════ BR-Router
                            (IPSec VPN Tunnel)       BR-Multilayer1/2
```

\---

## Department \& VLAN Configuration

### HQ Network — Base: `192.168.100.0`

|Department|VLAN|Subnet|Hosts|Gateway|
|-|-|-|-|-|
|MLOCS|10|192.168.100.0/26|60|192.168.100.1|
|MER|20|192.168.100.64/26|60|192.168.100.65|
|MRM|30|192.168.100.128/26|60|192.168.100.129|
|IT|40|192.168.100.192/26|60|192.168.100.193|
|CS|50|192.168.101.0/26|60|192.168.101.1|
|GWA|60|192.168.101.64/26|60|192.168.101.65|

### BR Network

|Department|VLAN|Subnet|Hosts|Gateway|
|-|-|-|-|-|
|NSO|80|192.168.101.128/27|30|192.168.101.129|
|HL|90|192.168.101.160/27|30|192.168.101.161|
|HR|100|192.168.101.192/27|30|192.168.101.193|
|MK|110|192.168.101.224/27|30|192.168.101.225|
|FIN|120|192.168.102.0/27|30|192.168.102.1|
|GWA-BR|130|192.168.102.32/27|30|192.168.102.33|

### SSS Server Room

|Device|VLAN|IP Address|Subnet|
|-|-|-|-|
|DHCP Server|70|192.168.102.67|192.168.102.64/28|
|DNS Server|70|192.168.102.68|192.168.102.64/28|
|Email Server|70|192.168.102.69|192.168.102.64/28|
|Web Server|70|192.168.102.70|192.168.102.64/28|

\---

## WAN \& Inter-Device Links

|Link|Subnet|HQ Side|BR/ISP Side|
|-|-|-|-|
|HQ-Router ↔ HQ-SW1|192.168.102.80/30|.82|.81|
|HQ-Router ↔ HQ-SW2|192.168.102.84/30|.86|.85|
|HQ-Router ↔ SSS|192.168.102.64/28|.65|—|
|BR-Router ↔ BR-SW1|192.168.102.92/30|.94|.93|
|BR-Router ↔ BR-SW2|192.168.102.96/30|.98|.97|
|HQ-Router ↔ ISP1|195.136.17.0/30|.1|.2|
|HQ-Router ↔ ISP2|195.136.17.4/30|.5|.6|
|BR-Router ↔ ISP1|195.136.17.8/30|.9|.10|
|BR-Router ↔ ISP2|195.136.17.12/30|.13|.14|
|HQ-Router ↔ BR-Router (VPN)|195.136.17.88/30|.89|.90|

\---

## Technologies Implemented

### 1\. VLANs \& Trunking

* Each department isolated in its own VLAN
* Trunk ports configured between multilayer switches and access switches
* Inter-VLAN routing handled by Layer 3 multilayer switches (3650-24PS)

### 2\. DHCP (Centralized)

* Single DHCP server in SSS Department (VLAN 70)
* `ip helper-address 192.168.102.67` configured on every SVI across all multilayer switches
* DHCP relay forwards broadcasts across VLANs to centralized server

### 3\. OSPF (Routing Protocol)

* OSPF Process ID: **10**, Area: **0**
* Configured on HQ-Router, BR-Router, and all multilayer switches
* Advertises all LAN subnets for dynamic route discovery
* ISP-facing serial links excluded from OSPF to enforce VPN path

### 4\. IPSec Site-to-Site VPN

* **Tunnel:** HQ-Router (`195.136.17.89`) ↔ BR-Router (`195.136.17.90`)
* **IKE Phase 1:** AES-256, SHA, Pre-Shared Key, DH Group 5
* **IKE Phase 2:** ESP-AES, ESP-SHA-HMAC, Tunnel mode
* **Pre-shared key:** `VPNKEY`
* **Interesting traffic:** HQ LAN (`192.168.100.0/23`) ↔ BR LAN (`192.168.101.128/25`, `192.168.102.0/26`)

### 5\. NAT/PAT

* PAT configured on HQ-Router and BR-Router
* Outbound interface: `Serial0/1/0` on each router
* NAT excludes VPN-bound traffic via ACL deny statements

### 6\. SSH Remote Access

* Configured on all routers and multilayer switches
* Domain: `cisco.net`
* Local authentication with username `admin`
* VTY lines restricted to SSH only (`transport input ssh`)

### 7\. Port Security (SSS Switch)

* Sticky MAC address learning
* Maximum 1 device per port
* Violation mode: shutdown

### 8\. Wireless

* Each department has a wireless access point
* Wireless clients obtain IP via DHCP

\---

## Security Configuration

### ACL 110 — VPN Interesting Traffic (HQ-Router)

```
access-list 110 permit ip 192.168.100.0 0.0.0.255 192.168.101.128 0.0.0.127
access-list 110 permit ip 192.168.100.0 0.0.0.255 192.168.102.0 0.0.0.63
access-list 110 permit ip 192.168.102.64 0.0.0.15 192.168.101.128 0.0.0.127
access-list 110 permit ip 192.168.102.64 0.0.0.15 192.168.102.0 0.0.0.63
```

### ACL 1 — NAT Traffic (excludes VPN destinations)

```
access-list 1 deny 192.168.101.128 0.0.0.127
access-list 1 deny 192.168.102.0 0.0.0.63
access-list 1 permit 192.168.100.0 0.0.0.63
access-list 1 permit 192.168.100.64 0.0.0.63
access-list 1 permit 192.168.100.128 0.0.0.63
access-list 1 permit 192.168.100.192 0.0.0.63
access-list 1 permit 192.168.101.0 0.0.0.63
access-list 1 permit 192.168.101.64 0.0.0.63
```

\---

## Testing \& Verification

|Test|Command|Expected Result|
|-|-|-|
|VPN Phase 1|`show crypto isakmp sa`|QM\_IDLE — ACTIVE|
|VPN Phase 2|`show crypto ipsec sa`|#pkts encrypt > 0|
|OSPF Neighbors|`show ip ospf neighbor`|FULL state|
|DHCP Clients|`ipconfig` on any PC|Valid IP from correct subnet|
|Inter-site ping|`ping 192.168.101.136` from PC0|100% success|
|NAT translations|`show ip nat translations`|Active PAT entries|
|SSH access|`ssh -l admin <device-ip>`|Successful login|

\---

## Key Issues Resolved During Implementation

|Issue|Root Cause|Fix Applied|
|-|-|-|
|VPN not negotiating|Typo in Serial0/0/0 IP (`192.18` instead of `192.168`)|Corrected IP address|
|Both routers same Serial IP|BR-Router Serial0/0/0 had HQ's IP|Assigned correct `.90` IP|
|Wrong VPN peer in crypto map|Peer pointed to old internal IP|Updated to `195.136.17.90/89`|
|DHCP not working|OSPF bypassing VPN for BR routes|Removed ISP links from OSPF|
|BR clients no DHCP|BR multilayer switches had no default route|Added `ip route 0.0.0.0 0.0.0.0`|
|VLAN 100 no gateway|Missing IP on VLAN 100 SVI|Added `192.168.101.193/27`|
|HQ→BR ping failing|BR switches had no route to HQ subnets|Added static routes on BR switches|
|NAT intercepting VPN traffic|ACL 1 matching VPN-destined packets|Added deny entries before permit|

\---

## Project Files

```
Hospital-Network-Design/
├── Hospital Network Design.pkt     # Cisco Packet Tracer file
├── README.md                       # This documentation
└── Problem-Hospital\_Network\_Design.pdf  # Project requirements
```

\---

## Author

**Elhabib Elhajri (Hajri Habib)**

* 🎓 Licence en Ingénierie des Réseaux et Systèmes — ISIMA Mahdia
* 🔗 GitHub: [elhabibelhajri](https://github.com/elhabibelhajri)
* 📁 Repository: `cisco-packet-tracer-projects`

\---

*Project completed as part of the Gurutech Networking Training series — Enterprise Network Design #7*

