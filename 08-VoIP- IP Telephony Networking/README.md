# Enterprise VoIP and Dial-Peering Network — Turtle Consultancy Limited

## Project Overview

This project involves the design and implementation of a full-scale VoIP (IP Telephony) network infrastructure for Turtle Consultancy Limited using Cisco Packet Tracer. The network interconnects four departments across a single enterprise environment, integrating voice and data services over a converged IP infrastructure.

---

## Network Topology

The network is divided into four departments, each operating as an independent LAN segment connected to a central routing infrastructure via serial links. A dedicated server room is attached to the ICT department router.

### Departments

| Department | Data VLAN | Voice VLAN | Data Subnet        | Voice Subnet       | Dial Plan |
|------------|-----------|------------|--------------------|--------------------|-----------|
| Finance    | VLAN 10   | VLAN 100   | 192.168.100.0/27   | 172.16.100.0/27    | 1xx       |
| HR         | VLAN 20   | VLAN 100   | 192.168.100.32/27  | 172.16.100.32/27   | 2xx       |
| Sales      | VLAN 30   | VLAN 100   | 192.168.100.64/27  | 172.16.100.64/27   | 3xx       |
| ICT        | VLAN 40   | VLAN 100   | 192.168.100.96/27  | 172.16.100.96/27   | 4xx       |

### Inter-Router Links (10.10.10.0/24)

| Link                  | Subnet           |
|-----------------------|------------------|
| Finance — ICT Router  | 10.10.10.0/30    |
| HR — ICT Router       | 10.10.10.4/30    |
| Sales — ICT Router    | 10.10.10.8/30    |

---

## Device Inventory

### Per Department

- 1x Cisco 2811 Router (VoIP-enabled)
- 1x Cisco 2960 Switch
- 20x PCs (connected directly to IP Phones)
- 20x IP Phones (each connected to a PC downstream)
- 1x Network Printer

### Server Room (attached to ICT Router)

| Server | Role    |
|--------|---------|
| HTTP   | Web Server |
| DNS    | Domain Name Resolution |
| Email  | Mail Server |
| DHCP   | IP Address Assignment for Data VLANs |

All server addresses are statically assigned.

---

## IP Addressing

### Address Pools

| Purpose          | Network           |
|------------------|-------------------|
| Data VLANs       | 192.168.100.0/24  |
| Voice VLANs      | 172.16.100.0/24   |
| Inter-router WAN | 10.10.10.0/24     |

### Subnetting (per department, /27 — 30 usable hosts)

Each department receives one /27 subnet for data and one /27 subnet for voice, providing 30 usable addresses per segment, sufficient for 20 PCs or 20 phones plus network overhead.

---

## Configuration Summary

### 1. Basic Device Settings

Applied to all routers and switches:

- Hostname
- Console password
- Enable secret password
- Banner MOTD
- Password encryption (`service password-encryption`)
- IP domain-lookup disabled (`no ip domain-lookup`)

### 2. VLANs

Each switch is configured with two VLANs:

- Data VLAN (department-specific: 10, 20, 30, or 40)
- Voice VLAN 100 (shared across all departments)

Access ports connect PCs via IP Phones; trunk ports uplink to the router.

```
! Example — Finance Switch
vlan 10
 name FINANCE_DATA
vlan 100
 name VOICE

interface FastEthernet0/1
 switchport mode access
 switchport access vlan 10
 switchport voice vlan 100
 spanning-tree portfast
```

### 3. Inter-VLAN Routing (Router-on-a-Stick)

Each router uses subinterfaces on the LAN-facing interface to route between the data and voice VLANs.

```
! Example — Finance Router
interface FastEthernet0/0.10
 encapsulation dot1Q 10
 ip address 192.168.100.1 255.255.255.224

interface FastEthernet0/0.100
 encapsulation dot1Q 100
 ip address 172.16.100.1 255.255.255.224
```

### 4. DHCP Configuration

**Data DHCP:** Handled by the centralized DHCP server in the server room. IP helper-address is configured on each router's data subinterface to forward DHCP requests.

```
interface FastEthernet0/0.10
 ip helper-address <DHCP_SERVER_IP>
```

**Voice DHCP:** Each department router acts as the local DHCP server for its Voice VLAN, with Option 150 (TFTP server) pointing to itself.

```
! Example — Finance Router Voice DHCP
ip dhcp pool FINANCE_VOICE
 network 172.16.100.0 255.255.255.224
 default-router 172.16.100.1
 option 150 ip 172.16.100.1
```

### 5. Routing Protocol — OSPF

OSPF is used to advertise all connected networks across routers. All interfaces participate in Area 0.

```
router ospf 1
 network 192.168.100.0 0.0.0.31 area 0
 network 172.16.100.0 0.0.0.31 area 0
 network 10.10.10.0 0.0.0.3 area 0
```

### 6. Remote Access — SSH

SSH is configured on all routers for secure remote management.

```
ip domain-name turtle.local
crypto key generate rsa modulus 1024
username admin privilege 15 secret <password>
line vty 0 4
 login local
 transport input ssh
```

### 7. VoIP — Telephony Service

Each router is configured as a Cisco Unified Communications Manager Express (CME) node.

```
! Example — Finance Router
telephony-service
 max-ephones 20
 max-dn 20
 ip source-address 172.16.100.1 port 2000
 auto assign 1 to 20

ephone-dn 1
 number 101

ephone-dn 2
 number 102
```

Dial plan ranges:

| Department | Number Range |
|------------|--------------|
| Finance    | 101 – 199    |
| HR         | 201 – 299    |
| Sales      | 301 – 399    |
| ICT        | 401 – 499    |

### 8. Dial-Peering (Inter-Router VoIP Routing)

Dial peers allow IP phones on different routers to reach each other using VoIP.

```
! Example — Finance Router: route calls to HR (2xx)
dial-peer voice 2 voip
 destination-pattern 2..
 session target ipv4:<HR_ROUTER_VOICE_IP>

! Route calls to Sales (3xx)
dial-peer voice 3 voip
 destination-pattern 3..
 session target ipv4:<SALES_ROUTER_VOICE_IP>

! Route calls to ICT (4xx)
dial-peer voice 4 voip
 destination-pattern 4..
 session target ipv4:<ICT_ROUTER_VOICE_IP>
```

Each router must have dial peers pointing to all other routers.

---

## Testing and Verification

| Test                                  | Expected Result                              |
|---------------------------------------|----------------------------------------------|
| PC obtains IP from DHCP server        | Address in correct data subnet               |
| IP Phone obtains IP from router DHCP  | Address in voice subnet with Option 150      |
| Intra-department phone call           | Call completes successfully                  |
| Inter-department phone call           | Call routes via dial-peer and completes      |
| PC pings server room devices          | Successful reply                             |
| HTTP access to web server             | Page loads in browser                        |
| Email client connects to mail server  | Sends and receives mail                      |
| DNS resolution                        | Hostnames resolve to correct IPs             |
| SSH login to router                   | Remote session established                   |
| OSPF neighbor relationships           | All routers show neighbors as FULL state     |

---

## Tools Used

- Cisco Packet Tracer
- Cisco 2811 Router
- Cisco 2960 Switch
- Cisco IP Phone 7960
- Cisco PC-PT and Printer-PT

---

## Author

Elhabib Elhajri
Networking Engineering Student — ISIMA Mahdia
CCNA 1 Certified | CCNA 2 In Progress
