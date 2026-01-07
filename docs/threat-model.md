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

| Actor                                                               | Capability | Motivation                       |
| ------------------------------------------------------------------- | ---------- | -------------------------------- |
|                                                                     |
| Opportunistic internet scanners Low Exploit exposed services        | Low        | Exploit exposed services         |
| Targeted attackers Medium Data theft or persistence                 | Medium     | Data theft or persistence        |
|                                                                     |
| Malicious insiders Medium Unauthorized access                       | Medium     | Unauthorized access              |
|                                                                     |
| Bots Low-Medium Brute-force, enumeration                            | Low-Medium | Brute-force, enumeration         |
|                                                                     |
| Accidental misconfiguration High Unintentional exposure/leaked data | High       | Unintentional exposure / leakage |
|                                                                     |
