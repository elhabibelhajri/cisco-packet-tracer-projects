# Simple Networking Project

## Project Overview

A basic two-department network connecting **Accounts** and **Delivery** via a single router. Built and simulated using **Cisco Packet Tracer**.

---

## Network Topology Summary

| Component | Details |
|-----------|---------|
| **Router** | 1 × Router |
| **Switches** | 2 × Switches — one per department |
| **PCs** | 4 × PCs — 2 per department |

---

## IP Addressing

Base address: `192.168.40.0/24` — split into two `/25` subnets.

| Department | Subnet | Usable Range | Gateway | Broadcast |
|------------|--------|--------------|---------|-----------|
| **Accounts** | 192.168.40.0/25 | .1 – .126 | 192.168.40.1 | .127 |
| **Delivery** | 192.168.40.128/25 | .129 – .254 | 192.168.40.129 | .255 |

---

## Testing Communication

To verify connectivity between departments:

1. Open a **Delivery PC** → **Desktop** → **Command Prompt**
2. Run:
   ```
   ping 192.168.40.2
   ```
3. A successful reply confirms inter-department routing is working

---

## Project Files

| File | Description |
|------|-------------|
| `Simple Networking Project.pkt` | Cisco Packet Tracer simulation file |
| `Simple_Networking_Project.png` | Original problem statement |
| `README.md` | This file |

---

## Author

Simple Networking Project  
*Accounts & Delivery Department Network – Cisco Packet Tracer*
