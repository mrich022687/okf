---
type: Server
title: DL360 Gen9
description: HPE DL360 Gen9 1U rack server — secondary Proxmox host with iLO 4 remote management.
tags: [dl360, gen9, hpe, server, proxmox, ilo]
timestamp: 2026-06-23T00:30:00Z
---

# DL360 Gen9

|| Status: iLO Configured — Proxmox Install Pending 🚧 |

The DL360 Gen9 is racked, powered on, and iLO 4 is configured for remote management. Proxmox VE 8 install pending via Ventoy USB.

## Current Configuration

- **Model:** HPE ProLiant DL360 Gen9 (1U rack)
- **Hostname:** `pve2.lan`
- **IP:** 192.168.12.50 (planned)
- **Role:** Secondary Proxmox node, to be clustered with main PVE
- **Networking:** 40G interlink via Mellanox ConnectX-3 Pro (main PVE side installed and detected)
- **OS:** Proxmox VE 8 (via Ventoy USB — install pending)
- **Storage:** HP Smart Array P440ar — RAID logical drive created (strip 64K, read-ahead, write-back with BBU)
- **Root Password:** Stored in GPG vault on main PVE

## iLO 4 Configuration

- **iLO IP:** 192.168.12.100 (DHCP from home router)
- **iLO DNS Name:** `pve2-ilo`
- **NIC Port Mode:** Dedicated ("On" — uses dedicated iLO RJ-45 port)
- **DHCP:** Enabled (IP assigned by ASUS router/switch)
- **iLO User Accounts:**
  - **Default Account:** `ILOMXQ80304TS` / password from iLO sticker
  - **Admin Account:** `Michael` / `mcrart8794!` (full admin privileges)
- **iLO Access:** Web GUI at https://192.168.12.100, SSH on port 22
- **Connected To:** ASUS router (used as switch) — dedicated iLO port cabled

## To-Do

1. ✅ Rack the server
2. ✅ Connect power, networking, and iLO dedicated port
3. ✅ Create logical drive on RAID controller
4. ✅ Configure iLO 4 (dedicated port, DHCP, user accounts, DNS name)
5. ◻ Boot from Ventoy USB, install Proxmox VE 8
6. ◻ Configure IP on LAN (192.168.12.50)
7. ◻ Post-install setup via SSH (Linda)
8. ◻ Configure 40G networking between pve and pve2
9. ◻ Cluster with main PVE

## References

- Installation ISO on [Ventoy Multi-Boot USB](/config/ventoy-usb.md)
- Main PVE server at [PVE Proxmox](/hardware/pve-proxmox.md)
