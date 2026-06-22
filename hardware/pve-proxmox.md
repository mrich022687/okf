---
type: Server
title: PVE Proxmox Server
description: Main virtualization host running Proxmox VE. Hosts all containers and VMs.
tags: [proxmox, pve, virtualization, server]
timestamp: 2026-06-21T18:15:00Z
resource: ssh://root@192.168.12.132
---

# PVE Proxmox Server

## Overview
The primary server running Proxmox VE (7.0.6-2-pve) on a HP machine with ZFS tank storage.

- **Hostname:** pve
- **IP:** 192.168.12.132
- **SSH:** `root@192.168.12.132` (via `ssh pve`)
- **API:** root@pam (password known)

## Storage
- **sda:** 136.7G — OS (LVM root + swap)
- **sdb:** 186.3G — ZFS pool member (tank)
- **sdc/sdd/sde:** 1.6T each — ZFS pool members (tank)
- **tank capacity:** 2.8T total, 2.7T available

### ZFS Datasets on tank
| Mount | Size | Purpose |
|-------|------|---------|
| `/tank/subvol-106-disk-0` | 20G | CT 106 (Hermes coder) |
| `/tank/subvol-103-disk-0` | 4G | CT 103 (richai-web) |
| `/tank/subvol-104-disk-0` | 8G | CT 104 (k3s-server) |
| `/tank/backups` | — | Backup storage |
| `/tank/vms` | — | VM disk storage |
| `/tank/data` | 2.7T | General data (OKF lives here) |
| `/tank/dump` | — | vzdump backups |

## Containers
| VMID | Name | Status | Spec |
|------|------|--------|------|
| 103 | richai-web | Running | 2C/4G |
| 104 | k3s-server | Stopped | 2C/8G |
| 106 | **hermes** | **Running** | **2C/4G, this container** |

## VMs
| VMID | Name | Status | Spec |
|------|------|--------|------|
| 100 | ubuntu-gnome | Running | 16GB |
| 101 | windows-11 | Stopped | 16GB |
| 102 | fedora | Stopped | 4GB |

## USB Drives (current)
See [USB Drives](config/usb-drives.md) for current inventory.

## Snapshots
- `tank/subvol-106-disk-0@hermes-golden` — Golden snapshot of CT 106
