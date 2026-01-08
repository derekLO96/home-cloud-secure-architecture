## Threat Model

### Overview

This document outlines the threat model for this project. The goal of this analysis is to identify key assets, potential threat actors, attack surfaces, and implemented mitigations in order to evaluate the system's security posture.

The threat model focuses on network exposure, unauthorized access, data confidentiality, and lateral movement, which are the primary risks in self-hosted environments.

## Assets

The following assets were identified as security-critical:

- Personal photos and videos
- Photos of government documents and identifications
- Photo metadata (timestamps, locations, faces, etc.)
- Immich application credentials
- WireGuard private keys
- Bastion infrastructure
- Home server Operating system
- SSH private keys

The loss of confidentiality, integrity, or availability of these assets would represent a security failure.

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

The system enforces multiple trust boundaries

- **Public internet** : untrusted
- **Oracle Bastion** : semi-trusted (internet-facing)
- **VPN network** : trusted, authenticated
- **Home server** : trusted

## Attack Surfaces

The following attack surfaces were evaluated:

- WireGuard interface on Bastion
- SSH service on Bastion
- Docker runtime on home server
- Immich application endpoints
- VPN routing and firewall configurations
- User privilege and password management

## Threat Analysis

### 1. Public Service Exploitation

**Description**:
Exploitation of publicly exposed application services

**Risk**: High
Likelihood: High (common internet scans)

**Mitigation**:

- No public exposure of home server
- No inbound NAT or port forwarding to private server
- Immich accessible only via WireGuard

### 2. Unauthorized Network Access

**Description**:
An Attacker attempts to join or traverse the private network.

**Risk**: High
**Likelihood**: Medium

**Mitigation**:

- WireGuard cryptographic key authentication
- Explicit peer configuration
- No password-based VPN access
- Ability to revoke access by removing keys

### 3. Brute-Force SSH Attacks

**Description**:
Automated attempts to gain access to Oracle Bastion via SSH.

**Risk**: Medium
**Likelihood**: High

**Mitigation**:

- SSH key-only authentication
- Fail2Ban enforcement
- Minimal exposed services
- Oracle Cloud firewall rules

### 4. Lateral Movement

**Description**:
Compromised component attempts to move laterally within the network.

**Risk**: Medium
**Likelihood**: Medium

**Mitigation**:

- Restricted VPN routing
- Hub-and-spoke network design
- No peer-to-peer client routing
- Firewall rules limiting traffic scope

### 5. Data Exfiltration

**Description**:
Unauthorized extraction of photos or metadata.

**Risk**: High
**Likelihood**: Low-Medium

**Mitigation**:

- Encrypted transport via WireGuard
- No public application endpoints
- Container isolation
- OS-level permission on storage

### 6. Misconfiguration Exposure

**Description**:
Accidental exposure caused by incorrect firewall or Docker settings.

**Risk**: High
**Likelihood**: Medium

**Mitigation**:

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

The following threats were **intentionally** excluded from this model:

- Physical access to private server
- Zero-day vulnerabilities in Immich
- Cloud provider insider threats
- Supply chain attacks

These threats require different mitigation strategies beyond the scope of this project.

## Assumptions

This threat model assumes:

- Secure generation and storage of cryptographic keys
- Timely system updates and patching
- No shared VPN keys between devices
- Proper operational security by the system operator

Violation of these assumptions may invalidate parts of this model.
