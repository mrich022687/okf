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
- Key-based SSH to all 8 devices
- SSH config in `/root/.ssh/config`

### Network & Infrastructure
- Tailscale tailnet management
- Proxmox VM/container provisioning
- K3s cluster setup
- NFS/Samba share configuration
