# Small Business Network – XYZ Company Branch

## Project Overview

This project covers the design and implementation of a small branch network for **XYZ Company**, a food items company based in Eastern Australia. The branch is located in **Bonalbo village** and operates independently from the HQ network. Built and simulated using **Cisco Packet Tracer**.

---

## Network Topology Summary

| Component | Details |
|-----------|---------|
| **Router** | 1 × Cisco Router |
| **Switch** | 1 × Cisco Switch |
| **VLANs** | 3 — one per department |
| **Wireless** | 3 × Access Points — one per department |
| **IP Assignment** | DHCP — all devices obtain addresses automatically |

---

## Department & VLAN Configuration

| VLAN | Department | Network Address | Usable Range | Broadcast |
|------|------------|-----------------|--------------|-----------|
| 10 | Admin / IT | 192.168.1.0/26 | .1 – .62 | .63 |
| 20 | Finance / HR | 192.168.1.64/26 | .65 – .126 | .127 |
| 30 | Customer Service / Reception | 192.168.1.128/26 | .129 – .190 | .191 |

> Base address: `192.168.1.0` — subnetted into `/26` blocks (62 usable hosts each).

---

## Requirements

- 1 Router and 1 Switch (all Cisco products)
- 3 departments, each isolated in its own VLAN
- Wireless access in every department via dedicated Access Points
- All hosts obtain IP addresses automatically via DHCP
- Full inter-department communication enabled (inter-VLAN routing)

---

## Testing Communication

To verify connectivity between departments:

1. Open any **PC** → **Desktop** → **Command Prompt**
2. Run:
   ```
   ping <IP address of a PC in another department>
   ```
3. All departments should successfully ping each other

---

## Project Files

| File | Description |
|------|-------------|
| `small business network.pkt` | Cisco Packet Tracer simulation file |
| `problem.png` | Original problem statement |
| `README.md` | This file |

---

## Author

Small Business Network Project  
*XYZ Company – Bonalbo Branch Network Design & Implementation*
