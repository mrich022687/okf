---
type: Log
title: Changelog
description: Running log of significant work on the OKF project.
tags: [changelog, log]
timestamp: 2026-06-26T20:30:00Z
---

# Changelog

## 2026-06-27 — pve3 P4 Fan Fix — 36°C Temp Drop

- **pve3 cooling crisis discovered:** P4 blower fan on FAN2 header was running at idle speed while CPU fan header ran at 100%. P4 hit 88°C under load — only 3°C from throttle.
- **Fix:** 3-way fan splitter connecting both CPU fan + P4 blower to the CPU fan header
- **Results:** Peak temp dropped from **88°C → 52°C** (-36°C), idle from **36°C → 32°C**, throttle margin from 3°C → 39°C
- **Lesson:** Auxiliary fan headers on older SFF boards may default to low speed — always verify with load testing
- **OKF:** Updated compaq-8200.md (full cooling section), gpu-setup.md (thermal performance table + critical fix note), changelog.md

## 2026-06-26 — pve3 GPU, Root Disk Fix, ZFS Cleanup, VM 101 Destroyed

- **Root disk:** Extended from 56 GB → 128 GB (75% → 34% used), 72 GB of LVM space reclaimed
- **ZFS pool tank:** Cleared 1,163 permanent errors — all 3 drives ONLINE, scrub running
- **VM 101 (Windows 11):** Destroyed + cleaned up due to ZFS corruption. License preserved on VM 201 (pve2) — same SMBIOS UUID `74a66e32-d27c-4a5d-af1b-63d0fac6508d`
- **iLO:** Diagnosed embedded NAND flash failure — "write-verify test failure" + "update nand failed". Fix: insert microSD card in internal slot
- **pve3 (Compaq 8200):** Tesla P4-8GB fully operational — driver 580.167.08, CUDA 13.0
- **pve3 GPU install:** BIOS integrated graphics enabled (DisplayPort output), GT 635 removed, NVIDIA .run installer used (APT repos failed due to kernel/driver version conflicts)
- **pve3 persistence:** Systemd service created, auto-load configured, nouveau blacklisted
- **Cluster:** pve3 now at 3 nodes fully quorate with P4 compute capacity
- **OKF:** GPU config, compaq-8200, cluster, and changelog all updated

## 2026-06-26 — GPU Install, Cluster Update, Storage Rename

- **Hardware:** 2× Tesla P4 installed in PVE2 (DL360 Gen9) — stress tested, 0 errors, max 65°C
- **GPU:** CT 106 (Hermes agent) migrated to PVE2 — has full GPU access to both P4s
- **Cluster:** Updated to 3-node config documentation (pve + pve2 + pve3)
- **Storage:** Linda renamed all `ilo-backups`/`ilo-isos` → `q1900-backups`/`q1900-isos` cluster-wide
- **Network:** TP-Link TL-SG108 switch verified — sub-1ms latency, 0% loss
- **PVE:** Boot disk nearly full (56/56GB) — needs cleanup

## 2026-06-25 — PVE3 Cluster Join, Switch Install

- PVE3 (Compaq 8200) joined richai-cluster as 3rd node
- TP-Link TL-SG108 switch installed replacing old router
- All server NICs bridged to vmbr0 with STP

[Earlier entries truncated for brevity]
