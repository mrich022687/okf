---
type: Config
title: Backup Strategy
description: Backup locations, schedules, and procedures for all critical systems.
tags: [backup, strategy, recovery]
timestamp: 2026-06-21T18:15:00Z
---

# Backup Strategy

## Critical Backups

### Container Backups
| Source | Format | Location | Frequency |
|--------|--------|----------|-----------|
| CT 106 (Hermes coder) | vzdump LXC | `/tank/dump/dump/` on PVE | On-demand |
| CT 106 (Hermes coder) | tar.zst copy | `/home/michael/` on Q1900M | On-demand |
| Config files | tar.gz | `/srv/backups/` on Q1900M | Periodic |

### Latest Backup
- **File:** `vzdump-lxc-106-2026_06_21-16_33_34.tar.zst` (999M)
- **Location:** PVE `/tank/dump/dump/` and Q1900M `/home/michael/hermes-ct106-backup.tar.zst`
- **Date:** 2026-06-21

### VM Snapshots on PVE
| VM | Snapshot | Date |
|----|----------|------|
| VM 100 (ubuntu-gnome) | pre-backup-20260615 | 2026-06-15 |
| VM 101 (windows-11) | clean-install, pre-backup-20260615 | 2026-06-15 |
| VM 102 (fedora) | pre-backup-20260615 | 2026-06-15 |
| CT 106 (hermes) | hermes-golden | Pre-dates session |

## USB Drives (Backup Targets)
See [USB Drives](usb-drives.md) for current USB drive inventory.
