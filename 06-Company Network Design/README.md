# Enterprise Network Design — Trading Floor Support Centre

## Project Overview

This project presents the design and implementation of a complete enterprise network for a trading floor support centre relocating 600 staff to a new three-floor building. The network was designed and simulated using Cisco Packet Tracer, following a hierarchical model with full redundancy at every layer.

\---

## Building Layout

|Floor|Departments|Users|
|-|-|-|
|First Floor|Sales \& Marketing / Human Resource \& Logistics|120 + 120|
|Second Floor|Finance \& Accounts / Administrator \& Public Relations|120 + 120|
|Third Floor|ICT / Server Room|120 + 12 devices|

## Devices

|Device|Model|Role|
|-|-|-|
|Routers|Cisco 2911|Edge routing, PAT, ISP connectivity|
|Multilayer Switches|Cisco 3650-24PS|Inter-VLAN routing, OSPF, core switching|
|Access Switches|Cisco 2960-24TT|VLAN access, port security|
|Access Points|Cisco AP-PT|Wireless access per department|
|Servers|Server-PT|DHCP, HTTPS, Email (static IPs)|

\---

## IP Addressing

### Base Network: 172.16.1.0

Subnetting was performed to allocate a /25 subnet (126 usable hosts) to each department, satisfying the 120-user requirement with room for growth.

|VLAN|Department|Network|Mask|Gateway|
|-|-|-|-|-|
|10|Sales \& Marketing|172.16.1.0|255.255.255.128|172.16.1.1|
|20|Human Resource|172.16.1.128|255.255.255.128|172.16.1.129|
|30|Logistics|172.16.2.0|255.255.255.128|172.16.2.1|
|40|Finance \& Accounts|172.16.2.128|255.255.255.128|172.16.2.129|
|50|Administrator|172.16.3.0|255.255.255.128|172.16.3.1|
|60|Public Relations|172.16.3.128|255.255.255.128|172.16.3.129|
|70|ICT|172.16.4.0|255.255.255.128|172.16.4.1|
|80|Server Room|172.16.4.128|255.255.255.128|172.16.4.129|

### WAN Links — Public IP Addresses

|Link|Network|
|-|-|
|Router 1 to ISP 1|195.136.17.0/30|
|Router 1 to ISP 2|195.136.17.4/30|
|Router 2 to ISP 1|195.136.17.8/30|
|Router 2 to ISP 2|195.136.17.12/30|

\---

## Configuration Details

### Basic Device Settings

All devices are configured with the following baseline settings:

* Hostname identifying the device role and location
* Console and enable (secret) passwords
* Banner MOTD: `Only authorized users are allowed`
* `no ip domain-lookup` to prevent DNS lookup delays
* `ip domain-name cisco.net`

### VLAN Configuration

Each department is assigned a dedicated VLAN on its access switch. Uplinks between access switches and the multilayer switches are configured as 802.1Q trunk ports.

### Inter-VLAN Routing

Inter-VLAN routing is handled entirely by the multilayer switches using Switched Virtual Interfaces (SVIs). Each VLAN interface is assigned the gateway address for that subnet.

```
interface Vlan10
 ip address 172.16.1.1 255.255.255.128
 no shutdown
```

### DHCP

A dedicated DHCP server located in the Server Room serves dynamic IP addresses to all departments. Each multilayer switch SVI is configured with `ip helper-address` pointing to the DHCP server, enabling DHCP relay across VLANs. A separate pool is defined on the server for each VLAN.

```
interface Vlan10
 ip helper-address <DHCP\_SERVER\_IP>
```

### Wireless

Each department is equipped with a dedicated wireless access point connected to the access layer switch on the correct VLAN, providing wireless coverage for all users.

### OSPF

OSPF process 10 is configured on all routers and multilayer switches to dynamically advertise and exchange routes across the entire network.

```
router ospf 10
 network 172.16.0.0 0.0.255.255 area 0
 network 10.10.10.0 0.0.0.255 area 0
```

### SSH

SSH is configured on all routers and Layer 3 switches for secure remote management.

```
ip domain-name cisco.net
username cisco secret cisco
crypto key generate rsa modulus 1024
line vty 0 4
 login local
 transport input ssh
```

### Port Security — Finance \& Accounts (VLAN 40)

Each access port in the Finance \& Accounts department is restricted to a single MAC address using the sticky method. Any violation triggers an automatic shutdown of the port.

```
switchport port-security
switchport port-security maximum 1
switchport port-security mac-address sticky
switchport port-security violation shutdown
```

### PAT (Port Address Translation)

PAT is configured on both routers to allow all internal hosts to access the internet through the router's outbound interface IP address. An ACL is applied to define the traffic subject to translation.

```
access-list 1 permit 172.16.0.0 0.0.255.255

ip nat inside source list 1 interface GigabitEthernet0/0 overload

interface GigabitEthernet0/0
 ip nat outside

interface GigabitEthernet0/1
 ip nat inside
```

\---

## Verification

|Test|Command|Expected Result|
|-|-|-|
|VLAN gateway reachable|ping <gateway>|Success|
|Inter-VLAN routing|ping <host in another VLAN>|Success|
|DHCP assignment|ipconfig /renew|IP dynamically assigned|
|OSPF adjacency|show ip ospf neighbor|Neighbors listed|
|SSH remote login|ssh -l cisco <device IP>|Login prompt appears|
|Port security|Connect second device|Port enters err-disabled state|
|Internet connectivity|ping 8.8.8.8|Success through PAT|

\---

## Project Files

```
Enterprise-Network-Design/
    README.md
    Company\_Network\_Design.pdf
    network\_topology.pkt
```

\---

## Author

El Hajri El Habib
Student — Réseaux et Systèmes, ISIMA Mahdia
Vice President, Code Bey Club
CCNA 1 Certified | CCNA 2 In Progress

Academic Year: 2025 – 2026

