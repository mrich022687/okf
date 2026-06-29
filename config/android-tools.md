---
type: Config
title: Android Tools & Software Library
description: Repository of Android development tools, firmware images, and software stored on Q1900 NFS backup server
tags: [android, tools, firmware, adb, gammaos, library, q1900]
timestamp: 2026-06-29T17:00:00Z
---

# Android Tools & Software Library

## Location
**Q1900 NFS:** `/mnt/pve/q1900/Android tools and software/` (mounted on all 3 Proxmox nodes)
**Full path on Q1900:** `/srv/backups/Android tools and software/`

## Directory Structure
```
Android tools and software/
├── ADB_and_Fastboot/          (17 MB)
│   ├── platform-tools/        — adb.exe, fastboot.exe, AdbWinApi.dll, sqlite3.exe, etc.
│   └── drivers/               — Generic ADB USB drivers
├── GammaOS/                   (6.5 GB)
│   ├── RG505_GammaOSNext_Lite_v1.1/  — Extracted flash images (boot, dtbo, system, userdata, vbmeta, vendor_boot, vendor)
│   │   └── scripts/           — FlashPartitions.bat, FlashPartitions.sh, EraseUserData.bat/sh
│   │   └── UnisocDrivers/     — Unisoc VCOM drivers for Win7/8/10
│   └── RG505_GammaOSNext_Lite_v1.1.7z.001-005  — Original split archives (password: gammaos)
├── Unisoc_Drivers/            — Standalone Unisoc driver copies (empty, content in GammaOS/)
├── Emulator_APKs/             — Empty (reserved for future APK library)
└── Documentation/             — Empty (reserved for guides/notes)
```

## Contents

### ADB & Fastboot Tools
- **Source:** Android SDK platform-tools
- **Version:** Latest as of Jun 2026
- **Files:** adb.exe, fastboot.exe, AdbWinApi.dll, AdbWinUsbApi.dll, etc1tool.exe, hprof-conv.exe, sqlite3.exe, mke2fs.exe, make_f2fs.exe

### GammaOS Next Lite v1.1.0
- **Device:** Anbernic RG505
- **Base:** Android 14 / LineageOS 21
- **Google Services:** None (Lite version)
- **Flash images:** boot_custom.img, dtbo_custom.img, system_custom.img, userdata.img, vbmeta_custom.img, vendor_boot_custom.img, vendor_custom.img

### Unisoc VCOM Drivers
- **Supported OS:** Windows 7, 8, 10 (x86 and x64)
- **Used for:** Flashing RG505 (Unisoc T618 SoC) via fastboot/bootrom mode

## Also on Laptop
A working copy of GammaOS firmware and ADB tools lives at:
- `C:\Users\mrich\RG505_GammaOS\` — GammaOS flash files
- `C:\adb\platform-tools\` — ADB + fastboot (permanent install)
