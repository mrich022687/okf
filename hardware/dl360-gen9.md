---
type: Server
title: DL360 Gen9
description: HPE DL360 Gen9 1U rack server — secondary Proxmox host (pve2) w/ iLO 4. Clustered into richai-cluster w/ main PVE. 40G Mellanox link, 146GB SAS storage added.
tags: [dl360, gen9, hpe, server, proxmox, ilo, pve2, cluster, mellanox]
timestamp: 2026-06-23T03:05:00Z
---

| DL360 Gen9
|
||| Status: ✅ Clustered to Main PVE — Fully Operational |
|
The DL360 Gen9 is racked, powered on, with **Proxmox VE 9.2** running at **192.168.12.50**. iLO 4 configured for remote management. Passwordless SSH access from main PVE host.

**Cluster Status:** Joined `richai-cluster` as node 2 🎉
- Node 1: `pve` (main server, 192.168.12.132)
- Node 2: `pve2` (this server, 192.168.12.50)
- Both nodes visible in Proxmox web GUI

## Current Configuration

- **Model:** HPE ProLiant DL360 Gen9 (1U rack)
- **Hostname:** `pve2.lan`
- **IP:** 192.168.12.50/24 (vmbr0 via nic4)
- **Gateway:** 192.168.12.1
- **DNS:** 1.1.1.1
- **Root Password:** `mcrart8794!`
- **Role:** Secondary Proxmox node, **clustered** with main PVE via `richai-cluster`
- **OS:** Proxmox VE **9.2** (Trixie/Debian 13) — fully updated ✅
- **Storage Controller:** HP Smart Array P440ar
- **Repos:** No-subscription enabled, enterprise repo disabled

## Storage

### Array A (OS — SATA SSD)
- **Physical Drive:** Bay 8 (200 GB SATA SSD)
- **Logical Volume:** RAID 0, 186.28 GB
- **Usage:** Proxmox OS (`/` + swap + local-lvm)

### Array B (Extra — SAS HDD) 🆕
- **Physical Drive:** Bay 7 (146 GB SAS HDD)
- **Logical Volume:** RAID 0, 136.7 GB
- **Filesystem:** ext4, label `pve2-extra`
- **Mount:** `/mnt/pve2-extra` (fstab persistent)
- **Proxmox Storage:** Added as `pve2-extra` (dir type) — active and available
- **Content:** ISOs, templates, backups, containers, snippets
- **Free Space:** ~127 GB

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

## 40G Mellanox ConnectX-3 Pro

Both servers have matching HP FlexibleLOM ConnectX-3 Pro cards:
- **Main PVE:** `eno49d1` (altname enp4s0d1) — MAC 94:40:c9:7a:de:e2
- **PVE2:** `nic4` (altname enp4s0d1) — MAC 94:40:c9:7e:a2:b2

**Current Status:** ✅ **WORKING!** 40 Gbps link established.

**Solution:** The cable was plugged into the card's Port 1, which defaults to InfiniBand mode. The `mlx4_core` module parameter `port_type_array=2,2` (forces both ports to Ethernet) fixed it. The module needed to be reloaded with the parameter for it to take effect — modprobe.d config alone wasn't enough until module reload.

**Link details:**
- Main PVE: **eno49d1** (Port 2) at 10.10.10.1/30, MTU 9000
- PVE2: **enp4s0** (Port 1) at 10.10.10.2/30, MTU 9000
- Speed: **40 Gbps** (40000baseCR4/Full), latency ~0.19ms
- Real-world throughput: **26 Gbps** (verified via iperf3)
- Connection: Direct-attach copper cable between servers (no switch)
- Config made permanent in `/etc/network/interfaces` on both sides

## To-Do

1. ✅ Rack the server
2. ✅ Connect power, networking, and iLO dedicated port
3. ✅ Create logical drive on RAID controller
4. ✅ Configure iLO 4 (dedicated port, DHCP, user accounts, DNS name)
5. ✅ Boot from Ventoy USB, install Proxmox VE
6. ✅ Configure IP on LAN (192.168.12.50)
7. ✅ Run `apt dist-upgrade` — security patches installed
8. ✅ Fix 40G Mellanox link — port_type_array=2,2, module reload, IPs configured, 26 Gbps verified
9. ✅ Configure 40G networking between pve and pve2
10. ✅ Cluster with main PVE (richai-cluster)
11. ✅ Add 146 GB SAS drive (Bay 7) → RAID 0 → ext4 → Proxmox storage `pve2-extra`

## GPU
- **1× NVIDIA Tesla P4 (8GB)** — in PCIe slot (08:00.0)
- Being configured; currently used for WiFi adapter setup
- Connected to main PVE via 40G link (10.10.10.0/30)

## Setup Lessons Learned

### Network During Initial OS Installation
- **Only connect the regular Ethernet port** during initial server setup / OS installation
- **Do NOT connect the 40G/10G card cable** yet — the card's driver may not be recognized by the boot environment or installer, and a cable plugged into an unrecognized card *prevents* the regular NIC from getting a link
- The Mellanox ConnectX-3 Pro (or any add-on NIC) can confuse the installer's network detection
- After the OS is installed and the 40G card driver is loaded, cable the 40G link and configure it separately
- Same principle applies to most servers: boot with only the management NIC connected; add high-speed interconnects post-install

## References

- Installation ISO on [Ventoy Multi-Boot USB](/config/ventoy-usb.md)
- Main PVE server at [PVE Proxmox](/hardware/pve-proxmox.md)
