---
type: Container
title: Hermes Coder Container
description: The Hermes AI agent container (CT 106). Runs DeepSeek model for coding and system administration. This is where Larry lives.
tags: [hermes, container, lxc, ai, agent]
timestamp: 2026-06-21T18:15:00Z
resource: ssh://root@192.168.12.132 (CT 106)
---

# Hermes Coder Container (CT 106)

## Overview
The container running the Hermes AI agent (Larry). This is the active session.

- **VMID:** 106
- **Hostname:** hermes
- **OS:** Ubuntu (template)
- **Specs:** 2 cores, 4GB RAM, 1GB swap, 20G root
- **IP:** 192.168.12.215/24
- **Profile:** `coder`
- **Home:** `/root/.hermes/profiles/coder/`
- **Model:** DeepSeek v4 Flash (via DeepSeek provider)

## Storage
- **ZFS subvol:** `tank:subvol-106-disk-0` (20G, 1.8G used)
- **Snapshot:** `hermes-golden` — golden snapshot of clean state

## SSH Config
- All host aliases in `/root/.ssh/config`
- 8 devices accessible

## Profile Structure
```
/root/.hermes/profiles/coder/
├── config.yaml
├── sessions.db
├── skills/
├── plugins/
├── cron/
├── memories/
└── cron/output/
```

## Backups
| Backup | Location | Date |
|--------|----------|------|
| vzdump CT 106 | `/tank/dump/dump/vzdump-lxc-106-2026_06_21-16_33_34.tar.zst` | 2026-06-21 |
| Config backup | `/tank/config-backup-20260615/` | 2026-06-15 |
| Copy on Q1900M | `/home/michael/hermes-ct106-backup.tar.zst` (999M) | 2026-06-21 |
