## Security Controls

### Overview

This document outlines the technical and administrative security controls implemented in this project's architecture. Controls are grouped by function and mapped to the risk they mitigate.

The objective is to reduce attack surface, enforce strong authentication, protect sensitive data, and limit the impact of potential compromises.

## Network Security Controls

### Private Overlay Network (WireGuard)

**Control Type**: Preventive
**Risk Addressed**: Unauthorized network access, eavesdropping

- All inter-system communication occurs over a WireGuard VPN
- Cryptographic public-key authentication is required for all peers
- No password-based VPN access is permitted
- Peer access can be revoked instantly by removing public keys from 'ssh_config' **and** 'wg0.conf'

**Security Benefits**:
Eliminates exposure of internal services to the public internet while enforcing encrypted network traffic.

## Zero Public Application Exposure

**Control Type**: Preventive
**Risk Addressed**: Service exploitation, automated scanning

- Immich is not bound to any public interfaces
- No inbound NAT or port forwarding to the home server
- Immich and SSH are only accessible via WireGuard VPN IPs

**Security Benefit**:
Removes the most common attacks in self-hosted servers.

## Firewall & Routing Controls

**Control Type**: Preventive
**Risk Addressed**: Lateral movement, unauthorized access

- Explicit iptables rules enforce how network traffic flows
- Default-deny approach for inbound traffic
- VPN routing restricted to require subnets only
- Oracle bastion acts as the sole routing hub

**Security Benefits**:
Limits the blast radius of compromised components.

## Host-Level Security Controls

**Control Type**: Preventive
**Risk Addressed**: Brute-force attacks, credential compromise

- SSH key-only authentication
- Password authentication disabled
- Remote Root login disabled
- Limited exposed services on Oracle bastion

**Security Benefits**:
Prevents common automated SSH compromise attempts

## Intrusion Prevention (Fail2Ban)

**Control Type**: Detective/Preventive
**Risk Addressed**: Repeated authentication attempts

- Fail2Ban monitors SSH logs
- Automatically bans abusive IPs
- Reduces noise and attack persistence

**Security Benefits**:
Adds a reactive layer to exposed entry points

## System Hardening

**Control Type**: Preventive
**Risk Addressed**: Privilege escalation, system compromise

- Minimal installed packages
- Regular security updates
- Principle of least functionality
- Service isolation where possible

**Security Benefits**:
Reduces exploitable surface area at the OS level

## Application & Container Security

### Containerization (Docker)

**Control Type**: Preventive
**Risk Addressed**: Application compromise, dependency conflicts

- Immich runs inside Docker containers
- Services are isolated from host processes
- Containers are bound to internal networks only
- Controlled port exposure within VPN

**Security Benefit**:
Limits impact pf application-level vulnerabilities

## Application Access Control

**Control Type**: Preventive
**Risk Addressed**: Unauthorized application use

- Immich accessible only through WireGuard
- No public authentication endpoints
- Reliance on internal service exposure only

**Security Benefits**:
Ensures only authenticated VPN users can reach the home server.

## Cryptographic Controls

### Encrypted Transport

**Control Type**: Preventive
**Risk Addressed**: Traffic interception, MITM attacks

- WireGuard uses modern cryptographic primitives
- All traffic encrypted in transit
- No plaintext service communication

**Security Benefits**:
Protects confidentiality and integrity of data in motion.

## Key Management

**Control Type**: Administrative/Preventive
**Risk Addressed**: Unauthorized access

- Unique WireGuard key pairs per device
- Keys stored securely on each system
- Revocation performed by removing peer configuration on 'wg0.conf'

**Security Benefits**:
Allows precise access control and rapid Revocation

## Monitoring & Operational Controls

### Logging

**Control Type**: Detective
**Risk Addressed**: Undetected compromise

- System logs enabled on Oracle bastion and home server
- SSH and Fail2Ban events recorded
- Docker service logs available for review
- Immich logs available for review

**Security Benefits**:
Supports incident investigation and troubleshooting

### Configuration Management

**Control Type**: Administrative
**Risk Addressed**: Misconfiguration

- Configurations documented in version-controlled repository
- Changes tracked and reviewable
- Reduced reliance on ad-hoc system changes

**Security Benefits**:
Improves reliability and audibility of security settings

## Limitations

The following limitations are acknowledged:

- No centralized SIEM (**s**ecurity **i**nformation and **e**vent **m**anagement)
- Limited active intrusion detection
- Manual key rotation
- No automated compliance validation

These limitations are acceptable given the project's scope and are documented for transparency.
