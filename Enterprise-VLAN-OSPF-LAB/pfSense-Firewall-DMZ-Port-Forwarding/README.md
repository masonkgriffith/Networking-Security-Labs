# pfSense Firewall DMZ Lab with Port Forwarding to IIS Server

## Objective

Design and implement a segmented firewall architecture using pfSense with WAN, LAN, and DMZ interfaces. Publish a web server hosted in the DMZ using NAT port forwarding while maintaining secure internal segmentation.

---

## Network Architecture

| Interface | Network | Purpose |
|------------|------------|------------|
| WAN | 192.168.1.100 | External / Upstream Network |
| LAN | 10.0.1.0/24 | Internal User Network |
| DMZ | 10.0.2.0/24 | Public-Facing Server Network |

DMZ Web Server:
- Windows Server
- IIS Installed
- IP: 10.0.2.10

---

## Implementation Steps

### 1. Interface Configuration
- Configured WAN as bridged interface
- Assigned LAN and DMZ static subnets
- Verified interface status and gateway reachability

### 2. Firewall Rules
- Allowed LAN outbound access
- Restricted DMZ to internal network
- Configured WAN rule for HTTP (80) and HTTPS (443)

### 3. NAT Port Forwarding

Created NAT rule:

- Interface: WAN
- Protocol: TCP
- Destination: WAN Address
- Destination Port: 80 → Redirect to 10.0.2.10 (Port 80)
- Destination Port: 443 → Redirect to 10.0.2.10 (Port 443)

Enabled:
- Associated firewall rule auto-generation

---

## Verification & Testing

- Confirmed IIS service listening on ports 80 and 443
- Verified internal LAN access to DMZ server
- Tested external HTTP access via WAN IP
- Validated HTTPS binding configuration
- Confirmed firewall rule hit counters incrementing

---

## Security Concepts Applied

- Network Segmentation
- Perimeter Firewall Enforcement
- Principle of Least Privilege
- DMZ Isolation
- Controlled Service Publishing via NAT

---

## Troubleshooting Performed

- Resolved initial HTTP/HTTPS reachability issue
- Verified IIS bindings
- Confirmed NAT rule mapping
- Ensured WAN interface was properly bridged
- Validated firewall rule ordering

---

## Skills Demonstrated

- Firewall configuration and rule management
- NAT and port forwarding implementation
- DMZ architecture design
- Web service exposure securely
- Traffic validation and troubleshooting
