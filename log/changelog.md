---
type: Log
title: Changelog
description: Running log of significant work on the OKF project.
tags: [changelog, log]
timestamp: 2026-06-26T20:30:00Z
---

# Changelog

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
