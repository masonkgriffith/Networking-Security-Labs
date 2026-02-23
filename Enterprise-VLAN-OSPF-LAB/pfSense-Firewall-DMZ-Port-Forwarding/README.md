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

## Troubleshooting Performed

- Verified IIS service bindings
- Validated NAT rule mapping
- Confirmed RDP port listening
- Adjusted firewall rule ordering
- Tested inter-network restrictions

---

## Skills Demonstrated

- Multi-zone firewall design
- Secure DMZ architecture
- NAT port forwarding configuration
- Granular firewall rule creation
- Administrative access control enforcement
- Traffic validation and policy troubleshooting
