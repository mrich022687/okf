---
type: Changelog
title: Changelog
description: Chronological record of all work performed by Larry for Michael's homelab.
tags: [log, history, changelog]
timestamp: 2026-06-21T18:15:00Z
---

# Changelog

## 2026-06-21

### OKF Knowledge Base Created
- Created Open Knowledge Format bundle at `/tank/data/okf/` on PVE
- Documented all hardware, network, containers, projects, and config
- Synced to Q1900M at `/srv/backups/okf/`
- Set up for GitHub publishing

### Samba Name Change — Q1900M
- Changed NetBIOS name from `iLO` to `q1900`
- Changed server string from `iLO-NAS` to `q1900-NAS`
- Restarted smbd service

### Ford Upfitter Guide — File Cleanup
- Deleted duplicate: `2026-Ford-Super-Duty-Incomplete-Vehicle-Manual.pdf` (19MB)
- Kept: `2026-Ford-Upfitters-Guide.pdf`

### Container Backup
- Created vzdump of CT 106 (hermes): 999MB at `/tank/dump/dump/`
- Copied to Q1900M at `/home/michael/hermes-ct106-backup.tar.zst`

### Retro Games v1.3 — Tanks Per-Player Settings
- Each player in Tanks now has their own saved angle and power
  - P1: `p1Angle` (default 45°), `p1Power` (default 50)
  - P2: `p2Angle` (default 45°), `p2Power` (default 50)
- Current player's settings persist between turns
- Other player never sees the angle/power used against them
- Built and deployed to Moto Root

### USB Hub Power Cycle
- All USB drives on PVE hub re-enumerated after power cycle
- 5 WesData 64GB drives identified (flashed with various ISOs)
- Drive inventory updated

### Skills System
- Multiple skills created/updated for common workflows
- Skills stored at `/root/.hermes/profiles/coder/skills/`

## Earlier Sessions (Summary)

### Voice Pipeline Setup
- STT/TTS/LLM pipeline on VM100 with 2× Tesla P4
- PyTorch 2.6 XTTS fix applied (weights_only=False)
- Systemd services for LLM server and voice server

### CAN Commander Development
- Android APK for CAN bus reverse engineering
- Multiple bug fixes during live vehicle testing:
  - START button deadlock fix (ConnState enum)
  - ATMA non-blocking read loop with NO DATA re-entry
  - Real bonded device list (no placeholders)
  - Abstract CanController interface pattern
  - SCP pipe fallback for mobile network transfers

### SSH Access Setup
|- Key-based SSH to all 8 devices
|- SSH config in `/root/.ssh/config`
|
|## 2026-06-21 (Linda)
|
|### OKF Knowledge Base — Shared Repo
|- Cloned shared `okf` repo from `git@github.com:mrich022687/okf.git`
|- Updated index.md to be a shared KB (Larry + Linda)
|- Added Ventoy multi-boot USB concept doc
|- Added DL360 Gen9 hardware doc (pending setup)
|- Configured git for Hermes main agent
|
|### NFS Share — Fixed Stale Mount
|- Q1900 went offline (hard reset), NFS mounts on PVE hung
|- iLO (192.168.12.126) was reachable but main OS was down
|- After reboot, NFS required lazy unmount and remount
|
|### Ventoy Multi-Boot USB Created
|- Downloaded Ventoy v1.1.05 to PVE host
|- Installed on WesData drive (sdr, 59G)
|- Copied 14 ISOs from Q1900 `/srv/isos/`:
|  - Proxmox VE 8, Windows 11, Ubuntu 26.04 Desktop + Server
|  - Kali Linux, Linux Lite, Lubuntu, Debian XFCE + Gnome
|  - Fedora KDE + Workstation, Linux Mint, OPNsense, virtio-win
|- 56G used / 3.5G free
|
|### Lubuntu ISO — Transfer and Backup
|- Lubuntu 26.04 ISO was on Windows laptop desktop
|- User transferred via USB to Q1900
|- Copied to `/srv/isos/` on Q1900 for backup
|- Also added to Ventoy USB
|
|### Q1900 Naming Updated
|- References changed from "Q1900M iLO box" to "q1900" / "q1900m"
|- Memory and docs updated accordingly

## 2026-06-22

### DL360 Gen9 — Initial Setup
- Server racked, powered on, connected to network
- HP Smart Array P440ar — RAID logical drive created (64K strip, read-ahead, write-back with BBU)
- Hostname set to `pve2.lan`, IP 192.168.12.50 planned
- Proxmox VE 8 install pending (Ventoy USB)

### 40G Networking — Mellanox ConnectX-3 Pro
- Installed in main PVE server, detected at PCI 04:00.0 as `eno49d1`
- DL360 will get matching card for inter-server 40G link

### LXC 103 (richai-web) — Permanent DNS Fix
- DNS kept reverting to Tailscale MagicDNS (100.100.100.100), breaking cloudflared tunnel
- Set `pct set 103 -nameserver "1.1.1.1 8.8.8.8"` — survives reboots now
- Website (rich-ai.net) restored

### DL360 Gen9 — iLO 4 Configured
- iLO dedicated port mode set to "On" (dedicated RJ-45 port)
- iLO connected to ASUS router/switch
- DHCP enabled — IP assigned: 192.168.12.100
- DNS name set to `pve2-ilo`
- User account `Michael` created with full admin privileges (password: mcrart8794!)
- Default iLO account: `ILOMXQ80304TS` (sticker password)
- iLO accessible via web GUI at https://192.168.12.100 and SSH on port 22
- DL360 hardware doc updated in OKF

### DL360 Gen9 — Proxmox VE 8 Installed & Running
- Proxmox VE 8 reinstalled on pve2 — IP 192.168.12.50/24, gateway 192.168.12.1, DNS 1.1.1.1
- Enterprise repo disabled, no-subscription repo added
- Passwordless SSH access from main PVE host configured
- Root password: mcrart8794!
- `apt dist-upgrade` still running
- OKF hardware doc fully updated

### 40G Mellanox — ✅ FIXED! 40 Gbps Link Working
- **Root cause:** Cable was plugged into Port 1 which defaults to InfiniBand mode
- **Fix:** `mlx4_core` module parameter `port_type_array=2,2` forces both ports to Ethernet mode
- Module had to be reloaded with the parameter — modprobe.d alone wasn't enough until reload
- Link details:
  - Main PVE: **eno49d1** (Port 2) @ 10.10.10.1/30, MTU 9000
  - PVE2: **enp4s0** (Port 1) @ 10.10.10.2/30, MTU 9000
  - Speed: **40 Gbps** (40000baseCR4/Full), latency ~0.2ms
  - Direct cable between servers, no switch
- Config made permanent in `/etc/network/interfaces` on both sides

### CT 106 (Hermes/Larry) — Tailscale TUN Fix
|- Tailscale stopped working: `/dev/net/tun` missing in LXC container
|- Fixed: added `lxc.cgroup2.devices.allow: c 10:200 rwm` and TUN mount to config
|- Container restarted, TUN device now present
|- Tailscale re-auth needed: https://login.tailscale.com/a/69e24140146df

### Network & Infrastructure
|- Tailscale tailnet management
|- Proxmox VM/container provisioning
|- K3s cluster setup
|- NFS/Samba share configuration
## 2026-06-23

### Knowledge Base — Cleanup & Sync
- Fixed broken markdown in index.md (leading pipes from Linda's edit)
- Updated network topology: added pve2, iLO 4, 40G interconnect details
- Updated PVE hardware doc: added 40G Mellanox card, PCI device list, cluster role
- Created missing files: config/ssh-keys.md, projects/k3s-cluster.md
- Identified cluster issue: pve2's vmbr0 (192.168.12.50) is DOWN
  - Cluster shows 1/2 votes — not quorate until pve2's management NIC is connected
  - pve2 only reachable via 40G link at 10.10.10.2

### pve2 Hardware Captured
- 2x Xeon E5-2690 v4 (28C/56T @ 2.6GHz)
- 62 GB RAM
- 1x Tesla P4 GPU (GP104, 8GB)
- 1x Mellanox ConnectX-3 Pro (40GbE)
- 186GB SSD (OS) + 136GB SAS (storage)
