---
type: MobileDevice
title: Moto Root — Rooted Android Phone
description: Primary Android device for development. Rooted, runs Termux. Used for CAN Commander, Retro Games, and voice pipeline testing.
tags: [android, rooted, termux, development, phone]
timestamp: 2026-06-21T18:15:00Z
resource: ssh://u0_a394@100.97.184.43:8022
---

# Moto Root — Rooted Android Phone

## Overview
Michael's rooted Motorola phone, used as the primary development target for Android apps.

- **SSH:** `u0_a394@100.97.184.43:8022` (via pve, `ssh moto-root`)
- **ADB TCP:** `setprop service.adb.tcp.port 5555`
- **Termux:** SSH server running on port 8022
- **Root:** Full su access

## APK Installation Method
```bash
# Transfer via PVE
cat app-debug.apk | ssh -p 8022 u0_a394@100.97.184.43 'cat > /data/data/com.termux/files/home/app.apk'

# Install
ssh -p 8022 u0_a394@100.97.184.43 \
  "su -c 'cp /data/data/com.termux/files/home/app.apk /data/local/tmp/app.apk && pm install -r /data/local/tmp/app.apk'"
```

## Installed Apps
- **CAN Commander** — CAN bus reverse engineering app
- **Retro Games** — Tanks, Pong, Snake collection
- **Voice pipeline components** — STT/TTS testing

## Notes
- SSH routed through PVE server (pf NAT)
- SCP can timeout on mobile networks; use `cat | ssh` pipe instead
- `cmd package install` as fallback if `pm install` fails
