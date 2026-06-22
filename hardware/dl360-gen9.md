---
type: Server
title: DL360 Gen9
description: New HPE DL360 Gen9 1U rack server for Proxmox — being set up as a secondary virtualization host linked to the main PVE server via 40G networking.
tags: [dl360, gen9, hpe, server, proxmox, pending]
timestamp: 2026-06-21T23:45:00Z
---

# DL360 Gen9

| Status: Initial Setup — Proxmox Install Pending 🚧 |

The DL360 Gen9 is racked and powered on. Logical drive created on HP Smart Array P440ar. Awaiting Proxmox VE 8 install.

## Current Configuration

- **Model:** HPE ProLiant DL360 Gen9 (1U rack)
- **Hostname:** `pve2.lan`
- **IP:** 192.168.12.50 (planned)
- **Role:** Secondary Proxmox node, to be clustered with main PVE
- **Networking:** 40G interlink via Mellanox ConnectX-3 Pro (main PVE side installed and detected)
- **OS:** Proxmox VE 8 (via Ventoy USB — install pending)
- **Storage:** HP Smart Array P440ar — RAID logical drive created (strip 64K, read-ahead, write-back with BBU)
- **Root Password:** Stored in GPG vault on main PVE

## To-Do

1. ✅ Rack the server
2. ✅ Connect power and networking (including 40G interlink)
3. ✅ Create logical drive on RAID controller
4. ◻ Boot from Ventoy USB, install Proxmox VE 8
5. ◻ Configure IP on LAN (192.168.12.50)
6. ◻ Post-install setup via SSH (Linda)
7. ◻ Configure 40G networking between pve and pve2
8. ◻ Cluster with main PVE

## References

- Installation ISO on [Ventoy Multi-Boot USB](/config/ventoy-usb.md)
- Main PVE server at [PVE Proxmox](/hardware/pve-proxmox.md)
