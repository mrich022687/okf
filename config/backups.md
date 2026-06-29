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

## Backup Monitor (Added Jun 29)
A backup health monitor script runs twice daily (6 AM and 6 PM) and reports to Michael via Telegram.

- **Script:** `/root/.hermes/profiles/main/scripts/backup-monitor.sh` (no_agent mode)
- **Cron job:** `Backup Health Monitor` (job_id: 5023dd4ae05f)
- **Schedule:** `0 6,18 * * *` (6 AM and 6 PM MST/MDT)
- **Delivery:** Back to origin chat (Telegram DM with Michael)

**What it checks:**
1. Cluster vzdump cron is active
2. Q1900 NFS storage is mounted (via pve2)
3. Latest backup per VM/CT across all dump locations (Q1900, tank-dump, pve2)
4. Backup freshness (green <24h, yellow 24-48h, red >48h)
5. pve3 online status

**Backup health (as of Jun 29):**
| Status | VM/CT | Age | Storage |
|--------|-------|-----|---------|
| ✅ Current | VM 102, VM 103, VM 104, VM 201, CT 206 | 7-14h | Q1900 / pve2-tank |
| ❌ Stale | VM 100 (200h), VM 101 (199h), CT 106 (80h), VM 200 (350h) | Days | Various |

**Stale VMs not in cluster backup schedule:** 101, 106, 200 — may need to be added if they're still needed.

## NFS Mount Notes (Fixed Jun 29)
All three Proxmox nodes had `hard` NFS mounts for Q1900, which causes D-state hangs when the Q1900 (low-power Atom box) has latency spikes. All fixed to `soft`:

| Node | Before | After |
|------|--------|-------|
| **Main PVE** | hard | soft ✅ |
| **pve2** | hard → soft (earlier fix) | soft ✅ |
| **pve3** | hard | soft ✅ |

## Stored in vzdump.cron (cluster-wide)
```
0 2 * * * root vzdump 100 101 102 103 104 --mode snapshot --compress zstd --storage q1900 --prune-backups keep-last=3
```

## Existing Backups (as of June 26)

|| VM/CT | Latest | Size |
||-------|--------|------|
|| VM 100 (Ubuntu Gnome) | June 21 | 62 GB |
|| VM 101 (Windows 11) | June 21 | 25 GB (destroyed — ZFS corruption) |
|| VM 102 (Fedora) | June 15 | 3.5 GB |
|| VM 200 (OPNsense) | June 15 | 767 KB |
|| VM 201 (Win11 Pro — pve2) | June 28 | ~25 GB |
|| CT 103 (Website) | June 21 | 271 MB |
|| CT 104 (k3s) | June 21 | 386 MB |
|| CT 206 (Larry on pve2) | June 21 | 999 MB |

## Notes
- VM 101 destroyed due to ZFS pool corruption on main PVE — license preserved and recreated as VM 201 on pve2 (see [VM 201 docs](containers/vm201-windows11.md))
- Storage was renamed from `ilo-backups` to `q1900` on 2026-06-26
- All references updated across all 3 cluster nodes, fstab, and cron files
- Old storage name `ilo-backups` fully removed — no confusing references remain
- ISO share also renamed: `ilo-isos` → `q1900-isos`
- No secondary backup target configured (Q1900 is single point of failure for backups)
