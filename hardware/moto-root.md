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


### OBD2 Adapters Tested

#### CH340/CH341A ELM327 v1.5 Clone
- **Vendor ID:** 1a86:7523, bcdDevice 0x8134
- **ELM327 version:** v1.5
- **Connection:** USB OTG via libusb in Termux (Python ctypes)
- **Baud:** 38400 (uses usb-serial-for-android baud formula, NOT Linux ch341.c formula)
- **Status:** Working — all OBD2 PIDs readable on 2015 Ford E-450
- **Script:** ~/test_real_init.py in Termux
- **Run as:** `su -c "export PATH=$PATH:/data/data/com.termux/files/usr/bin && timeout 60 python3 /data/data/com.termux/files/home/test_real_init.py"`
- **Note:** Must unbind from usbfs first if AndrOBD was using the device

#### CANable 2 (RH02 Plus / Joining)
- **VID/PID:** 0x16d0 / 0x117e
- **Firmware:** candleLight (gs_usb compatible)
- **Product string:** "CANable2 b158aa7 github.com/normaldotcom/canable2.git"
- **Status:** Detected but IFACE 1 BUSY on Android — use on proper Linux (Q1900M/laptop) with SocketCAN
- **Cable tap:** CAN-H / CAN-L wires (500 kbps for powertrain CAN)

#### OBDLink MX+ (Bluetooth)
- **MAC:** 00:04:3E:8A:E7:15
- **Protocol:** Bluetooth SPP, 115200 baud
- **Pair PIN:** 1234
- **Status:** Known working, paired with Moto Root. Used via AndrOBD or Serial BT Terminal + TCP bridge
