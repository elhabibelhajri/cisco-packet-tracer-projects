# Advanced Campus Area Network System Design

**Project #12 — Martin Luther King University**
Secure, Redundant, and Scalable Dual-Campus Enterprise Network

---

## Overview

This project presents the complete design and implementation of a secure enterprise campus network for Martin Luther King University, a multi-campus institution with approximately 30,000 users distributed across two sites located 100 miles apart. The network was built in Cisco Packet Tracer following a hierarchical three-tier model, emphasizing performance, redundancy, scalability, and security in compliance with the university's long-term growth projections.

---

## Network Topology

The design covers two campuses — **Main Campus** and **Branch Campus** — interconnected via a Site-to-Site IPsec VPN tunnel over a shared ISP (Airtel). The main campus hosts the centralized IT infrastructure including the server farm (DMZ), the Wireless LAN Controller (WLC), and the primary Cisco ASA Firewall. The branch campus mirrors the access and distribution layers with its own firewall and local switching infrastructure.

Each campus serves four faculties:

- Health and Sciences
- Business
- Engineering / Computing
- Art and Design

---

## Architecture

```
Internet (Airtel ISP)
        |
   [ASA Firewall]          [ASA Firewall]
   Main Campus             Branch Campus
        |                        |
  [Core Layer]             [Core Layer]
  2x Catalyst 3850         2x Catalyst 3850
        |                        |
  [Distribution/Access]    [Distribution/Access]
  Catalyst 2960 per        Catalyst 2960 per
  faculty                  faculty
        |
   [DMZ / Server Farm]
   DHCP, DNS, FTP, WEB,
   Email, SMTP
```

---

## Technologies Implemented

| Category | Technology |
|---|---|
| Design Model | Cisco Three-Tier Hierarchical |
| Routing Protocol | OSPF (multi-area) |
| Switching | VLANs, Inter-VLAN Routing, STP PortFast, BPDUguard |
| Link Aggregation | EtherChannel with LACP (802.3ad) |
| High Availability | HSRP (Hot Standby Router Protocol) |
| Security | Cisco ASA 5500-X Firewalls, IPsec VPN, Standard ACL for SSH |
| Wireless | Cisco WLC + Lightweight Access Points (LAPs) |
| Addressing | DHCP (dynamic), Static (servers), Subnetting |
| Cloud | Google Cloud Platform integration |
| Simulation Tool | Cisco Packet Tracer |

---

## IP Addressing Scheme

| Network Segment | Address Range |
|---|---|
| Management | 172.16.10.0/24 |
| LAN | 192.168.0.0/16 |
| WLAN | 10.10.0.0/16 |
| DMZ (Server Farm) | 10.20.20.0/27 |
| Main Campus Public | 105.100.50.0/30 |
| Branch Campus Public | 205.200.100.0/30 |

---

## VLAN Configuration

| VLAN ID | Name | Purpose |
|---|---|---|
| 10 | Management | Network device management |
| 20 | LAN | Wired end-user devices |
| 50 | WLAN | Wireless end-user devices |
| 199 | Blackhole | Unused port containment |

---

## Key Configuration Details

### Redundancy and High Availability
- HSRP configured on core multilayer switches to provide gateway failover and load balancing across all faculty VLANs.
- EtherChannel (LACP) aggregates uplinks between access and core switches to increase throughput and eliminate single points of failure.
- Dual DHCP servers deployed at the server farm for failover redundancy.

### Security
- Two Cisco ASA 5500-X firewalls enforce zone-based security policies, separating LAN, WLAN, DMZ, and outside zones with explicit inspection rules.
- Site-to-site IPsec VPN established between the two campus firewalls for encrypted inter-campus communication.
- Standard ACL applied to VTY lines, restricting SSH access exclusively to the Senior Network Security Engineer workstation.
- STP BPDUguard enabled on all access ports to prevent rogue switch connections.

### Wireless
- A single Cisco WLC deployed at the main campus centrally manages all LAPs across both campuses.
- Separate SSIDs provisioned per faculty to enforce WLAN segmentation.

### Routing
- OSPF advertises routes between the ASA firewalls, core multilayer switches, and the ISP edge.
- Static default routes configured on each firewall pointing toward the ISP.

### Server Farm (DMZ)
- Hosts: DHCP, DNS, FTP, Web, Email, and SMTP servers.
- All servers assigned static IP addresses within the 10.20.20.0/27 range.
- Branch campus users access DMZ resources securely through the IPsec VPN tunnel.

---

## Project Structure

```
project-12-campus-network/
|
+-- PacketTracer/
|   +-- MLK_University_Network.pkt       # Main simulation file
|
+-- Documentation/
|   +-- Project_Brief.pdf                # Original assignment brief
|   +-- Network_Topology.png             # Logical topology diagram
|
+-- README.md
```

---

## How to Run

1. Open Cisco Packet Tracer (version 8.x or later recommended).
2. Load `PacketTracer/MLK_University_Network.pkt`.
3. Use the simulation mode to verify traffic flows between campuses, test DHCP assignment, confirm HSRP failover, and validate IPsec VPN connectivity.
4. SSH into core devices from the designated Senior Network Engineer PC (Standard ACL enforced).

---

## Skills Demonstrated

- Enterprise network design using the Cisco hierarchical model
- Multi-area OSPF routing configuration
- Cisco ASA firewall policy configuration (zones, security levels, NAT, ACLs)
- IPsec VPN site-to-site tunnel configuration
- HSRP for gateway redundancy and load balancing
- EtherChannel (LACP) link aggregation
- Wireless network deployment with centralized WLC management
- VLAN design, trunking, and inter-VLAN routing
- Subnetting and IP addressing for large-scale environments
- DHCP server configuration with failover
- STP hardening (PortFast, BPDUguard)

---

## Author

**Sami**
Network and Systems Engineering Student — ISIMA Mahdia, Tunisia
CCNA 1 Certified | CCNA 2 & CompTIA Security+ (SY0-701) in progress
Vice-President, Code Bey Club

---

## License

This project was developed for academic purposes as part of the Advanced Enterprise Networking curriculum. All network designs and configurations are original work produced for educational use.
