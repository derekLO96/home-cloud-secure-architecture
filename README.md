# home-cloud-secure-architecture

A project dedicated to cyber security on a home server for secure, encrypted and private data storage.

# Overview

This project documents the design and deployment of a secure, self-hosted photo management system using Immich, with a strong emphasis on network isolation, least exposure, and defense-in-depth.
Instead of exposing services directly to the internet, the system uses a private WireGuard overlay network with traffic routed through a hardened cloud relay. The Immich service itself runs on a private ARM device and is never publicly reachable, significantly reducing the attack surface.
This project was built as a hands-on cybersecurity exercise focused on secure architecture, VPN routing, Docker isolation, and Linux hardening.

## Goals

- Independence from big tech
- Self-hosting a modern photo management service without public exposure
- Eliminate inbound internet access to home server
- Secure remote access using encrypted tunnels _only_
- Apply real-world security principles beyond basic homelab setup

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

- **Zero public exposure**

* no direct internet-facing services
