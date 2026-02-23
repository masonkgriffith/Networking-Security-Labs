# Enterprise VLAN & OSPF Network Lab

## Objective

Design and implement a segmented enterprise campus network using VLANs and dynamic routing (OSPF).  

This lab simulates a multi-department environment with proper broadcast domain isolation, Layer 3 routing, and dynamic route propagation between core and distribution devices.

The goal is to demonstrate structured network design, routing verification, and real-world troubleshooting methodology.

---

## Network Architecture

### VLAN Structure

| VLAN | Name        | Subnet            | Purpose                |
|------|------------|------------------|------------------------|
| 10   | Users      | 10.0.10.0/24     | End-user devices       |
| 20   | Servers    | 10.0.20.0/24     | Internal servers       |
| 30   | Management | 10.0.30.0/24     | Network administration |
| 99   | Native     | N/A              | Trunk native VLAN      |

### Core Components

- Layer 3 Switch (Inter-VLAN Routing)
- Edge Router
- OSPF Area 0 configuration
- Trunk links between switch and router
- DHCP scope configuration per VLAN

---

## Implementation Overview

### 1. VLAN Segmentation

- Created VLANs on Layer 3 switch
- Assigned access ports to appropriate VLANs
- Configured trunk links with allowed VLAN list
- Verified VLAN membership and tagging

---

### 2. Inter-VLAN Routing

- Enabled IP routing on Layer 3 switch
- Created SVIs (Switch Virtual Interfaces) for each VLAN
- Assigned default gateway IP per subnet
- Verified routing table population

---

### 3. OSPF Configuration

- Configured OSPF Process ID
- Assigned interfaces to Area 0
- Advertised VLAN subnets
- Verified neighbor adjacency formation
- Validated LSDB and routing table entries

---

## Verification & Testing

- Confirmed inter-VLAN connectivity via ping
- Verified OSPF neighbor state (FULL)
- Validated routing table entries with `show ip route`
- Confirmed correct default gateway behavior
- Tested subnet isolation and segmentation

---

## Troubleshooting & Incident Analysis

### 1. OSPF Neighbor Adjacency Failure

**Symptoms Observed:**
- OSPF neighbor not reaching FULL state
- Missing routes in routing table
- Inter-VLAN traffic failing beyond local switch

**Diagnostic Process:**
- Verified OSPF network statements
- Checked interface IP addressing
- Confirmed matching subnet masks
- Inspected OSPF area assignments
- Used `show ip ospf neighbor` to analyze adjacency state

**Root Cause:**
Mismatched OSPF area assignment between router and Layer 3 switch prevented adjacency establishment.

**Resolution:**
- Corrected OSPF area configuration
- Reset OSPF process
- Verified neighbor state transitioned to FULL
- Confirmed route propagation across devices

---

### 2. VLAN Misassignment Issue

**Symptoms Observed:**
- Host unable to communicate within same subnet
- No IP address received via DHCP

**Diagnostic Process:**
- Verified access port VLAN assignment
- Checked trunk allowed VLAN list
- Confirmed DHCP scope configuration
- Used `show vlan brief` to validate port membership

**Root Cause:**
Access port assigned to incorrect VLAN, preventing correct broadcast domain participation.

**Resolution:**
- Reassigned correct VLAN to access port
- Renewed DHCP lease
- Verified successful connectivity

---

## Security & Design Concepts Applied

- Broadcast domain reduction via VLAN segmentation
- Logical separation of user, server, and management traffic
- Controlled route advertisement
- Minimization of unnecessary Layer 2 exposure
- Structured network hierarchy (Core/Distribution/Access concept)

---

## Skills Demonstrated

- VLAN creation and trunk configuration
- Inter-VLAN routing via SVIs
- Dynamic routing protocol implementation (OSPF)
- Route verification and LSDB inspection
- Layer 2 and Layer 3 troubleshooting
- Structured incident analysis and resolution
- Network segmentation design

---

## Lessons Reinforced

- OSPF area mismatches prevent adjacency formation
- VLAN misconfiguration can silently break broadcast domains
- Routing validation requires both neighbor and route inspection
- Proper segmentation reduces unnecessary traffic propagation
