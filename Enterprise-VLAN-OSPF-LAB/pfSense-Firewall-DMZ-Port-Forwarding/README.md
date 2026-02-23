# pfSense Firewall DMZ Lab with Management Network & Controlled RDP Access

## Objective

Design and implement a segmented firewall architecture using pfSense with WAN, LAN, DMZ, and Management networks. Publish a public IIS web server in the DMZ using NAT port forwarding while restricting administrative access (RDP) exclusively to the Management network.

This lab demonstrates secure perimeter design, administrative isolation, and controlled service exposure.

---

## Network Architecture

| Interface | Network | Purpose |
|------------|------------|------------|
| WAN | 192.168.1.100 | External / Upstream Network |
| LAN | 10.0.1.0/24 | Internal User Network |
| DMZ | 10.0.2.0/24 | Public-Facing Server Network |
| MGMT | 10.0.3.0/24 | Administrative Management Network |

DMZ Web Server:
- Windows Server
- IIS Installed
- IP: 10.0.2.10

Management Workstation:
- IP: 10.0.3.10

---

## Implementation Overview

### 1. Interface Configuration
- Assigned WAN, LAN, DMZ, and MGMT interfaces
- Configured static subnets
- Verified gateway reachability and interface status

---

## 2. NAT Port Forwarding (Public Web Access)

Configured WAN NAT rule:

- Interface: WAN
- Protocol: TCP
- Destination: WAN Address
- Port 80 → Redirect to 10.0.2.10 (HTTP)
- Port 443 → Redirect to 10.0.2.10 (HTTPS)

Enabled automatic firewall rule generation.

Purpose:
Allow external users to access the IIS web server without exposing internal networks.

---

## 3. Firewall Rules & Segmentation

### LAN Rules
- Allow outbound internet access
- Block direct access to DMZ administrative ports

### DMZ Rules
- Restricted outbound traffic
- Prevented lateral movement to LAN and MGMT

### MGMT Rules
Created explicit rule:

- Source: MGMT network
- Destination: 10.0.2.10
- Protocol: TCP
- Port: 3389 (RDP)

This ensures only management hosts can access the DMZ server for administrative tasks.

No other network is permitted RDP access to the DMZ.

---

## Verification & Testing

- Confirmed HTTP/HTTPS access externally via WAN
- Verified LAN users cannot RDP to DMZ server
- Successfully initiated RDP session from MGMT network
- Validated firewall rule hit counters
- Confirmed proper traffic flow using pfSense logs

---

## Security Concepts Applied

- Network Segmentation
- Administrative Isolation
- Principle of Least Privilege
- Perimeter Service Publishing via NAT
- Controlled Access Path Enforcement
- Reduced Attack Surface

---
## Troubleshooting & Incident Analysis

This lab included multiple real-world troubleshooting scenarios involving Layer 2 conflicts, NAT validation, and firewall rule enforcement.

---

### 1. Critical Incident: Duplicate IP Address Conflict (LAN Interface)

**Symptoms Observed:**
- Intermittent loss of connectivity to the default gateway
- Inconsistent ping responses to pfSense LAN interface
- ARP table entries fluctuating between MAC addresses
- Web UI access to pfSense occasionally failing

**Initial Hypothesis:**
Suspected firewall rule misconfiguration or interface instability.

**Diagnostic Process:**
- Verified LAN interface configuration and subnet mask
- Confirmed no packet loss on physical interface
- Checked pfSense system logs for interface errors (none found)
- Used `arp -a` on LAN host to inspect MAC-to-IP resolution
- Identified duplicate MAC mapping for the gateway IP address

Further validation confirmed that the host PC was statically configured with the same IP address as the pfSense LAN interface.

**Root Cause:**
Layer 2 ARP conflict caused by duplicate IP address assignment between pfSense LAN interface and host workstation.

**Impact:**
Gateway instability and unpredictable packet routing due to ARP cache poisoning between competing devices.

**Resolution:**
- Reassigned host to a unique static IP within subnet
- Flushed ARP cache on affected devices
- Verified stable ARP mapping to correct MAC address
- Confirmed consistent gateway reachability and restored connectivity

---

### 2. HTTPS Service Exposure Failure

**Symptoms Observed:**
- HTTP accessible externally
- HTTPS inaccessible via WAN
- Port 443 appeared open but not serving content

**Diagnostic Process:**
- Verified NAT port forwarding configuration
- Confirmed automatic firewall rule generation
- Checked IIS bindings for HTTPS
- Validated certificate binding to correct IP/port
- Tested local access from DMZ server

**Root Cause:**
Improper IIS HTTPS binding configuration prevented service from listening correctly on port 443.

**Resolution:**
- Corrected IIS binding configuration
- Revalidated NAT rule mapping
- Confirmed successful HTTPS access externally

---

### 3. Management Network Access Validation

**Objective:**
Ensure RDP access to DMZ server is restricted exclusively to the MGMT network.

**Testing Performed:**
- Attempted RDP from LAN (blocked as expected)
- Attempted RDP from WAN (blocked as expected)
- Initiated RDP from MGMT network (successful)

**Result:**
Firewall rule ordering and interface segmentation successfully enforced administrative isolation.

---

## Skills Demonstrated

- Multi-zone firewall design
- Secure DMZ architecture
- NAT port forwarding configuration
- Granular firewall rule creation
- Administrative access control enforcement
- Traffic validation and policy troubleshooting
