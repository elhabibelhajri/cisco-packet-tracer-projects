# Telecommunication Company Network System Design

## Project Overview

This project presents the design and implementation of a secure, scalable, and highly available enterprise network for Cairo Telco, a telecommunications company located in Cairo, Egypt. The company occupies the fourth and fifth floors of Pharaoh's Mega Plaza and requires a robust network infrastructure to support its operations, cloud services, VoIP communications, and wireless connectivity across all departments.

The network was designed and simulated using Cisco Packet Tracer, following a hierarchical network model to ensure redundancy, performance, and manageability.

---

## Network Topology

The network follows a three-layer hierarchical design:

- **Core Layer**: Cisco Catalyst 3850 48-Port Multilayer Switch (CAIRO-CORE-SW) responsible for high-speed routing and switching between all segments.
- **Distribution/Access Layer**: Three Cisco Catalyst 2960 48-Port Switches serving the six departments across two floors.
- **Perimeter Security**: Cisco ASA 5525-X Firewall separating internal zones from the DMZ and external internet.

### Floors and Departments

| Floor | Department | Users |
|---|---|---|
| 4th Floor | HR and Finance | 40 |
| 4th Floor | Product Brand and Marketing | 45 |
| 4th Floor | Admin and Corporate | 35 |
| 5th Floor | IT Network and Support | 45 |
| 5th Floor | Software Engineering | 36 |
| 5th Floor | Cloud Engineering | 32 |

---

## IP Addressing Scheme

| Segment | Network | Purpose |
|---|---|---|
| LAN | 192.168.10.0/24 | Wired user devices |
| WLAN | 10.20.0.0/16 | Wireless user devices |
| VoIP | 172.16.10.0/24 | IP phones |
| DMZ | 10.10.10.0/28 | Internal servers |
| Public | 197.200.100.0 | ISP-facing interface |
| Azure Cloud | 20.20.20.0/30 | Azure connectivity |

---

## VLAN Configuration

| VLAN ID | Name | Purpose |
|---|---|---|
| 50 | LAN | Wired end-user devices |
| 60 | WLAN | Wireless end-user devices |
| 101 | VOICE | VoIP IP phones |

VLANs are consistent across all access switches and the core multilayer switch.

---

## Technologies and Features Implemented

### EtherChannel (LACP)
Link aggregation was configured between CAIRO-CORE-SW and all three access switches using the IEEE 802.3ad LACP protocol. Each EtherChannel bundle aggregates two FastEthernet links per access switch, providing increased bandwidth and redundancy.

- CAIRO-CORE-SW to CAIRO-ACCESS-SW1: Po1 (Gig1/0/4, Gig1/0/5)
- CAIRO-CORE-SW to CAIRO-ACCESS-SW2: Po2 (Gig1/0/6, Gig1/0/7)
- CAIRO-CORE-SW to CAIRO-ACCESS-SW3: Po3 (Gig1/0/8, Gig1/0/9)

All port-channels verified via `show etherchannel summary` with status SU and member ports in P (bundled) state.

### Spanning Tree Protocol
- **PortFast** enabled on all access ports (Fa0/3 to Fa0/24) to allow immediate transition to forwarding state for end-user devices.
- **BPDUGuard** enabled on all PortFast ports to protect against unauthorized switch connections.

### Inter-VLAN Routing
Configured on CAIRO-CORE-SW using Switched Virtual Interfaces (SVIs) for each VLAN. The multilayer switch performs both Layer 2 switching and Layer 3 routing between all VLANs.

### DHCP
All end-user devices obtain IP addresses dynamically from the Windows Server 2022 AD server located in the DMZ server farm. The core switch is configured with DHCP relay (ip helper-address) to forward DHCP requests to the server.

### VoIP and Telephony
A Cisco 2811 Voice Gateway Router is connected to the core switch and configured with Cisco Unified CME (CallManager Express) to provide IP telephony services. Each department has IP phones assigned dial numbers in the format 1XXX.

### Wireless Network
A Cisco WLC-2504 Wireless LAN Controller centrally manages six Lightweight Access Points (LAPs), one per department. Both employee and guest SSIDs are provisioned per department.

### OSPF Routing
OSPF is configured as the dynamic routing protocol on all routers and the multilayer switch to advertise routes between internal zones, the DMZ, and toward the ISP.

### Cisco ASA Firewall
The ASA 5525-X is configured with three security zones:

| Zone | Security Level | Interface |
|---|---|---|
| Inside | 100 | Internal LAN/WLAN/VoIP |
| DMZ | 50 | Server farm |
| Outside | 0 | ISP / Internet |

Inspection policies control traffic flow between zones based on security requirements.

### SSH and Standard ACL
SSH is enabled on all network devices for secure remote management. A standard ACL is applied to VTY lines restricting remote administrative access exclusively to the IT Network and Support department subnet.

### Static Addressing
All servers in the DMZ server farm are assigned static IP addresses:

| Server | Role |
|---|---|
| AD Server | Active Directory, DNS, DHCP |
| ERP Server | Internal ERP application |
| Email Server | Corporate email |
| File Server | Shared file storage |

### Microsoft Azure Connectivity
The network includes a simulated Azure cloud segment (20.20.20.0/30) reachable via the ISP router, representing connectivity to Azure VM, Blob Storage, and networking resources used by the Software Engineering and Cloud Engineering departments.

---

## Device Inventory

| Device | Model | Quantity |
|---|---|---|
| Multilayer Switch | Cisco Catalyst 3850 48-Port | 1 |
| Access Switch | Cisco Catalyst 2960 48-Port | 3 |
| Access Switch | Cisco Catalyst 2960 24-Port | 2 |
| Firewall | Cisco ASA 5525-X | 1 |
| Voice Gateway | Cisco 2811 Router | 1 |
| WLC | Cisco WLC-2504 | 1 |
| Lightweight AP | Cisco LAP | 6 |
| Servers | Windows Server 2022 | 4 |

---

## Verification Commands

```
show etherchannel summary
show interfaces trunk
show vlan brief
show ip route
show ip ospf neighbor
show ip dhcp binding
show telephony-service
show ephone registered
show wireless client summary
```

---

## Tools Used

- Cisco Packet Tracer 9.0.0.0810
- Cisco IOS CLI
- Windows Server 2022 (simulated)

---

## Author

**Hajri Habib**
Network and Systems Engineering Student, ISIMA Mahdia
CCNA1 Certified | CCNA2 In Progress
GitHub: [elhabibelhajri](https://github.com/elhabibelhajri)
LinkedIn: [elhabibelhajri](https://linkedin.com/in/elhabibelhajri)
