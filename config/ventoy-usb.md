---
type: Configuration
title: Ventoy Multi-Boot USB
description: Multi-boot USB drive with Ventoy containing 14 bootable ISOs for system installation and recovery.
tags: [ventoy, usb, multi-boot, isos]
timestamp: 2026-06-21T23:45:00Z
---

# Ventoy Multi-Boot USB

## Overview

A 59GB exFAT USB drive (WesData, `/dev/sdr`) with [Ventoy v1.1.05](https://www.ventoy.net) installed, containing 14 bootable ISOs. Just plug in, boot from USB, and pick an ISO from the Ventoy menu. Add or remove ISOs anytime by drag-and-drop — no reformatting needed.

## Contents (14 ISOs — 56G used / 3.5G free)

| # | ISO | Size | Purpose |
|---|-----|------|---------|
| 1 | **Proxmox VE 8** | 8.1G | Hypervisor installation |
| 2 | **Windows 11** | 7.9G | OS install / repair |
| 3 | **Ubuntu 26.04 Desktop** | 6.1G | Latest Ubuntu desktop |
| 4 | **Kali Linux** | 4.5G | Security / pentesting |
| 5 | **Linux Lite 7.0** | 3.8G | Lightweight for HP laptop |
| 6 | **Lubuntu 26.04** | 3.7G | Lightweight Ubuntu flavor |
| 7 | **Debian XFCE** | 3.6G | Lightweight Debian |
| 8 | **Debian Gnome** | 3.6G | Full Debian desktop |
| 9 | **Fedora KDE** | 3.2G | KDE desktop distro |
| 10 | **Linux Mint 22.3** | 2.9G | XFCE edition |
| 11 | **Ubuntu Server 26.04** | 2.8G | Server installation |
| 12 | **Fedora Workstation** | 2.7G | GNOME desktop distro |
| 13 | **OPNsense 25.7** | 2.1G | Firewall/router OS |
| 14 | **virtio-win** | 754M | VirtIO drivers for Windows VMs |

## How to Use

1. Plug into any computer
2. Boot from USB (select from BIOS boot menu)
3. Ventoy shows a menu of all ISOs
4. Pick one and it boots

## Building the Drive

- Formatted with Ventoy v1.1.05 (MBR, Secure Boot enabled)
- Data partition is exFAT — compatible with Windows, Linux, macOS
- ISOs sourced from [Q1900](/hardware/q1900m.md) at `/srv/isos/`
- See [USB Drives](usb-drives.md) for full drive inventory
