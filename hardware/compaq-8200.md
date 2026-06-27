|---
type: Server
title: HP Compaq 8200 Elite SFF — PVE3
description: Third Proxmox cluster node, i7-2600, 30GB RAM, 168GB SSD, Tesla P4-8GB.
tags: [proxmox, pve3, compaq, sff, tesla, p4]
timestamp: 2026-06-26T21:15:00Z
---

# HP Compaq 8200 Elite SFF — pve3

|||| Status: ✅ Proxmox VE 9.2.2 — Cluster Node 3, Tesla P4 Online |

Third node in the richai-cluster. Equipped with a Tesla P4-8GB for compute workloads. Intel HD 2000 integrated graphics handles display output.

## Specs

- **CPU:** Intel Core i7-2600 @ 3.4GHz (4C/8T)
- **RAM:** 30GB DDR3
- **Storage:** 168GB SSD (ext4, non-ZFS)
- **Network:** Onboard 1GbE → 192.168.12.163
- **GPU:** Tesla P4-8GB (NVIDIA 580.167.08, CUDA 13.0)

## GPU Installation Notes

The Tesla P4 was installed with integrated graphics as the primary display adapter:

1. **BIOS Configuration:** Enabled integrated graphics (i915) → set as primary display → DisplayPort output
2. **GT 635 Removed:** Old GeForce GT 635 (Kepler) was a stopgap display card — removed once onboard video was active
3. **Driver Install:** Used NVIDIA `.run` installer (`NVIDIA-Linux-x86_64-580.167.08.run`) — APT packages failed because:
   - Debian trixie `nvidia-driver` (550.x) couldn't compile on Proxmox kernel 7.0.2-6-pve
   - CUDA repo had mixed versions — 610.x dropped Pascal support, 590.x had APT dependency conflicts
4. **Persistence:** Systemd service created at `/etc/systemd/system/nvidia-persistenced.service`
5. **Auto-load:** Kernel modules configured in `/etc/modules-load.d/nvidia.conf`
6. **Nouveau:** Blacklisted in `/etc/modprobe.d/blacklist-nouveau.conf`

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
