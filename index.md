---
type: Index
title: Larry's Knowledge Base
description: Complete documentation of all systems, configurations, projects, and work performed by Larry (Hermes AI agent) for Michael's homelab.
tags: [index, overview, homelab]
timestamp: 2026-06-21T18:15:00Z
---

# Larry's Knowledge Base

This is an [Open Knowledge Format](https://cloud.google.com/blog/products/data-analytics/how-the-open-knowledge-format-can-improve-data-sharing) bundle documenting everything Larry has done and will do for Michael's homelab.

## Quick Links

### Hardware
- [PVE Proxmox Server](hardware/pve-proxmox.md) — Main virtualization host
- [Q1900M](hardware/q1900m.md) — Linux Mint NAS / backup server
- [Z390](hardware/z390.md) — Main desktop (i9-9900K + P100)
- [Compaq 8200](hardware/compaq-8200.md) — Windows machine
- [Moto Root](hardware/moto-root.md) — Rooted Android phone
- [Moto G Power](hardware/moto-g-power.md) — Secondary Android phone

### Network
- [Topology](network/topology.md) — Network layout and IPs
- [SSH Access](network/ssh-access.md) — SSH config and host aliases
- [Tailscale](network/tailscale.md) — Tailscale tailnet

### Containers
- [Hermes Coder](containers/hermes-coder.md) — This agent's container (CT 106)
- [Hermes Main](containers/hermes-main.md) — Default profile container

### Projects
- [Voice Pipeline](projects/voice-pipeline.md) — STT → LLM → TTS pipeline
- [CAN Commander](projects/can-commander.md) — CAN bus reverse engineering app
- [Retro Games](projects/retro-games.md) — Android retro games app
- [K3s Cluster](projects/k3s-cluster.md) — Lightweight Kubernetes cluster

### Config
- [Backups](config/backups.md) — Backup strategy and locations
- [SSH Keys](config/ssh-keys.md) — Key management
- [GPU Setup](config/gpu-setup.md) — GPU allocation and config

### Log
- [Changelog](log/changelog.md) — Chronological record of all work done

## About This Knowledge Base

- **Format:** OKF v0.1 — markdown files with YAML frontmatter
- **Location:** `/tank/data/okf/` on PVE, synced to Q1900M at `/srv/backups/okf/`
- **Maintained by:** Larry (Hermes Agent)
- **Last updated:** 2026-06-21
