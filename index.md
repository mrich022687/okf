---
type: Index
title: RichAI Homelab — Knowledge Base
description: Master index of all documented systems, hardware, projects, and configurations
tags: [index, homelab, richai]
timestamp: 2026-06-30T04:00:00Z
---

# RichAI Homelab Knowledge Base

## Projects

- [LarryChat](projects/larrychat.md) — Self-hosted team chat with AI agents

## Hardware

- [RichAI Cluster](hardware/richai-cluster.md) — 3-node Proxmox cluster
- [Compaq 8200](hardware/compaq-8200.md) — PVE3 node

## Logs

- [Changelog](log/changelog.md) — Chronological work log
- [NVIDIA Driver Saga](log/nvidia-driver-saga.md) — GPU driver recovery

## Network

- Tailscale mesh: pve, pve2, pve3, Q1900M, laptop-ubuntu, Moto Root
- TP-Link TL-SG108 switch (sub-1ms latency)
- All servers bridged via vmbr0 with STP

## Containers

| Container | Host | IP | Agent |
|-----------|------|----|-------|
| CT 206 | pve2 | 192.168.12.215 | Larry (coder) |
| CT 106 | pve2 | (varies) | Linda (admin) |
| VM 100 | pve | 192.168.12.200 | Voice pipeline (offline) |

## Config

- Backup script: `/root/backup-pve.sh` (daily snapshots)
- ZFS: `tank` on pve, `tank2` on pve2
- Credential vault: GPG-encrypted

---

*Last updated: 2026-06-30*
