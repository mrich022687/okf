---
type: Server
title: DL360 Gen9
description: New HPE DL360 Gen9 1U rack server for Proxmox — being set up as a secondary virtualization host linked to the main PVE server via 40G networking.
tags: [dl360, gen9, hpe, server, proxmox, pending]
timestamp: 2026-06-21T23:45:00Z
---

# DL360 Gen9

## Status: Pending Setup 🚧

The DL360 Gen9 has been acquired but is not yet racked or powered on. Will run Proxmox VE as a secondary host.

## Planned Configuration

- **Model:** HPE ProLiant DL360 Gen9 (1U rack)
- **Hostname:** `dl360` (proposed)
- **Role:** Secondary Proxmox node, potentially clustered with main PVE
- **Networking:** 40G card for inter-server link between DL360 and main PVE
- **OS:** Proxmox VE 8 (via Ventoy USB)

## To-Do

1. Rack the server
2. Connect power and networking (including 40G interlink)
3. Boot from Ventoy USB, install Proxmox VE 8
4. Configure IP on LAN
5. Post-install setup via SSH (Linda)
6. Configure storage, VMs, and 40G networking
7. Optionally cluster with main PVE

## References

- Installation ISO on [Ventoy Multi-Boot USB](/config/ventoy-usb.md)
- Main PVE server at [PVE Proxmox](/hardware/pve-proxmox.md)
