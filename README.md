# HabeebSec Enterprise — Network Infrastructure & Security Lab

## Project Overview

This project documents the design, implementation, security hardening, troubleshooting, and validation of an enterprise network infrastructure for a fictional organization, **HabeebSec Enterprise**.

The environment was designed and implemented in **Cisco Packet Tracer** to demonstrate practical enterprise networking and security engineering skills, including network segmentation, inter-VLAN routing, network services, access control, device management security, endpoint protection, and structured troubleshooting.

The project follows a practical engineering workflow:

> **Design → Implement → Secure → Validate → Troubleshoot → Document**

---

## Business Requirements

The network was designed to support:

- Multiple organizational departments
- 43 employee workstation capacity
- Internal server infrastructure
- Guest network access
- Department-level network segmentation
- Controlled inter-department communication
- Centralized network services
- Secure network-device administration
- Fault isolation and troubleshooting
- Future scalability

### Implementation Scope

The business requirement specifies capacity for **43 employee workstations**.

For the Packet Tracer implementation, a representative subset of **10 client/guest endpoints** was deployed and validated:

- 2 Management endpoints
- 2 IT endpoints
- 5 Staff endpoints
- 1 Guest endpoint

The implementation uses `/24` networks to provide sufficient address capacity for the intended larger deployment.

---

## Network Architecture

The architecture uses a hierarchical design consisting of:

- 1 Router
- 1 Core Layer-3-capable switch
- 3 Department access switches
- 1 Server access switch
- 4 Servers
- Representative client endpoints

### Core Components

| Device | Role |
|---|---|
| R1 | Inter-VLAN routing, DHCP relay, ACL enforcement, SSH management |
| CORE-SW1 | Core switching and VLAN distribution |
| ACC-MGMT | Management department access switch |
| ACC-IT | IT department access switch |
| ACC-STAFF | Staff and Guest access switch |
| SERVER-SW1 | Server network access switch |

---

## VLAN Segmentation

The network is segmented using VLANs to reduce unnecessary broadcast traffic, improve security, simplify administration, and provide controlled communication between network zones.

| VLAN | Name | Network | Gateway |
|---:|---|---|---|
| 10 | Management | 192.168.10.0/24 | 192.168.10.1 |
| 20 | IT | 192.168.20.0/24 | 192.168.20.1 |
| 30 | Staff | 192.168.30.0/24 | 192.168.30.1 |
| 40 | Servers | 192.168.40.0/24 | 192.168.40.1 |
| 50 | Guest | 192.168.50.0/24 | 192.168.50.1 |

The VLAN numbering intentionally corresponds with the third octet of the IP addressing scheme to make the network easier to understand, administer, and troubleshoot.

---

## IP Addressing

The addressing scheme follows a structured private IPv4 design using the `192.168.0.0/16` private address space.

### Key Infrastructure Addresses

| Device | IP Address |
|---|---|
| R1 VLAN 10 Gateway | 192.168.10.1 |
| R1 VLAN 20 Gateway | 192.168.20.1 |
| R1 VLAN 30 Gateway | 192.168.30.1 |
| R1 VLAN 40 Gateway | 192.168.40.1 |
| R1 VLAN 50 Gateway | 192.168.50.1 |
| CORE-SW1 Management | 192.168.10.10 |
| ACC-MGMT Management | 192.168.10.11 |
| ACC-IT Management | 192.168.10.12 |
| ACC-STAFF Management | 192.168.10.13 |
| SERVER-SW1 Management | 192.168.10.14 |

### Server Infrastructure

| Server | Address | Function |
|---|---|---|
| SRV-DHCP | 192.168.40.10 | DHCP |
| SRV-DNS | 192.168.40.11 | DNS |
| SRV-FILE | 192.168.40.12 | FTP/File Services |
| SRV-MGMT | 192.168.40.13 | Management Server |

---

## Network Services

The environment implements and validates several core enterprise network services.

### DHCP

DHCP is centralized on `SRV-DHCP`.

Router-on-a-stick subinterfaces use DHCP relay with:

```text
ip helper-address 192.168.40.10
...

DHCP scopes were configured for the required VLANs and tested from representative endpoints.

### DNS

DNS is provided by `SRV-DNS`.

The following internal record was configured:

files.habeebsec.local → 192.168.40.12

Hostname resolution was validated using `nslookup`.

### FTP

`SRV-FILE` provides FTP services.

User authentication and directory access were tested from authorized network segments.

### SSH

SSH provides secure remote administration of network infrastructure.

SSH was configured on:

- R1
- CORE-SW1
- ACC-MGMT
- ACC-IT
- ACC-STAFF
- SERVER-SW1

Device-specific administrative accounts were used rather than relying on shared credentials.

---

## Security Controls

The network incorporates multiple defensive controls.

### 1. VLAN Segmentation

Network zones are separated into Management, IT, Staff, Servers, and Guest VLANs.

### 2. Extended ACLs

Extended IPv4 ACLs enforce communication policies between network zones.

The ACL design follows the principle of:

> Allow required communication and deny unnecessary access.

Controls include:

- Guest isolation
- Department-level restrictions
- Management access restrictions
- Server access controls
- SSH management restrictions
- DNS and FTP service permissions

### 3. SSH Management

Telnet was not used for administrative access.

SSH was configured with:

- Local authentication
- Privilege level 15 administrative accounts
- RSA keys
- VTY restrictions
- Session timeout

### 4. Switch Port Security

Port security was implemented on endpoint-facing switch ports using:

- Maximum one secure MAC address
- Sticky MAC learning
- Shutdown violation action

Port security was intentionally not applied to trunk/uplink interfaces.

### 5. Management Plane Protection

Guest users were explicitly prevented from accessing the router's SSH management service.

---

## Access Control Policy

The security policy was designed around business requirements and least-privilege principles.

| Source | Key Access Policy |
|---|---|
| Management | Authorized access to required infrastructure/services |
| IT | Administrative access and authorized support access |
| Staff | DNS and authorized file services; restricted management access |
| Servers | Protected server zone |
| Guest | Isolated from internal networks and device management |

ACL behavior was validated through both successful and intentionally blocked traffic tests.

---

## Validation

Network functionality was validated using practical tests including:

- DHCP address assignment
- Inter-VLAN connectivity
- DNS hostname resolution
- FTP authentication
- FTP directory access
- SSH administration
- ACL enforcement
- Guest isolation
- Port-security configuration verification
- Trunk validation
- VLAN assignment verification
- Interface status verification

Example validation commands included:

    show ip interface brief
    show interfaces trunk
    show vlan brief
    show access-lists
    show port-security
    show spanning-tree inconsistentports

---

## Troubleshooting Labs

Two deliberate network failures were introduced and resolved to demonstrate structured troubleshooting.

### Lab 1 — Incorrect Access-Port VLAN

An IT endpoint was intentionally assigned to the wrong VLAN.

The fault was identified using:

    show vlan brief

The access port was restored to the correct IT VLAN and connectivity was successfully recovered.

### Lab 2 — Trunk VLAN Propagation Failure

VLAN 20 was intentionally removed from the ACC-IT trunk's allowed VLAN list.

The fault was identified using:

    show interfaces trunk

VLAN 20 was restored to the trunk and IT connectivity recovered successfully.

These exercises demonstrate a practical troubleshooting methodology rather than relying on trial-and-error configuration.

---

## Security Testing Results

Representative security tests confirmed that:

- Guest traffic to Management was blocked
- Guest traffic to IT was blocked
- Guest traffic to Staff was blocked
- Guest traffic to Servers was blocked
- Guest SSH access to the router was blocked
- Staff SSH access to network infrastructure was blocked
- Authorized IT SSH administration succeeded
- Authorized DNS access succeeded
- Authorized FTP access succeeded

ACL hit counters were reviewed to confirm that security policies were actively matching traffic.

---

## Troubleshooting Methodology

Troubleshooting followed a structured process:

1. Identify the symptom
2. Determine the affected network segment
3. Verify endpoint configuration
4. Check VLAN assignment
5. Check trunk configuration
6. Verify routing
7. Inspect ACLs
8. Validate services
9. Correct the root cause
10. Retest
11. Document the result

This approach is designed to reduce unnecessary configuration changes and isolate faults systematically.

---

## Key Engineering Lessons

This project reinforced several practical networking concepts:

- VLANs provide logical segmentation.
- Trunks carry multiple VLANs between network devices.
- Router-on-a-stick enables inter-VLAN routing using 802.1Q subinterfaces.
- DHCP relay enables centralized DHCP across multiple VLANs.
- DNS converts hostnames into IP addresses.
- ACLs enforce traffic policy based on source, destination, protocol, and service.
- ACL direction is critical to correct enforcement.
- Port security protects endpoint-facing switch ports.
- SSH provides secure network-device administration.
- Troubleshooting should focus on identifying the root cause rather than repeatedly changing configurations.
- Configuration persistence is essential; running configurations must be saved to startup configuration.

---

## Project Limitations

The Packet Tracer implementation is a representative laboratory environment rather than a production deployment.

Known limitations include:

- Only a representative subset of endpoints was implemented.
- No physical wireless access point or enterprise wireless controller was deployed.
- No external ISP/Internet path was implemented, so Guest Internet access could not be validated.
- The environment uses Packet Tracer's simulated network services.
- Some security controls would require further refinement for a production deployment.

These limitations are documented intentionally rather than presented as completed functionality.

---

## Future Improvements

Future iterations could include:

- Firewall implementation
- Dedicated wireless infrastructure
- Guest Internet-only routing
- Network Address Translation
- Redundant core/network devices
- Dynamic routing protocols
- Centralized logging/SIEM integration
- Network monitoring
- NTP infrastructure
- AAA using RADIUS/TACACS+
- IDS/IPS
- Secure server hardening
- Infrastructure-as-Code
- Cloud integration
- Zero Trust access controls

---

## Technologies Used

- Cisco Packet Tracer
- Cisco IOS
- IPv4
- VLANs
- 802.1Q Trunking
- Router-on-a-Stick
- DHCP
- DHCP Relay
- DNS
- FTP
- SSH
- Extended ACLs
- Switch Port Security
- STP
- Network Troubleshooting

---

## Repository Structure

The repository will contain:

    HabeebSec-Enterprise-Network-Security-Lab/
    ├── README.md
    ├── documentation/
    │   └── technical-report.pdf
    ├── diagrams/
    │   └── network-topology.png
    ├── configurations/
    │   ├── R1-config-sanitized.txt
    │   ├── CORE-SW1-config-sanitized.txt
    │   ├── ACC-MGMT-config-sanitized.txt
    │   ├── ACC-IT-config-sanitized.txt
    │   ├── ACC-STAFF-config-sanitized.txt
    │   └── SERVER-SW1-config-sanitized.txt
    ├── evidence/
    │   ├── vlan-validation/
    │   ├── trunk-validation/
    │   ├── acl-validation/
    │   ├── service-validation/
    │   └── troubleshooting/
    └── project-files/
        └── HabeebSec-Enterprise.pkt

---

## Portfolio Value

This project demonstrates practical experience in:

- Enterprise network design
- Network segmentation
- Infrastructure configuration
- Network security
- Access control
- Secure device administration
- Network services
- Troubleshooting
- Security validation
- Technical documentation

It forms part of a broader cybersecurity learning path toward **SOC Analysis, Cloud Security, and Security Engineering**.

---

## Author

**Habeeb Shekoni**

Cybersecurity Student | Aspiring SOC Analyst | Cloud Security Enthusiast

GitHub: **@HabeebSec**

---

## Disclaimer

This project is an educational laboratory environment created for cybersecurity and network engineering practice.

All configurations, credentials, addresses, and services are used within the simulated lab environment. Credentials must be removed or sanitized before publishing configuration files to a public repository.
