---
type: Server
title: PVE Proxmox Server
description: Main virtualization host running Proxmox VE 7. Hosts all containers, VMs, and the OKF knowledge base. Clustered with pve2 via richai-cluster.
tags: [proxmox, pve, virtualization, server, cluster, mellanox]
timestamp: 2026-06-23T00:00:00Z
resource: ssh://root@192.168.12.132
---

# PVE Proxmox Server

## Overview
The primary server running Proxmox VE (7.0.6-2-pve) on a HP machine with ZFS tank storage. Forms **richai-cluster** with pve2 (DL360 Gen9).

- **Hostname:** pve
- **Role:** Cluster Node 1 (richai-cluster)
- **IP:** 192.168.12.132
- **40G Link:** 10.10.10.1/30 (Mellanox ConnectX-3 Pro -> pve2)
- **SSH:** `root@192.168.12.132` (via `ssh pve`)

## Power Supply

### Current
- **Type:** 2× HP 500W Platinum (720478-B21)
- **Mode:** Redundant (2+0)
- **Draw (idle):** ~210W total (PS1: 90W, PS2: 120W)
- **Safe limit:** 500W (single PSU in redundancy)

### Planned Upgrade
- **Target:** 2× HPE 1200W Platinum (720480-B21) — ordered 2026-06-23 (~80/pair on eBay)
- **Motivation:** Power 2× Tesla P100 (250W each) + 2× Tesla P4 (75W each) + system (~200W)
- **Capacity after upgrade:** 2400W combined, 1200W redundant — sufficient for all GPUs
- **Status:** Pending delivery and installation

## PCI Devices

## Memory
- **Total:** 128 GB (8× 16GB DDR4 ECC RDIMMs)
- **Channels in use:** 2 of 4 per CPU (suboptimal)
- **Current population per CPU:** DIMM 1, 4, 9, 12
- **Target:** 128 GB once relocated to DIMM 1-8 for 4-channel interleave
- **01:00.1** — Matrox G200EH (onboard VGA)
- **05:00.0** — NVIDIA Tesla P4 (GP104, 8GB)
- **0b:00.0** — NVIDIA Tesla P4 (GP104, 8GB)
- **84:00.0** — NVIDIA Tesla P100 PCIe 16GB (GP100, 16GB)
- **04:00.0** — Mellanox ConnectX-3 Pro (MT27520, 40GbE)

## Storage
- **sda:** 136.7G — OS (LVM root + swap)
- **sdb/sdc/sdd/sde:** 1.6T each — ZFS pool members (tank)
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
| 103 | richai-web | Running | 1C/512MB — Cloudflare tunnel host |
| 104 | k3s-server | Stopped | 2C/8G |
| 106 | **hermes (Larry)** | **Running** | **2C/4G — coder profile** |

## VMs
| VMID | Name | Status | Spec |
|------|------|--------|------|
| 100 | ubuntu-gnome | Stopped | 6C/16G — voice pipeline host |
| 101 | windows-11 | Running | 6C/16G — daily driver VM |
| 102 | fedora | Stopped | 4C/4G |

## 40G Mellanox ConnectX-3 Pro
- **PCI:** 04:00.0 — MT27520 Family
- **Interface:** eno49d1 (altname enp4s0d1)
- **IP:** 10.10.10.1/30, MTU 9000
- **Link partner:** pve2 (10.10.10.2/30)
- **Speed:** 40 Gbps, ~26 Gbps real-world
- **Fix:** port_type_array=2,2 in mlx4_core module

## USB Drives (current)
See [USB Drives](config/usb-drives.md) for current inventory.

## Snapshots
- `tank/subvol-106-disk-0@hermes-golden` -- Golden snapshot of CT 106

## References
- [richai-cluster](hardware/richai-cluster.md)
- [DL360 Gen9 / pve2](hardware/dl360-gen9.md)
- [Network Topology](network/topology.md)
