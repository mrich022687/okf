---
type: Config
title: USB Drives Inventory
description: Current USB drives plugged into the PVE server hub. Updated dynamically.
tags: [usb, drives, inventory, hardware]
timestamp: 2026-06-21T18:15:00Z
---

# USB Drives Inventory

## Current Status (2026-06-21)

| Device | Size | Label | Content | Mount |
|--------|------|-------|---------|-------|
| sdn | 58.6G | LINUX MINT | Linux Mint live USB | Not mounted |
| sdo | 3.8G | Linux Lite 7.0 | Linux Lite installer | `/mnt/usb_sdo1` |
| sdp | 58.6G | PVE | Proxmox installer | Not mounted |
| sdq | 1.9T | ESD-USB | Windows installer (32G partition) | `/mnt/usb_sdq1` |
| sdr | 58.6G | WesData | Backup + Lubuntu ISO | `/mnt/usb_sdr` (exfat) |
| sds | 58.6G | Q1900M BIOS | BIOS firmware flasher | `/mnt/usb_sds` |
| sdt | 58.6G | Ubuntu-Server 26.04 | Ubuntu Server installer | Not mounted |

### WesData Drive Details
5 identical WesData 64GB drives (ID 346d:5678), currently flashed with various ISOs:
- Linux Mint, PVE, Backup+Lubuntu, Q1900M BIOS, Ubuntu-Server

### Notes
- Drives mapped after USB hub power cycle on 2026-06-21
- SCSI rescan needed after plugging/unplugging
