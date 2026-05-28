\# Vic Modern Hotel – Network Design \& Implementation



\## Project Overview



This project involves the design and implementation of a complete enterprise

network for Vic Modern Hotel using Cisco Packet Tracer. The hotel spans three

floors, each hosting multiple departments, all interconnected through a

structured hierarchical network topology.



\---



\## Network Topology Summary



\- \*\*3 Routers\*\* (all located in the IT server room, 3rd floor)

\- \*\*3 Switches\*\* (one per floor)

\- \*\*8 Departments\*\* across 3 floors, each in its own VLAN

\- \*\*WiFi Access Points\*\* on each floor for wireless devices

\- \*\*Printers\*\* in every department

\- \*\*Inter-router links\*\* via Serial DCE cables



\---



\## Floor \& VLAN Configuration



| Floor | Department | VLAN ID | Network Address |

|-------|------------|---------|-----------------|

| 1st | Reception | 80 | 192.168.8.0/24 |

| 1st | Store | 70 | 192.168.7.0/24 |

| 1st | Logistics | 60 | 192.168.6.0/24 |

| 2nd | Finance | 50 | 192.168.5.0/24 |

| 2nd | HR | 40 | 192.168.4.0/24 |

| 2nd | Sales | 30 | 192.168.3.0/24 |

| 3rd | Admin | 20 | 192.168.2.0/24 |

| 3rd | IT | 10 | 192.168.1.0/24 |



\---



\## Router Interconnection (Serial DCE Links)



| Link | Network |

|------|---------|

| F1-Router ↔ F2-Router | 10.10.10.0/30 |

| F2-Router ↔ F3-Router | 10.10.10.4/30 |

| F1-Router ↔ F3-Router | 10.10.10.8/30 |



\---



\## Technologies \& Protocols Used



| Technology | Purpose |

|------------|---------|

| VLANs | Department isolation |

| Inter-VLAN Routing | Router-on-a-stick / Layer 3 routing |

| OSPF | Dynamic routing between routers |

| DHCP | Automatic IP assignment per VLAN (router as server) |

| SSH | Secure remote login on all routers |

| Port Security | Restrict IT switch port fa0/1 to Test-PC only (sticky MAC, shutdown) |

| WiFi (Access Points) | Wireless connectivity for laptops, phones, tablets |



\---



\## Key Configurations



\### OSPF

\- Enabled on all three routers

\- Advertises all directly connected networks

\- Ensures full inter-floor and inter-VLAN communication



\### DHCP

\- Each router serves as the DHCP server for its floor's VLANs

\- IP addresses are assigned dynamically to all end devices



\### SSH

\- Configured on all routers for secure remote management

\- Username: `habib` | Password: `1234`

\- Tested from Test-PC in the IT department



\### Port Security (IT Switch – fa0/1)

\- Only Test-PC is allowed on port fa0/1

\- MAC address learned via \*\*sticky\*\* method

\- Violation mode: \*\*shutdown\*\*



\---



\## Tools Used



\- \*\*Cisco Packet Tracer\*\* – Network simulation and implementation



\---



\## How to Open the Project



1\. Install \[Cisco Packet Tracer](https://www.netacad.com/courses/packet-tracer)

2\. Open the `.pkt` project file included in this repository

3\. Explore the logical topology to view device configurations

4\. Use the CLI of any router/switch to inspect running configurations



\---



\## Author



End-Year Networking Project  

Vic Modern Hotel Network Design \& Implementation

