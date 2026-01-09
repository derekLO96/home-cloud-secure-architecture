## Deployment

### Overview

This document describes the deployment process for this project. The focus is on secure provisioning, controlled exposure, and repeatable setup, rather than step-by-step installation instructions.

All components are deployed with the assumption that no application service is publicly exposed and that access occurs exclusively through an authenticated VPN.

### Deployment Model

The system follows a hub-and-spoke deployment model:
**1.** Oracle bastion is configured and provisioned first
**2.** Home server is configured and deployed second
**3.** Client devices are then configured and added last

This order ensures that secure connectivity is established before any application services become accessible

## Prerequisites

### Infrastructure

- One cloud VM with a public IP
- One private ARM-based server
- One or more client devices

### Software

- Linux (Ubuntu for bastion and Raspberry pi OS 64-bit Lite for home server)
- WireGuard
- Docker & Docker Compose
- iptables / ufw
- Fail2Ban

## Deployment Phase

### Phase 1: Oracle Bastion Configuration

The Oracle bastion serves as the only internet-facing component and must be deployed first.

Key steps:

- Configuring minimal Linux VM
- Apply system updates
- Install WireGuard
- Generate key pairs for clients
- Harden SSH access
- Configure firewall and iptables rules
- Download, install and configure Fail2Ban

Security consideration:

- SSH key-only authentication
- Minimal open ports (SSH + WireGuard only)
- Default-deny firewall posture

The bastion must be fully secured before VPN peers are added

### Phase 2. VPN Configuration (WireGuard)

WireGuard is configured in a hub-and-spoke topology:

- Bastion acts as the central peer, routing all VPN traffic
- Home server and clients connect only to the bastion and never directly to each other

Deployment principles:

- Unique key pair per device
- Static VPN IP assignment
- Explicit Allowed IPs configuration
- No wildcard routing

Once established, the VPN is validated before proceeding.

### Phase 3: Home Server Deployment

The Home server hosts the Immich application and is **never** exposed directly to the internet.

Deployment steps:

- Configure minimal Raspberry pi OS Lite
- Generate SSH key pairs for clients
- Harden SSH access
- Apply system updates
- Download, install and configure WireGuard
- Download, install and configure ufw
- Download, install and configure iptables
- Install Docker and Docker Compose
- Download and install Immich via Docker
- Configure Immich services
- Bind Immich and docker services to internal interfaces **only**
- Verify that no ports are accessible outside the VPN

Security considerations:

- No inbound NAT or port forwarding
- SSH is hardened and only accessible with authenticated keys
- Containers isolated from host network
- Access limited to VPN subnet

Application deployment occurs only after VPN connectivity is confirmed.

### Phase 4: Firewall & Routing Validation

Firewall and routing rules are validated to ensure:

- Only required traffic is permitted
- No unintended lateral movement
- No accidental internet exposure

Validation includes:

- Testing access from authorized VPN clients
- Verifying denial of access from non-VPN sources
- Confirming absence of listening services on public interfaces

### Phase 5: Client Onboarding

Client devices are onboarded individually.

Process:

- Generate unique WireGuard key pair
- Assign dedicated VPN IP
- Restrict Allowed IPs to required services
- Test access through the VPN

Access can be revoked at any time by removing the peer configuration from the bastion's 'wg0.conf' file.

## Deployment Validation Checklist

- [ ] Oracle bastion hardened and patched
- [ ] SSH Key-only Authentication enforced
- [ ] WireGuard tunnel established
- [ ] Home server unreachable from public internet
- [ ] Immich accessible only via WireGuard
- [ ] Logs generated for access attempts

## Operational Considerations

### Updates

- OS security updates applied on a regularly basis
- Docker images updated manually after review
- WireGuard updates monitored for compatibility

### Backups

- Application data backed up separately
- Backup destinations not publicly accessible
- Restore process tested periodically

### Availability

- VPN dependency acknowledged
- Oracle bastion availability directly impacts access (dependency on Oracle's services and up times)
- No automated failover in current scope

## Failure Scenarios

The deployment accounts for the following scenarios:

- **VPN failure**: Application becomes unreachable but remains secure
- **Oracle bastion compromise**: Access can be revoked by rotating keys
- **Client compromise**: Single peer access can be revoked without affecting other peers

Security is prioritized over availability in failure cases.
