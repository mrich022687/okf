---
type: Project
title: Game Streaming & Emulation
description: PS2 game ripping library, PCSX2 + Dolphin emulators, Sunshine/Moonlight streaming from pve3 to RG505 using Tesla P4 Vulkan encoding.
tags: [gaming, sunshine, moonlight, ps2, emulation, pcsx2, dolphin, pve3, rg505, tesla-p4]
timestamp: 2026-06-30T21:50:00Z
---

# Game Streaming & Emulation

## Overview

Game streaming setup using Sunshine (host) on pve3 with Tesla P4 GPU Vulkan hardware encoding, streaming to Moonlight on the Anbernic RG505. PCSX2 (PS2) and Dolphin (GameCube/Wii) emulators installed. PS2 discs are being ripped and stored locally on pve3 for direct access.

## Architecture

```
┌─────────┐   5GHz WiFi    ┌──────────┐
│  RG505  │ ◄───────────── │   pve3   │
│Moonlight│                │Sunshine  │
│ (client)│                │  (host)  │
└─────────┘                │  Tesla P4│
                           │  Vulkan  │
                           └──────────┘
```

- **Host:** pve3 (192.168.12.163), Tesla P4-8GB
- **Client:** Anbernic RG505, Android, Moonlight (root version)
- **Streaming:** Sunshine -> Moonlight, H.264/H.265 via Vulkan hardware encode
- **Emulators:** PCSX2 (PS2), Dolphin (GameCube/Wii) — both Flatpak installs
- **Game storage:** `/games/PS2/` on 80 GB ext4 logical volume

## pve3 Gaming Host Setup

### Storage

- **LV:** `/games` — 80 GB ext4 on thin pool LV (88 GB total)
- **Used:** ~20 GB (7 PS2 ISOs)
- **Free:** ~55 GB
- **Path:** `/games/PS2/` for all game ISOs

### GPU

- **Tesla P4-8GB** (NVIDIA 580.167.08 driver)
- **Vulkan encoding:** h264_vulkan + hevc_vulkan confirmed working
- **KMS capture requires:** `nvidia-drm.modeset=1` kernel parameter

### Services

| Service | Port | Detail |
|---------|------|--------|
| Sunshine (web UI) | TCP 47990 | Config interface, https://192.168.12.163:47990 |
| Sunshine (pairing) | TCP 47984 | Moonlight pairing/HTTPS |
| Sunshine (http) | TCP 47989 | HTTP endpoint |
| Sunshine (RTSP) | TCP 48010 | Stream control/RTSP handshake |
| Sunshine (video) | UDP 47998-48010 | Video/audio stream data |
| Ollama | TCP 11434 | LLM inference |

### Emulators

| Emulator | Install Method | Binary Path |
|----------|---------------|-------------|
| PCSX2 v2.6.3 | Flatpak (Flathub) | `flatpak run net.pcsx2.PCSX2` |
| Dolphin Emulator | Flatpak (Flathub) | `flatpak run org.DolphinEmu.dolphin-emu` |

### Sunshine Config

- **Config path:** `/root/.config/sunshine/sunshine.conf`
- **Service:** `/etc/systemd/system/sunshine.service` (DISPLAY=:99, after xvfb.service)
- **Auth:** user `michael`, password `mcrart8794`
- **CSRF origins:** `https://192.168.12.163:47990,http://192.168.12.163:47989`

### Headless Display Issues & Fixes

Sunshine on a headless Tesla P4 requires special setup:

1. **Xvfb virtual framebuffer:** Display :99 at 1920x1080x24
   - Process: `/usr/bin/Xvfb :99 -screen 0 1920x1080x24 +extension GLX +render -nolisten tcp`
2. **KMS capture fails without modeset:** NVIDIA driver doesn't support universal planes by default
   - **Fix:** `nvidia-drm.modeset=1` — persisted at `/etc/modprobe.d/nvidia-drm-modeset.conf`
   - Without this: "GPU driver doesn't support universal planes" error
3. **X11 capture crashes Sunshine:** SEGV because Xvfb is software-only (no Vulkan surface)
4. **NvFBC not available:** Prebuilt Sunshine binary lacks NvFBC support for Tesla cards
5. **AV1 not supported:** Tesla P4 (compute 6.1) lacks AV1 encoding hardware
6. **Xorg headless config generated:** `/etc/X11/xorg.conf.sunshine` — backup approach if KMS doesn't produce frames

**Current state (Jun 30):** KMS capture with `modeset=1` finds connector ID 75, Vulkan encoders load successfully, pairing + RTSP handshake work. Video frames not arriving at client — likely the nvidia-drm virtual connector isn't producing actual framebuffer content. Xorg headless config ready as fallback.

### Firewall (UFW)

All rules scoped to 192.168.12.0/24 LAN:

```
47984/tcp    ALLOW IN    Sunshine HTTPS pairing
47989/tcp    ALLOW IN    Sunshine HTTP
47990/tcp    ALLOW IN    Sunshine config UI
48010/tcp    ALLOW IN    Sunshine RTSP streaming
47998:48010/udp  ALLOW IN    Sunshine video/audio streaming
```

## PS2 Game Library

All ISOs stored at `/games/PS2/` on pve3. Ripped via `dd` from PS2 discs.

| # | Game | Size | Ripped |
|---|------|------|--------|
| 1 | Top Gun: Combat Zones | 579 MB | Jun 30 |
| 2 | Gran Turismo 3: A-Spec | 3.7 GB | Jun 30 |
| 3 | Gran Turismo 4 | 5.0 GB | Jun 30 |
| 4 | Sonic Mega Collection Plus | 4.4 GB | Jun 30 |
| 5 | Pirates of the Caribbean: At World's End | 1.3 GB | Jun 30 |
| 6 | Avatar: The Last Airbender | 1.6 GB | Jun 30 |
| 7 | Crash of the Titans | 3.1 GB | Jun 30 |
| **Total** | | **~20 GB** | |

### Ripping Process

```bash
# Disc ripping (pve3 has DVD-RW at /dev/sr0)
dd if=/dev/sr0 of=/games/PS2/game_name.iso bs=2048 status=progress

# Transfer from main PVE tank
scp /tank/data/game-isos/game.iso root@pve3:/games/PS2/
```

### Gran Turismo 4 Note

Originally ripped to main PVE at `/tank/data/game-isos/` (5.0 GB). Transferred to pve3 via SCP. Backup copy still on tank.

## RG505 Client

### Moonlight

- **Installed:** `com.limelight.root` (root version for GammaOS)
- **Method:** Downloaded APK, transferred via ADB over laptop, installed with `adb install`
- **Pairing host:** 192.168.12.163 (manual entry required — auto-discovery won't find it)
- **Status:** Pairing in progress — RTSP handshake works, video streaming being debugged

### Pending

- **Aurora Store:** Not yet installed (user wants to avoid Google Play)
- Daijisho frontend already configured with ROM paths on SD card

## Pitfalls

1. **P100 cannot do NVENC headless** — pure compute card, no display engine
2. **Tesla P4 works with Vulkan** but needs `modeset=1` for KMS capture
3. **UFW needs BOTH TCP and UDP** — TCP 48010 was initially missed
4. **Xvfb alone insufficient** — Sunshine can't capture Vulkan surfaces from it
5. **Prebuilt Sunshine lacks NvFBC** — needs custom build for Tesla Frame Buffer Capture
6. **pve3 only has 1GbE** — sufficient for 1080p, no 40G interconnect

## References

- [pve3 hardware](/hardware/compaq-8200.md)
- [RG505 handheld](/hardware/rg505.md)
- [richai-cluster](/hardware/richai-cluster.md)
