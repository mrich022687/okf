---
type: Container
title: Hermes Coder Container
description: The Hermes AI agent container (Larry). Lives on pve2 (DL360 Gen9). Independent of main pve — stays up when pve crashes.
tags: [hermes, container, lxc, ai, agent, pve2]
timestamp: 2026-06-29T17:10:00Z
resource: ssh://root@192.168.12.50 (CT 206)
---

# Hermes Coder Container (CT 206)

> **IMPORTANT:** This container was **migrated from CT 106 on pve to CT 206 on pve2**. All references to "CT 106" in old docs are stale.

## Location & Independence

- **Host:** pve2 (192.168.12.50) — HP ProLiant DL360 Gen9
- **CT ID:** 206
- **IP:** 192.168.12.215/24 (eth0 via vmbr0)
- **Tailscale:** 100.125.72.72
- **Gateway:** 192.168.12.1
- **SSH:** `root@192.168.12.215` (not directly accessible; use `ssh pve2` then `pct enter 206`)

**Key fact:** This container runs on pve2 — a completely separate physical machine from pve. When pve crashes or reboots (e.g. the watchdog-triggered crashes in June 2026), this container stays online. The user (Michael) can still reach me via Telegram, which has its own internet connection independent of the Proxmox cluster.

## Overview

The container running the Hermes AI agent (Larry). This is the active session.

- **VMID:** 206 (was 106 on pve — migrated June 2026)
- **Hostname:** hermes
- **OS:** Ubuntu 24.04 (template)
- **Specs:** 2 cores, 4GB RAM, 20G rootfs
- **IP:** 192.168.12.215/24
- **Profile:** `coder`
- **RootFS:** `pve2-extra:206/vm-206-disk-0.raw` (NOT on tank — independent of pve storage)
- **Model:** DeepSeek v4 Flash (via DeepSeek provider)

## Network Topology

```
Internet → Telegram → Michael (Telegram client)
              ↕
         Larry (this container)
              ↕
         pve2 (192.168.12.50) ←→ pve (192.168.12.132) via LAN
              ↕
         pve3 (192.168.12.163)
              ↕
         Q1900M (192.168.12.208) — K3s worker node
```

When **pve goes down**: LarryChat backend, K3s control plane, GPU dashboard, and OKF (tank storage) all become unreachable. But Larry stays alive because he's on pve2 and communicates with Michael via Telegram (internet), not through the internal cluster.

## Storage

- **RootFS:** `pve2-extra:206/vm-206-disk-0.raw` (20G, 13G used)
- **Access to tank:** Only when pve is online (NFS mount)
- **Local storage:** Self-contained on pve2

## SSH Config

- All host aliases in `/root/.ssh/config`
- 8+ devices accessible when pve is up
- When pve is down, only pve2, pve3, and Q1900M are reachable

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

| Backup | Location | Date | Size |
|--------|----------|------|------|
| vzdump CT 106 (old) | `/tank/dump/dump/` | Jun 21, 23, 26 | ~1GB each |
| Latest pre-migration | `/tank/dump/dump/vzdump-lxc-106-2026_06_26-07_43_06.tar.zst` | 2026-06-26 | 1.1GB |
| On pve2 | pve2-extra storage | Not yet backed up | — |

**NOTE:** Since migration to CT 206 on pve2, a new backup needs to be configured targeting the pve2 storage.

## References
- [PVE Server](hardware/pve-proxmox.md)
- [DL360 Gen9 / pve2](hardware/dl360-gen9.md)
- [Network Topology](network/topology.md)
