# Enterprise VLAN, OSPF & High-Availability Campus Network Lab

## Objective

Design and implement a hierarchical enterprise campus network incorporating:

- VLAN-based segmentation
- Inter-VLAN routing
- Multi-area OSPF dynamic routing
- Route summarization and filtering
- First-hop redundancy using HSRP
- Structured troubleshooting and validation

This lab simulates a production-style core/distribution/access architecture with high availability and controlled route propagation between network tiers.

The goal is to demonstrate enterprise-grade routing design, redundancy implementation, and convergence validation.

---

## Network Architecture

### Hierarchical Design Model

The lab follows a three-tier enterprise model:

- Core Layer – High-speed routing and backbone connectivity
- Distribution Layer – Policy enforcement, route summarization, redundancy
- Access Layer – VLAN segmentation and endpoint connectivity

---

## VLAN Structure

| VLAN | Name        | Subnet            | Purpose                |
|------|------------|------------------|------------------------|
| 10   | Users      | 10.0.10.0/24     | End-user devices       |
| 20   | Servers    | 10.0.20.0/24     | Internal servers       |
| 30   | Management | 10.0.30.0/24     | Network administration |
| 99   | Native     | N/A              | Trunk native VLAN      |

---

## Core Components

- Dual Layer 3 Distribution Switches
- Edge Router
- OSPF Multi-Area Design
- HSRP for Gateway Redundancy
- Trunked uplinks with controlled VLAN propagation
- DHCP per VLAN

---

## Implementation Overview

### 1. VLAN Segmentation & Access Layer

- Created VLANs on distribution switches
- Configured access ports per department
- Implemented trunk links with restricted allowed VLAN lists
- Verified VLAN tagging and isolation

Broadcast domains were intentionally minimized to reduce Layer 2 exposure.

---

### 2. Inter-VLAN Routing with Redundancy

- Enabled IP routing on both distribution switches
- Created SVIs for each VLAN on both devices
- Implemented HSRP per VLAN

Example HSRP configuration per VLAN:
- Virtual gateway IP assigned
- Active/Standby roles defined
- Priority and preemption configured

Validated:
- Active gateway election
- Seamless failover upon primary shutdown
- Minimal convergence delay

This ensures first-hop redundancy for all client VLANs.

---

### 3. OSPF Multi-Area Configuration

- OSPF Process configured on distribution and core
- Area 0 designated as backbone
- Access/distribution VLANs placed into non-backbone area
- Verified adjacency formation and LSDB consistency

Validated:
- Neighbor state transitions to FULL
- Proper LSA exchange
- Routing table population across tiers

---

### 4. Route Summarization

To reduce routing table size and LSA propagation:

- Implemented summarization at Area Border Router (ABR)
- Summarized VLAN subnets into aggregate routes
- Verified summary routes advertised upstream

Benefits demonstrated:
- Reduced LSDB size
- Faster SPF calculations
- Improved scalability

---

### 5. Route Filtering & Controlled Advertisement

- Implemented prefix filtering to restrict unnecessary route propagation
- Prevented management VLAN from being unnecessarily advertised
- Validated filtering via `show ip route` and `show ip ospf database`

This enforces routing policy boundaries between tiers.

---

## Verification & Validation

- Confirmed inter-VLAN connectivity across redundant gateways
- Verified HSRP failover without traffic interruption
- Observed OSPF neighbor FULL state
- Validated route summarization entries
- Confirmed filtered routes absent from upstream tables
- Measured convergence behavior during simulated link failure

---

## Troubleshooting & Incident Analysis

### 1. OSPF Area Mismatch

Symptoms:
- Neighbor stuck in EXSTART/INIT
- Missing routes

Root Cause:
Mismatched area configuration between ABR and core.

Resolution:
Corrected area assignments and reset OSPF process.
Adjacency formed successfully and routes propagated.

---

### 2. HSRP Failover Validation

Scenario:
Primary distribution switch shut down.

Observed:
- Standby device assumed Active role
- Virtual IP remained reachable
- No gateway reconfiguration required on hosts

Demonstrated enterprise-grade gateway resiliency.

---

### 3. Route Advertisement Leak

Issue:
Unintended VLAN advertisement into backbone.

Resolution:
Applied prefix filtering to limit propagation.
Verified correction via routing table inspection.

---

## Enterprise Design Concepts Applied

- Hierarchical campus model
- First-hop redundancy (HSRP)
- OSPF multi-area design
- Route summarization at ABR
- Controlled route filtering
- Broadcast domain reduction
- Policy enforcement at distribution layer
- Redundancy and convergence validation

---

## Skills Demonstrated

- VLAN and trunk configuration
- Inter-VLAN routing via SVIs
- HSRP implementation and failover testing
- OSPF multi-area configuration
- Route summarization and filtering
- LSDB inspection and route validation
- Redundancy design and convergence analysis
- Structured troubleshooting methodology

---

## Lessons Reinforced

- OSPF area mismatches prevent adjacency establishment
- Route summarization improves scalability and convergence
- HSRP ensures gateway resilience without host reconfiguration
- Filtering prevents unnecessary route propagation
- Enterprise networks require redundancy and policy boundaries
