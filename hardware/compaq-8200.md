---
type: Server
title: HP Compaq 8200 Elite SFF — PVE3
description: Third Proxmox cluster node, i7-2600, 30GB RAM, 168GB SSD.
tags: [proxmox, pve3, compaq, sff]
timestamp: 2026-06-26T20:30:00Z
---

# HP Compaq 8200 Elite SFF — pve3

||| Status: ✅ Proxmox VE 9.2.2 — Cluster Node 3 |

Third node in the richai-cluster. Acts as lightweight cluster member — can be used for management tasks, experiment VMs, or a single Tesla P4 if needed.

## Specs

- **CPU:** Intel Core i7-2600 @ 3.4GHz (4C/8T)
- **RAM:** 30GB DDR3
- **Storage:** 168GB SSD (ext4, non-ZFS)
- **Network:** Onboard 1GbE → 192.168.12.163
- **GPU:** None installed (possible P4 candidate)

## Setup

- **OS:** Proxmox VE 9.2.2
- **Hostname:** pve3.lan
- **Storage:** `local` — ~46GB free after boot
- **BIOS Settings:** VTx En, VTd En, DEP En, Intel TXT Dis

## Cluster

- Joined `richai-cluster` via manual corosync key copy (June 25, 2026)
- Quorate with 3 nodes

## References

- [richai-cluster](/hardware/richai-cluster.md)
