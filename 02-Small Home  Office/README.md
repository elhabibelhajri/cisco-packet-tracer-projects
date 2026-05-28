\# Small Business Network - XYZ Company Branch



\## Problem Statement

!\[Problem Statement](problem.png)



\## Overview

Branch network design for XYZ Company (a food items company based in Eastern Australia).

The branch is located in Bonalbo village and operates separately from the HQ network.

Built using Cisco Packet Tracer.



\## Requirements

\- 1 Router and 1 Switch (all Cisco products)

\- 3 Departments each in a separate VLAN:

&#x20; - Admin/IT

&#x20; - Finance/HR

&#x20; - Customer Service/Reception

\- Each department has wireless network access

\- All host devices obtain IP addresses automatically (DHCP)

\- All departments can communicate with each other

\- Base network address: 192.168.1.0



\## Network Topology

\- 1 Cisco Router

\- 1 Cisco Switch

\- 3 VLANs (one per department)

\- 3 Wireless Access Points (one per department)

\- DHCP enabled for all devices



\## VLAN Plan

| VLAN | Department              | Network          |

|------|-------------------------|------------------|

| 10   | Admin/IT                | 192.168.1.0/26   |

| 20   | Finance/HR              | 192.168.1.64/26  |

| 30   | Customer Service        | 192.168.1.128/26 |



\## How to Test

Open any PC → Desktop → Command Prompt

ping \[IP of PC in another department]

All departments should be able to ping each other.



\## Files

\- `small business network.pkt` — Packet Tracer file

\- `problem.png` — Problem statement

