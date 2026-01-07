# home-cloud-secure-architecture

A project dedicated to cyber security on a home server for secure, encrypted and private data storage.

# Overview

This project documents the design and deployment of a secure, self-hosted photo management system using Immich, with a strong emphasis on network isolation, least exposure, and defense-in-depth.
Instead of exposing services directly to the internet, the system uses a private WireGuard overlay network with traffic routed through a hardened cloud relay. The Immich service itself runs on a private ARM device and is never publicly reachable, significantly reducing the attack surface.
This project was built as a hands-on cybersecurity exercise focused on secure architecture, VPN routing, Docker isolation, and Linux hardening.

## Repository Structure

This repository is structured in a way that clearly separates concepts and their relative information.

```bash
|-- README.mo
|-- docs/
| |-- architecture.md
| |-- threat-model.md
| |-- security-controls.md
| |-- deployment.md
| |-- lessons-learned.md
|
|-- diagrams/
| |-- network.png
|
|-- configs/
| |-- WireGuard/
| | |-- pi5/
| | |-- oracle/
| | |-- personal-device/
| |
| |-- SSH/
| | |-- pi5/
| | |-- oracle/
| | |-- personal-device/
| |
| |-- docker/
| |
| |-- ufw/
| | |-- pi5/
| | |-- oracle/
| | |-- personal-device/
| |
| |-- iptables/
| | |-- pi5/
| | |-- oracle/
|
```

## Goals

- Independence from big tech
- Self-hosting a modern photo management service without public exposure
- Eliminate inbound internet access to home server
- Secure remote access using encrypted tunnels _only_
- Apply real-world security principles beyond basic homelab setup
- Create a middle man that is publicly exposed, but hardened, and that would route the encrypted tunnel to home server

## High-Level architecture

The system is composed of three main components:

- **Private Server (Raspberry pi 5)**

  Hosts the Immich docker instance. No public ports exposed.

- **Cloud Relay (Oracle Linux VM)**

  Acts as the bastion for the encrypted connection.

- **Client Devices (Any Personal Computing Device)**

  Access Immich exclusively through the WireGuard tunnel.

All communication occurs over an encrypted WireGuard network. The Immich service is reachable _only_ by authenticated peers.

## Security Design Principles

This project was guided by the following security principles:

- **Zero** public exposure
  no direct internet-facing services

- Least privilege
  restricted routing and peer access

- Defense in depth
  VPN, firewall configuration and container isolation

- Separation of concerns
  relay, servers, and clients are isolated roles

## Threat Model (Summary)

- Public service exploitation
- Unauthorized network access
- Brute-force SSH attacks
- Lateral movement
- Service compromise

A detailed threat analysis is available in (threat model location)

## Security Controls Implemented

- WireGuard private overlay network
- No inbound NAT to the private server (pi5)
- Iptables forwarding and NAT rules on relay
- Docker container isolation
- SSH key-only authentication
- Fail2Ban on exposed services
- Principle of least exposure for all components

## Why This Architecture?

ypical self-hosting approaches rely on port forwarding or reverse proxies exposed to the internet. This project intentionally avids that model for its exposure and increased surface of attack.

**Benefits of this design**:

- Smaller attack surface
- Encrypted traffic end-to-end
- No dependency on public DNS or TLS
- Safer access from untrusted networks (public Wi-Fi/cellular networks)

**Tradeoffs**:

- Increased networking complexity
- VPN dependency for access
- Advanced routing configurations

## Technologies Used

- WireGuard
- Fail2Ban
- Docker & Docker compose
- Ubuntu Linux (x86)
- Raspberry pi OS Lite - Debian bookworm (ARM)
- Oracle Ubuntu instance (1 virtual x86 core)
- Iptables / ufw (uncomplicated fire wall)
- Immich (self-hosted photo platform)

## Repository Structure

## Lessons Learned

- Iptable configuration on both the home server and the Oracle bastion must be configured _after_ WireGuard is setup to ensure proper communication to avoid frustration.
- Proper network configuration of the Oracle server on Oracle's control panel _after_ Wireguard setup.
- Cloud relays simplify secure access without exposing home infrastructure
- Security-first design often increases complexity but significantly reduces risk
- WireGuard is extremely efficient and fast in data transfer even when the files are Gigabytes in size

## Disclaimer

This repository is for **educational and demonstration purposes** only. Configuration files are sanitized and should be adapted before use in production environments. Never share your private keys with anyone or expose your home's IP addresses on the internet, these informations are sensitive and should be guarded with utmost care.
