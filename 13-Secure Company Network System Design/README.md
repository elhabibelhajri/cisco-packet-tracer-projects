# Secure Company Network System Design

**Project #13 — Cytonn Innovation Ltd**
Secure, Redundant, and Scalable Single-Site Enterprise Network

---

## Overview

This project presents the complete design and implementation of a secure enterprise network for Cytonn Innovation Ltd, a cloud solutions company with 600 staff preparing to move into a new building. The network was built in Cisco Packet Tracer following a hierarchical three-tier model, covering six departments with emphasis on security, redundancy, VoIP integration, and scalability.

---

## Network Topology

The building hosts six departments — Sales and Marketing, HR and Logistics, Finance and Accounts, Administrator and Public Relations, ICT, and a Server Room — all connected through a centralized core switching layer. Dual ISP connectivity (SEACOM and Safaricom) ensures internet redundancy. Two Cisco ASA firewalls provide security zone enforcement and failover. A centralized WLC manages all wireless access points, and a Cisco Voice Gateway enables IP telephony across all floors.

---

## Architecture

```
Internet
    |
SEACOM ISP          Safaricom ISP
    |                    |
[ASA Firewall 1]  [ASA Firewall 2]
         |              |
      [Core Layer]
      2x Catalyst 3850 (HSRP + LACP)
              |
      [Access Layer]
      Catalyst 2960 per department
              |
      [DMZ / Server Farm]        [Inside Zone]
      FTP, WEB, Email,           DHCP, DNS,
      APP, NAS Storage           RADIUS, AD
```

---

## Technologies Implemented

| Category | Technology |
|---|---|
| Design Model | Cisco Three-Tier Hierarchical |
| Routing Protocol | OSPF |
| Switching | VLANs, Inter-VLAN Routing, STP PortFast, BPDUguard |
| Link Aggregation | EtherChannel with LACP (802.3ad) |
| High Availability | HSRP, Dual ISP, Dual ASA |
| Security | Cisco ASA 5500-X, Standard ACL for SSH |
| Wireless | Cisco WLC + Lightweight Access Points (LAPs) |
| Telephony | Cisco Voice Gateway, VoIP (VLAN 70, dial plan 4xxx) |
| Addressing | DHCP (dynamic), Static (servers), Subnetting |
| Authentication | Active Directory, RADIUS |
| Simulation Tool | Cisco Packet Tracer |

---

## IP Addressing Scheme

| Network Segment | Address Range |
|---|---|
| Management | 192.168.10.0/24 |
| LAN | 172.16.0.0/16 |
| WLAN | 10.20.0.0/16 |
| DMZ | 172.30.0.0/16 |
| VoIP | 172.30.0.0/16 |
| SEACOM Public | 105.100.50.0/30 |
| Safaricom Public | 197.200.100.0/30 |

---

## VLAN Configuration

| VLAN ID | Name | Purpose |
|---|---|---|
| 10 | Management | Network device management |
| 20 | LAN | Wired end-user devices |
| 50 | WLAN | Wireless end-user devices |
| 70 | VoIP | IP phone traffic |
| 90 | Inside Servers | DHCP, DNS, RADIUS |
| 199 | Blackhole | Unused port containment |

---

## Key Configuration Details

### Redundancy and High Availability
- HSRP configured on both core multilayer switches for gateway failover and load balancing across all department VLANs.
- EtherChannel (LACP) aggregates uplinks between access and core switches.
- Dual ASA firewalls deployed for firewall-level redundancy.
- Dual ISP connections (SEACOM + Safaricom) eliminate single points of failure at the internet edge.

### Security
- Cisco ASA 5500-X firewalls enforce three security zones: outside, inside, and DMZ.
- Inside zone hosts Active Directory, DHCP, DNS, and RADIUS servers.
- DMZ hosts FTP, Web, Email, APP, and NAS Storage servers.
- Standard ACL on VTY lines restricts SSH access to the Senior Network Security Engineer workstation only.
- STP BPDUguard enabled on all access ports.

### VoIP
- Cisco Voice Gateway configured for IP telephony across all floors.
- Dial plan uses 4xxx format with VLAN 70 dedicated to voice traffic.
- IP phones deployed per department.

### Wireless
- Centralized Cisco WLC manages all LAPs across every department.
- WLAN traffic isolated on VLAN 50.

### Routing
- OSPF advertises routes between ASA firewalls and core multilayer switches.
- Static default routes on each ASA pointing to respective ISP gateways.

### Server Zones
- Inside zone: DHCP, DNS, RADIUS — static addresses within 10.11.11.0/27.
- DMZ: FTP, Web, Email, APP, NAS Storage — static addresses within the DMZ range.

---

## Departments

| Department | Floor |
|---|---|
| Sales and Marketing | Floor 1 |
| HR and Logistics | Floor 1 |
| Finance and Accounts | Floor 2 |
| Administrator and Public Relations | Floor 2 |
| ICT | Floor 2 |
| Server Room | Floor 3 |

---

## Project Structure

```
project-13-secure-company-network/
|
+-- PacketTracer/
|   +-- Cytonn_Innovation_Network.pkt
|
+-- Documentation/
|   +-- Project_Brief.pdf
|   +-- Network_Topology.png
|
+-- README.md
```

---

## How to Run

1. Open Cisco Packet Tracer (version 8.x or later recommended).
2. Load `PacketTracer/Cytonn_Innovation_Network.pkt`.
3. Use simulation mode to verify inter-VLAN routing, DHCP assignment, HSRP failover, VoIP dial functionality, and ASA zone policy enforcement.
4. SSH into core devices from the designated Senior Network Security Engineer PC only.

---

## Skills Demonstrated

- HSRP
- EtherChannel (LACP)
- VLANs
- Inter-VLAN Routing (SVIs)
- OSPF
- Cisco ASA Firewall (Zones, Security Levels, Policies)
- Dual ISP Redundancy
- DHCP (Centralized / Inside Zone)
- Subnetting
- STP PortFast & BPDUguard
- Wireless LAN Controller (WLC) + LAPs
- VoIP / IP Telephony (Cisco Voice Gateway, VLAN 70)
- Standard ACL (SSH restriction)
- Static Routing (Default routes on ASA)
- DMZ Design
- Active Directory & RADIUS Authentication
- Hierarchical Network Design (Three-Tier Model)

---

## Author

**Sami**
Network and Systems Engineering Student — ISIMA Mahdia, Tunisia
CCNA 1 Certified | CCNA 2 & CompTIA Security+ (SY0-701) in progress
Vice-President, Code Bey Club

---

## License

This project was developed for academic purposes as part of the Advanced Enterprise Networking curriculum. All network designs and configurations are original work produced for educational use.
