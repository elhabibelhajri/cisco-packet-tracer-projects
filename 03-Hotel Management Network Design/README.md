# Vic Modern Hotel – Network Design \& Implementation

## Project Overview

This project covers the design and implementation of a complete enterprise network for **Vic Modern Hotel** using **Cisco Packet Tracer**. The hotel spans three floors, each hosting multiple departments, all interconnected through a structured hierarchical network topology.

\---

## Building Layout \& VLAN Configuration

|Floor|Department|VLAN ID|Network Address|
|-|-|-|-|
|**1st Floor**|Reception|80|192.168.8.0/24|
||Store|70|192.168.7.0/24|
||Logistics|60|192.168.6.0/24|
|**2nd Floor**|Finance|50|192.168.5.0/24|
||HR|40|192.168.4.0/24|
||Sales|30|192.168.3.0/24|
|**3rd Floor**|Admin|20|192.168.2.0/24|
||IT|10|192.168.1.0/24|

\---

## Network Topology Summary

|Component|Details|
|-|-|
|**Routers**|3 routers, all located in the IT server room (3rd floor)|
|**Switches**|3 switches — one per floor|
|**Departments**|8 departments across 3 floors, each in its own VLAN|
|**Wireless**|WiFi Access Points on each floor for laptops, phones, and tablets|
|**Inter-Router Links**|Serial DCE cables connecting all three routers|

\---

## Router Interconnection (Serial DCE Links)

|Link|Network|
|-|-|
|F1-Router ↔ F2-Router|10.10.10.0/30|
|F2-Router ↔ F3-Router|10.10.10.4/30|
|F1-Router ↔ F3-Router|10.10.10.8/30|

\---

## Technologies \& Protocols

|Technology|Purpose|
|-|-|
|**VLANs**|Department-level network isolation|
|**Inter-VLAN Routing**|Router-on-a-stick / Layer 3 routing between departments|
|**OSPF**|Dynamic routing between all three routers|
|**DHCP**|Automatic IP assignment per VLAN (each router acts as server)|
|**SSH**|Secure remote login on all routers|
|**Port Security**|Restricts IT switch port `fa0/1` to Test-PC only|
|**WiFi Access Points**|Wireless connectivity per floor|

\---

## Key Configurations

### OSPF

* Enabled on all three routers
* Advertises all directly connected networks
* Ensures full inter-floor and inter-VLAN communication

### DHCP

* Each router serves as the DHCP server for its floor's VLANs
* IP addresses are assigned dynamically to all end devices

### SSH

* Configured on all routers for secure remote management
* **Username:** `habib` | **Password:** `1234`
* Tested from Test-PC in the IT department

### Port Security (IT Switch – `fa0/1`)

* Only **Test-PC** is permitted on port `fa0/1`
* MAC address learned via **sticky** method
* Violation mode: **shutdown**

\---

## Tools Used

|Tool|Purpose|
|-|-|
|**Cisco Packet Tracer**|Network simulation and implementation|

\---

## How to Open the Project

1. Install [Cisco Packet Tracer](https://www.netacad.com/courses/packet-tracer)
2. Open the `.pkt` project file included in this repository
3. Explore the logical topology to view device configurations
4. Use the CLI on any router or switch to inspect running configurations

\---

## Author

End-Year Networking Project  
*Vic Modern Hotel – Network Design \& Implementation*

