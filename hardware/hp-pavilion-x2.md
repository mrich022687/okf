---
type: Hardware
title: HP Pavilion x2 Detachable (Amanda's Computer)
description: Amanda's personal HP tablet — currently running Lubuntu, pending reinstall.
tags: [hardware, hp, tablet, amanda, client]
timestamp: 2026-06-27T21:00:00Z
---

# HP Pavilion x2 Detachable (Amanda's Computer)

## Overview

Amanda's personal HP tablet computer. Currently running Lubuntu 24.04 (was Linux Lite 7.0, kept freezing — Lubuntu is lighter and cleaner). The OS needs to be reinstalled onto internal storage (was accidentally installed on SD card).

## Specs

| Component | Detail |
|-----------|--------|
| **Model** | HP Pavilion x2 Detachable |
| **CPU** | Intel Atom/Celeron x5-E8000 / J3xxx / N3xxx series |
| **RAM** | 1.8 GB (very limited — uses swap to compensate) |
| **Internal Storage** | 29 GB eMMC (mmcblk0) |
| **SD Card** | 116 GB (mmcblk1) — currently has old Lubuntu install on it |
| **GPU** | Intel Integrated Graphics (no NVIDIA — CPU only) |
| **Network** | WiFi (wlp1s0) |
| **Form Factor** | Detachable tablet with keyboard |

## Network

| Field | Value |
|-------|-------|
| **IP** | 192.168.12.232/24 (DHCP) |
| **Hostname** | manderz-HP-Pavilion-x2-Detachable |
| **Tailscale** | Not yet installed |
| **SSH** | `ssh manderz@192.168.12.232` (password: base variant, all lowercase) |

## Software

| Field | Value |
|-------|-------|
| **OS** | Linux Lite 7.0 (Ubuntu 24.04) — being replaced with Lubuntu 24.04 |
| **SSH** | OpenSSH 9.6p1 — enabled and working |

## Issues & Notes

- Secure Boot is enabled — must enroll MOK key when booting any unsigned Linux installer
- Boot menu: press Esc at startup, then F9 for boot menu
- Only 1.8 GB RAM — Chrome alone can consume >1 GB
- System updates were pending (many packages)
- SD card was used as boot drive accidentally — clean install needed on mmcblk0 instead

## References

- [GPU Setup](../config/gpu-setup.md)
