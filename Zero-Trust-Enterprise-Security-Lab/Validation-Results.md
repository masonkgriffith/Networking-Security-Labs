# Zero-Trust Enterprise Network Security Lab  
Segmentation | DMZ Hardening | IDS/IPS | Lateral Movement Containment

## Overview

This lab simulates a segmented enterprise network built using Zero-Trust design principles.

The objective was not simply to configure VLANs and firewall rules, but to:

- Architect strict east-west isolation between security zones  
- Expose a controlled DMZ service  
- Restrict server egress traffic  
- Deploy IDS/IPS inspection  
- Simulate adversarial reconnaissance  
- Validate lateral movement containment through logged enforcement  

All controls were intentionally tested using internal scan techniques to confirm policy effectiveness.

---

## Architecture Design

### Network Segmentation (Zone-Based Model)

| Zone    | Purpose                  | Subnet         |
|---------|--------------------------|---------------|
| VLAN10  | Management               | 10.0.10.0/24  |
| VLAN20  | User Workstations        | 10.0.20.0/24  |
| VLAN30  | Internal Servers         | 10.0.30.0/24  |
| VLAN40  | DMZ (Public Services)    | 10.0.40.0/24  |
| VLAN50  | Security Tools           | 10.0.50.0/24  |
| VLAN99  | Untrusted / Guest        | 10.0.99.0/24  |

---

## Core Security Controls Implemented

### 1. Explicit Inter-VLAN Deny Policy (Zero-Trust Enforcement)

No VLAN trusts another by default.  
All east-west traffic requires explicit allow rules.

Example enforcement rule:

Block Users → INTERNAL_VLANS

Result:
- User segment cannot enumerate servers
- User segment cannot access DMZ directly
- Lateral movement is contained at the firewall

All deny rules are logged for visibility and validation.

---

### 2. Controlled DMZ Exposure

A Windows Server running IIS was deployed in the DMZ (10.0.40.2).

WAN access was configured via:

- NAT port forward (TCP 80)
- Explicit WAN firewall rule
- No internal trust to DMZ

Internal VLANs cannot access DMZ directly.  
Only WAN-originated traffic reaches the public service.

---

### 3. Server Egress Restriction

Internal servers are restricted to:

- TCP 80
- TCP 443

All other outbound traffic is denied.

This prevents:
- Reverse shell callbacks
- Arbitrary beaconing
- Unauthorized service communication

---

### 4. IDS/IPS Deployment

Suricata was deployed and configured with:

- ET Open rule set
- HOME_NET properly defined
- Inline inspection enabled
- Block offenders policy configured

IDS inspection was tested alongside firewall policy validation to observe detection and enforcement behavior.

---

## Adversarial Validation Testing

To validate segmentation controls, a simulated compromised endpoint was deployed in the User VLAN (10.0.20.10).

### Test 1: DMZ Subnet Reconnaissance

Command executed:

nmap -sS -Pn 10.0.40.0/24

Observed behavior:

- ICMP echo requests blocked
- TCP SYN packets to port 80 blocked
- No TCP handshake completed
- No host or port enumeration successful

Firewall logs confirmed:

- Source: 10.0.20.10  
- Destination: 10.0.40.x  
- Rule matched: Block Users → INTERNAL_VLANS  

Result:
Reconnaissance attempts were silently contained, preventing lateral movement from user to DMZ zone.

---

### Test 2: Service Enumeration Attempt

Additional scan techniques were tested:

- SYN scan
- Full port sweep
- Targeted service scan

All attempts failed due to explicit inter-VLAN deny policy.

---

## Technical Outcome

This lab demonstrates:

- Zero-Trust segmentation design  
- Explicit east-west deny enforcement  
- Controlled public service exposure  
- Restricted server egress  
- Logged policy validation  
- Simulated lateral movement containment  

Rather than focusing only on configuration, this lab emphasizes validation of defensive controls through adversarial testing.

---

## Lessons Learned

- Segmentation must be explicitly enforced, not assumed.
- East-west containment is as critical as perimeter defense.
- Firewall logs provide actionable telemetry for validating policy effectiveness.
- Virtualized IPS deployments may introduce inspection limitations depending on NIC drivers and hypervisor behavior.
