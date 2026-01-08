## Architecture

### Overview

This document describes the architecture of a secure, encrypted, self-hosted Immich deployment designed to minimize attack surface while maintaining reliable remote access from any authenticated device around the world.

The system uses a private WireGuard overlay network to interconnect a cloud-based relay, a private home server running on a raspberry pi5, and authorized client devices. The Immich serice is **never** exposed directly to the internet and is only reachable through authenticated VPN peers.

A visual representation of the system architecture is available in : /diagrams/network.png

## Architectural Goals

The architecture was designed to achieve the following goals:

- Prevent direct internet exposure of the private server and home network
- Enforce encrypted communication across all components
- Restrict access to authenticated and authorized devices **only**
- Maintain connection simplicity while improving security posture
- Reduce the blast radius of potential compromises

## System Components

### 1. Private Home Server (pi5)

**Role**: Host the Immich server
**Environment**: Raspberry pi 5 running Raspberry pi OS Lite (Debian bookworm) on a non-routable private network

responsibilities:

- Runs Immich using Docker Compose
- Stores photo and video data and application metadata
- Listens only on WireGuard private interface
- Does **not** accept inbound internet traffic

security characteristics:

- No public IP address
- No inbound NAT or port forwarding
- Access restricted to VPN peers only
- SSH key-only authentication

### 2. Bastion (Oracle Ubuntu VM)

**Role**: Controlled ingress and traffic router
**Environment**: Oracle cloud infrastructure running a VM of an Ubuntu instance (Hardened)

Responsibilities:

- Terminates WireGuard tunnels from all peers
- Routes VPN traffic between clients and the private server
- Performs NAT and forwarding for internal services
- Acts as the only internet exposed component

Security characteristics:

- Minimal exposed services
- SSH key-only authentication
- Fail2Ban monitoring traffic
- Strict firewall rules

### 3. Client Devices

**Role**: Authorized access endpoints
**Environment**: any device on any untrusted networks (public WiFi/mobile networks)

Responsibilities:

- Establishes encrypted WireGurad tunnel
- Access Immich via private VPN IPs
- **Never** communicates directly with the private server outside the tunnel

Security characteristics:

- Cryptographic identity via WireGuard keys
- No persistent inbound exposure
- Access can be revoked by removing keys

## Network Topology

### Logical Flow

**1.** Clients initiate WireGuard tunnel to the Bastion
**2.** Bastion authenticates peers using public keys
**3.** Traffic is routed to the Immich server internally via the VPN
**4.** Home server responds through the same encrypted tunnel

Network traffic is encrypted throughout the entire process and is inaccessible to external observers. The home server and the client's public IP's are never exposed.

## Addressing & Routing

- Each component is assigned a static VPN IP
- The Bastion acts as the routing hub
- Home server has no default route to the public internet
- Client devices are restricted to require subnets only

This hub-and-spoke model simplifies access control and limits lateral movement.

## Containerization & Isolation

Immich is deployed using Docker to provide:

- Process isolation
- Controlled networking
- Simplified Updates
- Reduced dependency conflicts

Containers are bound only to internal interfaces and are unreachable without VPN access.

## Trust Boundaries

The architecture defines clear trust boundaries:

- **Untrusted**: public internet, mobile networks
- **Semi-trusted**: Bastion (hardened but internet-facing)
- **Trusted**: Home server and internal VPN network

Crossing that trust boundary requires key authentication and encryption.

## Design Tradeoffs

### Benefits

- Significantly reduced attack surface
- No reliance on public DNS or TLS certificates
- Strong cryptographic authentication
- Secure access from any network

### Costs

- Increased networking complexity
- VPN dependency for availability
- Higher operational overhead

These tradeoffs were accepted in favor of stronger security guarantees due to the nature of the sensitive data that would be stored on this server.
