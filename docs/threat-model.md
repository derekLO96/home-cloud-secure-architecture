## Threat Model

### Why This Document Exists

This threat model was created to help me think more clearly about what I was trying to protect, who or what could realistically threaten the system, and where things could go wrong.

Rather than aiming for a perfect or exhaustive model, the goal was to practice security thinking and to make informed design decisions while building a real system.

## What I Am Protecting (Assets)

The following assets were identified as security-critical:

- Personal photos and videos
- Photo metadata (timestamps, locations, faces, etc.)
- Immich application credentials
- WireGuard private keys
- Bastion infrastructure
- Home server Operating system
- SSH private keys

Protecting these assets mainly means preserving confidentiality, while also maintaining integrity and availability where possible.

## Threat Actors

|              Actor              | Capability |            Motivation            |
| :-----------------------------: | :--------: | :------------------------------: |
|                                 |            |
| Opportunistic internet scanners |    Low     |     Exploit exposed services     |
|                                 |            |
|       Targeted attackers        |   Medium   |    Data theft or persistence     |
|                                 |            |
|       Malicious insiders        |   Medium   |       Unauthorized access        |
|                                 |            |
|              Bots               | Low-Medium |     Brute-force, enumeration     |
|                                 |            |
|   Accidental misconfiguration   |    High    | Unintentional exposure / leakage |

## Trust Boundaries

One of the most powerful concepts I learned was the idea of trust boundaries.

In this system:

- **Public internet** : untrusted
- **Oracle Bastion** : partially trusted (exposed to the internet)
- **VPN network** : trusted once authenticated
- **Home server** : fully trusted

## Main Areas of Risk

Through building and troubleshooting the system, a few key risk areas became clear

### 1. Public Service Exploitation

**Description**:
Exploitation of publicly exposed application services

**Risk**: High

**Likelihood**: High (common internet scans)

**Solution**:

- No public exposure of home server
- No inbound NAT or port forwarding to private server
- Immich accessible only via WireGuard

### 2. Unauthorized Network Access

**Description**:
An Attacker attempts to join or traverse the private network.

**Risk**: High

**Likelihood**: Medium

**Solution**:

- WireGuard cryptographic key authentication
- Explicit peer configuration
- No password-based VPN access
- Ability to revoke access by removing keys

### 3. Brute-Force SSH Attacks

**Description**:
Automated attempts to gain access to Oracle Bastion via SSH.

**Risk**: Medium

**Likelihood**: High

**Solution**:

- SSH key-only authentication
- Fail2Ban enforcement
- Minimal exposed services
- Oracle Cloud firewall rules

### 4. Lateral Movement

**Description**:
Compromised component attempts to move laterally within the network.

**Risk**: Medium

**Likelihood**: Medium

**Solution**:

- Restricted VPN routing
- Hub-and-spoke network design
- No peer-to-peer client routing
- Firewall rules limiting traffic scope

### 5. Data Exfiltration

**Description**:
Unauthorized extraction of photos or metadata.

**Risk**: High

**Likelihood**: Low-Medium

**Solution**:

- Encrypted transport via WireGuard
- No public application endpoints
- Container isolation
- OS-level permission on storage

### 6. Misconfiguration Exposure

**Description**:
Accidental exposure caused by incorrect firewall or Docker settings.

**Risk**: High

**Likelihood**: Medium

**Solution**:

- Minimal configuration footprint
- Explicit allow rules
- No default-open rules
- Documentation of configuration decisions

## Risk Summary

|         Threat          | Residual Risk |
| :---------------------: | :-----------: |
| Public Service Exploit  |      Low      |
| Unauthorized VPN access |      Low      |
|     SSH compromise      |  Low-Medium   |
|    Lateral movement     |    Medium     |
|    Data Exfiltration    |      Low      |
|    Misconfiguration     |    Medium     |

## Out-of-Scope Threats

These threats were **intentionally** excluded because they where deemed Out-of-Scope:

- Physical access to private server
- Zero-day vulnerabilities in Immich
- Cloud provider insider threats
- Supply chain attacks

## Assumptions

This model assumes:

- Secure generation and storage of keys
- Timely system updates and patching
- Devices with VPN access are trusted
- Proper operational security by the system operator

If these are in any way violated, then risk increases significantly and jeopardises the entire system.
