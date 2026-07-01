|---
type: Server
title: HP Compaq 8200 Elite SFF — PVE3
description: Third Proxmox cluster node, i7-2600, 30GB RAM, 168GB SSD, Tesla P4-8GB.
tags: [proxmox, pve3, compaq, sff, tesla, p4]
timestamp: 2026-06-26T21:15:00Z
---

# HP Compaq 8200 Elite SFF — pve3

|||| Status: ✅ Proxmox VE 9.2.2 — Cluster Node 3, Tesla P4 Online |

Third node in the richai-cluster. Equipped with a Tesla P4-8GB for compute workloads. Intel HD 2000 integrated graphics handles display output.

## Specs

- **CPU:** Intel Core i7-2600 @ 3.4GHz (4C/8T)
- **RAM:** 30GB DDR3
- **Storage:** 168GB SSD (ext4, non-ZFS)
- **Network:** Onboard 1GbE → 192.168.12.163
- **GPU:** Tesla P4-8GB (NVIDIA 580.167.08, CUDA 13.0)

## GPU Installation Notes

The Tesla P4 was installed with integrated graphics as the primary display adapter:

1. **BIOS Configuration:** Enabled integrated graphics (i915) → set as primary display → DisplayPort output
2. **GT 635 Removed:** Old GeForce GT 635 (Kepler) was a stopgap display card — removed once onboard video was active
3. **Driver Install:** Used NVIDIA `.run` installer (`NVIDIA-Linux-x86_64-580.167.08.run`) — APT packages failed because:
   - Debian trixie `nvidia-driver` (550.x) couldn't compile on Proxmox kernel 7.0.2-6-pve
   - CUDA repo had mixed versions — 610.x dropped Pascal support, 590.x had APT dependency conflicts
4. **Persistence:** Systemd service created at `/etc/systemd/system/nvidia-persistenced.service`
5. **Auto-load:** Kernel modules configured in `/etc/modules-load.d/nvidia.conf`
6. **Nouveau:** Blacklisted in `/etc/modprobe.d/blacklist-nouveau.conf`

## Cooling — P4 Blower Fan Fix ⚠️

### The Problem

pve3 has a blower fan attached to the Tesla P4's heatsink via **FAN2** header. The BIOS was running this header at a low idle speed while the CPU fan header ran at full speed. This caused the P4 to hit **88°C** under sustained GPU load — only 3°C below thermal throttle (91°C), with only ~35W sustained power draw.

### The Fix

Used a **3-way fan splitter** to connect both the CPU heatsink fan AND the P4 blower fan to the **CPU fan header** (which runs at 100% in BIOS). Now both fans run at full speed simultaneously.

### Before vs After — Stress Test Results (2026-06-27)

| Metric | Before (FAN2) | After (CPU header splitter) | Improvement |
|--------|:-------------:|:--------------------------:|:-----------:|
| Idle temp | 36°C | 32°C | -4°C |
| Peak temp (sustained) | **88°C** | **52°C** | **-36°C** |
| Sustained power | ~35W | ~32W | — |
| GPU utilization | 97-98% | 97-98% | Same |
| Thermal throttle margin | **3°C** | **39°C** | ✅ Huge |
| Cool-down | Slow | Rapid (drops ~1°C/sec after load) | ✅ |

**Stress test method:** 30 sequential ollama llava:7b inferences + 8 rounds of 4× concurrent inferences = continuous 98% GPU util for ~5 minutes.

### Lesson Learned

On the Compaq 8200 (and likely many older SFF/Dell Optiplex/HP EliteDesk boards):
- **CPU fan header** runs full BIOS speed (or PWM controlled)
- **Auxiliary fan headers** (FAN2, FAN3, SYS_FAN) may default to idle/low speed
- Always verify fan header speeds with a known load — don't assume all headers are equal
- A simple fan splitter on the CPU header is the cheapest fix if you only need 2 fans

## Setup

- **OS:** Proxmox VE 9.2.2
- **Hostname:** pve3.lan
- **Storage:** `local` — ~46GB free after boot
- **BIOS Settings:** VTx En, VTd En, DEP En, Intel TXT Dis

## Cluster

- Joined `richai-cluster` via manual corosync key copy (June 25, 2026)
- Quorate with 3 nodes

## Gaming Services

Sunshine game streaming host + PCSX2/Dolphin emulators for Moonlight clients (like the RG505).

### Storage

- **LV:** `/games` — 80 GB ext4 on thin pool (88 GB total)
- **Game library:** `/games/PS2/` — 7 PS2 ISOs, ~20 GB used, 55 GB free
- **Mount:** systemd mount at `/etc/systemd/system/games.mount`

### Services Running

| Service | Port | Detail |
|---------|------|--------|
| Sunshine | TCP 47984, 47989, 47990, 48010 | Game streaming host |
| Sunshine | UDP 47998-48010 | Video/audio stream data |
| Ollama | TCP 11434 | LLM inference |
| Xvfb | :99 | Virtual framebuffer 1920x1080 for Sunshine |

### GPU Streaming Config

- **Encoder:** h264_vulkan + hevc_vulkan (Tesla P4)
- **Capture:** KMS (requires `nvidia-drm.modeset=1`)
- **Kernel param:** `/etc/modprobe.d/nvidia-drm-modeset.conf` — `options nvidia_drm modeset=1`

### Emulators

| Emulator | Type | Flatpak |
|----------|------|---------|
| PCSX2 v2.6.3 | PS2 | `net.pcsx2.PCSX2` |
| Dolphin | GC/Wii | `org.DolphinEmu.dolphin-emu` |

### UFW Rules

All ports locked to `192.168.12.0/24`:
- TCP: 47984, 47989, 47990, 48010 (Sunshine)
- UDP: 47998-48010 (Sunshine streaming)

### Known Issues

- **Headless Tesla P4 + KMS:** Modeset=1 required for KMS capture. Virtual connector may not produce frames — Xorg headless config at `/etc/X11/xorg.conf.sunshine` as fallback
- **Xvfb only works for X11 apps** — can't provide Vulkan surfaces for capture

## References

- [Game Streaming](/projects/game-streaming.md)
- [richai-cluster](/hardware/richai-cluster.md)
