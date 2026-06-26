---
type: Server
title: Compaq 8200 Elite SFF — pve3
description: Proxmox node (pve3) in the richai-cluster. Small form factor, i7-2600, 31 GB RAM. Runs on integrated graphics — PCIe x16 slot available for GPU.
tags: [proxmox, pve3, compaq, 8200, sff, cluster]
timestamp: 2026-06-26T11:30:00Z
resource: ssh://root@192.168.12.163
---

# Compaq 8200 Elite SFF — pve3

## Overview
An HP Compaq 8200 Elite Small Form Factor running Proxmox VE. Third node in the richai-cluster (pve3).

- **Hostname:** pve3
- **Role:** Cluster Node 3 (richai-cluster)
- **IP:** 192.168.12.163
- **SSH:** `root@192.168.12.163`

## Hardware Specs

| Component | Detail |
|-----------|--------|
| **CPU** | Intel Core i7-2600 @ 3.4 GHz (4C/8T, Sandy Bridge) |
| **RAM** | 31 GB DDR3 |
| **Boot Drive** | 180 GB Intel SSD (Proxmox root: 51 GB, 9% used) |
| **Network** | Intel 82579LM Gigabit (vmbr0) |
| **GPU** | Intel HD Graphics 2000 (integrated only) |

## PCIe Slots (all empty)

| Slot | Type | Electrical Width | Usage |
|------|------|-----------------|-------|
| X16PCIEXP | PCIe 2.0 x16 | x16 from CPU | Best for GPU |
| X4PCIEXP | PCIe 2.0 x4 | x4 | Add-on cards |
| X1PCIEXP1 | PCIe 2.0 x1 | x1 | Low bandwidth |
| PCI1 | Legacy PCI 32-bit | — | Old cards |

## GPU Options
The x16 slot can take a Tesla P4 (75W, 8GB VRAM, single-slot, no power cable needed). Would need a 40mm fan mod for active cooling since the P4 is passively cooled and SFF airflow is tight.

## Storage
- Currently NFS-mounts ISOs from `q1900-isos` (Q1900) and `isos-nfs` (main PVE)
- Has 44 GB free on root — room for a VM or two
- Extra drive slot available internally for a SATA SSD/HDD

## VM Numbering
- Node 3 in cluster → VMs use 30x IDs (e.g., richai-web CT is 103, not affected by this)
