---
type: Config
title: Backup Strategy
description: Automated nightly backups to the Q1900. Snapshot mode, zstd compression, keeps last 3.
tags: [backup, strategy, recovery, q1900]
timestamp: 2026-06-26T12:00:00Z
---

# Backup Strategy

## Backup Target: q1900 (192.168.12.208)
- **Storage name:** `q1900` (NFS mount at `/mnt/q1900`)
- **Server:** Q1900 at 192.168.12.208, share `/srv/backups`
- **Capacity:** 457 GB total, ~251 GB free (40% used)

## Schedule
- **When:** Nightly at 2:00 AM
- **What:** VMs 100-104 (all VMs + containers)
- **Mode:** Snapshot (zero downtime)
- **Compression:** zstd
- **Retention:** keep-last=3 (oldest auto-pruned)

## Stored in vzdump.cron (cluster-wide)
```
0 2 * * * root vzdump 100 101 102 103 104 --mode snapshot --compress zstd --storage q1900 --prune-backups keep-last=3
```

## Existing Backups (as of June 26)

| VM/CT | Latest | Size |
|-------|--------|------|
| VM 100 (Ubuntu Gnome) | June 21 | 62 GB |
| VM 101 (Windows 11) | June 21 | 25 GB |
| VM 102 (Fedora) | June 15 | 3.5 GB |
| VM 200 (OPNsense) | June 15 | 767 KB |
| CT 103 (Website) | June 21 | 271 MB |
| CT 104 (k3s) | June 21 | 386 MB |
| CT 106 (Larry on pve2) | June 21 | 999 MB |

## Notes
- Storage was renamed from `ilo-backups` to `q1900` on 2026-06-26
- All references updated across all 3 cluster nodes, fstab, and cron files
- Old storage name `ilo-backups` fully removed — no confusing references remain
- ISO share also renamed: `ilo-isos` → `q1900-isos`
- No secondary backup target configured (Q1900 is single point of failure for backups)
