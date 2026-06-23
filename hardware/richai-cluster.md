---
type: Cluster
title: richai-cluster
description: Proxmox cluster linking main PVE server (pve) and DL360 Gen9 (pve2) via 40G Mellanox interconnect.
tags: [proxmox, cluster, pve, pve2, high-availability, corosync]
timestamp: 2026-06-23T03:10:00Z
---

# richai-cluster

||| Status: ✅ Operational — 2 Nodes |

Proxmox VE cluster formed between the main server (pve) and the DL360 Gen9 (pve2).

## Cluster Info

- **Name:** `richai-cluster`
- **Transport:** knet (corosync 3)
- **Auth:** Secure (shared key)
- **Nodes:** 2
- **Quorum:** 2/2 — Quorate

## Nodes

| Node ID | Name | IP | Hardware | 40G Link |
|---------|------|----|----------|----------|
| 1 | `pve` | 192.168.12.132 | Main Proxmox Server | 10.10.10.1/30 |
| 2 | `pve2` | 192.168.12.195 (WiFi) | DL360 Gen9 | 10.10.10.2/30 |

## 40G Interconnect

Direct-attach Mellanox ConnectX-3 Pro @ **26 Gbps** throughput, MTU 9000.
Used for cluster traffic and storage migration.

## Cluster Networking

- **Corosync Ports:** UDP 5405 (knet)
- **Firewall:** UFW open for 192.168.12.0/24 on main PVE
- **Host Resolution:** /etc/hosts on both nodes configured for name resolution

## Capabilities

- ✅ Central web GUI — both nodes visible from either server
- ✅ VM/CT live migration (requires shared storage)
- ✅ HA configuration (auto-restart on node failure)
- ✅ Single sign-on / user management
- ✅ Cluster-wide backup schedules

## Pending

- [ ] Set up shared storage (ZFS or NFS over 40G link)
- [ ] Configure HA for critical VMs
- [ ] Add third node when available

## References

- [DL360 Gen9](/hardware/dl360-gen9.md)
- [PVE Main Server](/hardware/pve-proxmox.md)
