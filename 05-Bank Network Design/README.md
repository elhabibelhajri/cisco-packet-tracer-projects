# Enterprise Network Design – Radeon Company Ltd. (Nairobi Branch)

## Project Overview

This project covers the design and implementation of a hierarchical enterprise network for **Radeon Company Ltd.**, a US-owned Banking and Insurance company establishing its first African branch in **Nairobi, Kenya**. The network spans a **four-story building** housing 10 departments plus a dedicated server room.

\---

## Building Layout \& Departments

|Floor|Departments|PCs|Printers|Servers|
|-|-|-|-|-|
|**1st Floor**|Management, Research, Human Resource|20 each|4 each|—|
|**2nd Floor**|Marketing, Accounting, Finance|20 each|4 each|—|
|**3rd Floor**|Logistics \& Store, Customer Care, Guest Area|20 / 20 / 40|4 / 4 / 2|—|
|**4th Floor**|Administration, ICT, Server Room|20 / 20 / 2|2 / 2 / 3|DHCP, HTTP, Email|

> Each department (excluding the server room) is expected to support approximately \*\*60 users\*\* (wired + wireless combined).

\---

## Network Design

### Topology Model

* **Design Pattern:** Hierarchical Network Design (Core → Distribution → Access layers)
* **Redundancy:** Redundant links included at core and distribution layers
* **Modeling Tool:** MS Visio / Visual Paradigm / Draw.io
* **Simulation Tool:** Cisco Packet Tracer / GNS3

### Physical Layout

* **One router per floor**, interconnecting all switches on that floor
* **One switch per department** at the access layer
* **Wireless Access Points (WAPs)** deployed in every department
* Dedicated **Server Room** on the 4th floor hosting all centralized services

### Routing Protocol

* **OSPF (Open Shortest Path First)** configured on all routers for dynamic route advertisement

\---

## VLAN Configuration

Each department is isolated in its own VLAN:

|VLAN ID|Department|
|-|-|
|VLAN 10|Management|
|VLAN 20|Research|
|VLAN 30|Human Resource|
|VLAN 40|Marketing|
|VLAN 50|Accounting|
|VLAN 60|Finance|
|VLAN 70|Logistics \& Store|
|VLAN 80|Customer Care|
|VLAN 90|Guest Area|
|VLAN 100|Administration|
|VLAN 110|ICT|
|VLAN 120|Server Room|

* All inter-VLAN routing is handled at the distribution/core layer
* Trunk links carry all VLANs between switches and routers

\---

## IP Addressing \& Subnetting

**Base Address:** `192.168.10.0`  
Subnetting is performed based on the number of hosts per department (\~60 wired/wireless users). A `/26` subnet (62 usable hosts) is used for standard departments; `/25` (126 usable hosts) for the Guest Area.

|Dept.|VLAN|Subnet|Mask|Usable Range|Broadcast|
|-|-|-|-|-|-|
|Management|10|192.168.10.0|/26|.1 – .62|.63|
|Research|20|192.168.10.64|/26|.65 – .126|.127|
|Human Resource|30|192.168.10.128|/26|.129 – .190|.191|
|Marketing|40|192.168.10.192|/26|.193 – .254|.255|
|Accounting|50|192.168.11.0|/26|.1 – .62|.63|
|Finance|60|192.168.11.64|/26|.65 – .126|.127|
|Logistics \& Store|70|192.168.11.128|/26|.129 – .190|.191|
|Customer Care|80|192.168.11.192|/26|.193 – .254|.255|
|Guest Area|90|192.168.12.0|/25|.1 – .126|.127|
|Administration|100|192.168.12.128|/26|.129 – .190|.191|
|ICT|110|192.168.12.192|/26|.193 – .254|.255|
|Server Room|120|192.168.13.0|/26|.1 – .62|.63|

> All end devices obtain IP addresses dynamically via DHCP from the centralized \*\*DHCP server\*\* in the Server Room.

\---

## Server Configuration

All servers are located in the **Server Room (4th Floor)**:

|Server|Role|
|-|-|
|**DHCP Server**|Assigns dynamic IP addresses to all network hosts|
|**HTTP Server**|Hosts the internal company web portal|
|**Email Server**|Handles internal corporate email communication|

\---

## Device Basic Configuration (All Routers \& Switches)

```
hostname <device-name>
no ip domain-lookup
banner motd # Unauthorized Access is Prohibited #
line console 0
  password <console-password>
  login
line vty 0 4
  password <vty-password>
  login
  transport input ssh
enable secret <enable-password>
service password-encryption
```

### SSH Remote Login (All Routers)

```
ip domain-name radeon.local
crypto key generate rsa modulus 1024
ip ssh version 2
line vty 0 4
  transport input ssh
```

\---

## Port Security Configuration (Access Layer Switches)

Applied on all access ports connecting end devices:

```
switchport mode access
switchport port-security
switchport port-security maximum 1
switchport port-security mac-address sticky
switchport port-security violation shutdown
```

\---

## Wireless Network

* Each department has at least **one Wireless Access Point (WAP)**
* WAPs are configured per VLAN to ensure wireless users remain within their department's subnet
* SSID named after the department (e.g., `RADEON\_MGMT`, `RADEON\_MKTG`)

\---

## Testing \& Verification

|Test|Expected Result|
|-|-|
|Ping within same VLAN|✅ Success|
|Ping across different VLANs|✅ Success (via inter-VLAN routing)|
|DHCP lease from Server Room|✅ All hosts receive dynamic IP|
|SSH login to routers|✅ Remote access confirmed|
|HTTP server access|✅ Reachable from all VLANs|
|Email server access|✅ Reachable from all VLANs|
|Port security violation|✅ Port shuts down on MAC violation|

\---

## Tools Used

|Purpose|Tool|
|-|-|
|Network Topology Diagram|Draw.io / MS Visio / Visual Paradigm|
|Network Simulation|Cisco Packet Tracer / GNS3|
|Routing Protocol|OSPF|
|IP Management|Manual subnetting from 192.168.10.0|

\---

Final-year networking students – Local University, Nairobi  
*Project commissioned by Radeon Company Ltd.*

