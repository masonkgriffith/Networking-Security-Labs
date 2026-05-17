# BGP & EIGRP ISP Network Lab

## Overview
This lab simulates a multi-AS ISP environment using GNS3. Two ISPs each run EIGRP 
internally for intra-AS reachability, and exchange public prefixes via eBGP across 
a public peering link. Route-maps and prefix-lists enforce filtering at the AS boundary 
to prevent internal infrastructure routes from leaking between autonomous systems.

---

## Objectives
- Simulate realistic ISP-to-ISP BGP peering across autonomous systems
- Use EIGRP as the internal IGP to establish next-hop reachability within each AS
- Advertise public IP blocks into BGP using the network command
- Redistribute BGP routes into EIGRP so internal routers learn foreign prefixes
- Enforce outbound and inbound prefix filtering using route-maps and prefix-lists

---

## Topology
- **AS 100 — ISP Alpha:** R1 (boundary), R3 (internal), R5 (internal)
- **AS 200 — ISP Beta:** R2 (boundary), R4 (internal), R6 (internal)
- **eBGP peering link:** 192.0.2.0/30 (R1: 192.0.2.1, R2: 192.0.2.2)


---

## IP Address Plan

| Device | Interface  | Address              | Purpose                  |
|--------|------------|----------------------|--------------------------|
| R1     | e1/0       | 192.0.2.1/30         | eBGP peering link        |
| R2     | e1/0       | 192.0.2.2/30         | eBGP peering link        |
| R1     | e1/1       | 10.0.1.1/30          | EIGRP link to R3         |
| R3     | e1/1       | 10.0.1.2/30          | EIGRP link to R1         |
| R3     | e1/0       | 10.0.2.1/30          | EIGRP link to R5         |
| R5     | e1/0       | 10.0.2.2/30          | EIGRP link to R3         |
| R5     | Lo1        | 203.0.113.129/25     | AS100 public prefix      |
| R2     | e1/1       | 10.0.3.1/30          | EIGRP link to R4         |
| R4     | e1/0       | 10.0.3.2/30          | EIGRP link to R2         |
| R4     | e1/1       | 10.0.4.1/30          | EIGRP link to R6         |
| R6     | e1/0       | 10.0.4.2/30          | EIGRP link to R4         |
| R6     | Lo1        | 198.51.100.129/25    | AS200 public prefix      |

---

## Key Concepts

**Why EIGRP alongside BGP?**
BGP requires next-hop reachability to maintain sessions and forward traffic. EIGRP 
runs internally within each AS so every router can reach its neighbors. Without EIGRP, 
R1 would have no path to R3 or R5 and BGP would have nothing to advertise.

**Why redistribute BGP into EIGRP?**
Internal routers (R3, R5, R4, R6) only run EIGRP. To reach foreign public prefixes 
learned via BGP, the boundary routers redistribute BGP routes into EIGRP so internal 
routers learn them as external EIGRP routes (D EX).

**Why use a null route on boundary routers?**
The BGP network command requires an exact prefix match in the routing table. A null 
route creates that exact match so BGP can advertise the full /24 block even though 
only a /25 exists via EIGRP internally.

**Why route-map and prefix-list filtering?**
Without filtering, internal infrastructure routes (10.0.x.x) would leak across the 
AS boundary. Prefix-lists define permitted prefixes and route-maps apply them inbound 
and outbound on the eBGP neighbor statement — ensuring only public prefixes are 
exchanged between ISPs.

---

## Tools Used
- GNS3
- Cisco IOS routers
- RFC 5737 documentation address space (203.0.113.0/24, 198.51.100.0/24, 192.0.2.0/24)
