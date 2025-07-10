# Park IT

Kubernetes infrastructure for self-hosting bitcoin-related services and providing resources for developers working at a shared space.

## Overview

This repository contains Kubernetes manifests and configurations for managing self-sovereign bitcoin infrastructure running on Proxmox. The cluster provides:

- **Bitcoin Core nodes** (mainnet, testnet4, signet) with full indexing
- **Electrum servers** (current using Fulcrum)
- **Mempool.space** block explorer
- **Development playground** for testing bitcoin applications

## Infrastructure Components

### Core Services (bplab-system)
- **Flux GitOps** - Automated deployment from this repository
- **Traefik** - Ingress controller with TCP/SSL termination
- **Cert-Manager** - Automatic TLS certificate management
- **MetalLB** - Load balancer for bare metal
- **Proxmox CSI** - Dynamic storage provisioning
- **Cilium** - Container network interface for network policies

## Notes:
Proxmox Admin URL: https://10.31.0.6:8006/

## Secrets:
See: [Secrets Readme](./secrets/README.md)