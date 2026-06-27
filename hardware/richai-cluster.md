---
type: Cluster
title: richai-cluster
description: Proxmox VE 3-node cluster — pve (DL380), pve2 (DL360), pve3 (Compaq 8200).
tags: [proxmox, cluster, pve, pve2, pve3, corosync]
timestamp: 2026-06-26T20:30:00Z
---

# richai-cluster

||| Status: ✅ Operational — 3 Nodes |

Proxmox VE cluster with three nodes connected via TP-Link TL-SG108 switch.

## Cluster Info

- **Name:** `richai-cluster`
- **Transport:** knet (corosync 3)
- **Auth:** Secure (shared key)
- **Nodes:** 3
- **Quorum:** 3/3 — Quorate

## Nodes

| Node ID | Name | IP | Hardware | Role |
|---------|------|----|----------|------|
| 1 | `pve` | 192.168.12.132 | DL380 Gen9 — ZFS tank (3×1.6TB), 2×P100 | Main |
| 2 | `pve2` | 192.168.12.50 | DL360 Gen9 — 2×Tesla P4, CT 106 (Hermes) | GPU Compute |
| 3 | `pve3` | 192.168.12.163 | Compaq 8200 — i7-2600, 30GB RAM, 168GB SSD | Light/Management |

## Network

- **Switch:** TP-Link TL-SG108 — unmanaged, loop prevention ON
- **Latency:** All nodes sub-0.6ms
- **Gateway:** 192.168.12.1 (T-Mobile gateway)
- **PVE iLO:** 192.168.12.126
- **PVE2 iLO:** 192.168.12.100

## GPUs

- **PVE (DL380):** 2× Tesla P100 (32GB VRAM) — full-height, dual-slot
- **PVE2 (DL360):** 2× Tesla P4 (16GB VRAM) — low-profile, single-slot, PCIe slot powered
- **PVE3 (Compaq 8200):** TBD

## References

- [PVE (DL380 Gen9)](/hardware/pve-proxmox.md)
- [PVE2 (DL360 Gen9)](/hardware/dl360-gen9.md)
- [PVE3 (Compaq 8200)](/hardware/compaq-8200.md)
