---
type: Index
title: Hermes Knowledge Base
description: Complete documentation of all systems, configurations, projects, and work performed by Hermes AI agents (Larry + Linda) for Michael's homelab and business (Rich AI, LLC).
tags: [index, overview, homelab, rich-ai]
timestamp: 2026-06-23T00:00:00Z
okf_version: "0.1"
---

# Hermes Knowledge Base

This is an [Open Knowledge Format (OKF) v0.1](https://cloud.google.com/blog/products/data-analytics/how-the-open-knowledge-format-can-improve-data-sharing) bundle documenting Michael's homelab infrastructure. Maintained by two Hermes AI agents:

- **Larry** (coder profile, CT 106) — Coding, Android apps, GPU/ML, retro games
- **Linda** (main profile, host) — Infrastructure, backups, networking, server admin

## Quick Links

### Hardware
- [PVE Proxmox Server](hardware/pve-proxmox.md) — Main virtualization host
- [Q1900M](hardware/q1900m.md) — Linux Mint NAS / backup server
- [DL360 Gen9](hardware/dl360-gen9.md) — Secondary Proxmox host (pve2), clustered
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
- [CAN Bus Reverse Engineering](projects/can-bus-reverse-engineering.md) — Emergency vehicle upfitting and signal mapping
- [Voice Pipeline](projects/voice-pipeline.md) — STT → LLM → TTS pipeline
- [CAN Commander](projects/can-commander.md) — CAN bus reverse engineering app
- [Retro Games](projects/retro-games.md) — Android retro games app
- [K3s Cluster](projects/k3s-cluster.md) — Lightweight Kubernetes cluster

### Config
### References
- [Ford Upfitter Guides](references/ford-upfitter-guides.md) — Ford Super Duty wiring diagrams and upfitter documentation

- [Backups](config/backups.md) — Backup strategy and locations
- [SSH Keys](config/ssh-keys.md) — Key management
- [GPU Setup](config/gpu-setup.md) — GPU allocation and config
- [USB Drives](config/usb-drives.md) — USB drive inventory
- [Ventoy Multi-Boot USB](config/ventoy-usb.md) — Multi-boot USB with 14 ISOs

### Log
- [Changelog](log/changelog.md) — Chronological record of all work done

## About This Knowledge Base

- **Format:** OKF v0.1 — markdown files with YAML frontmatter
- **Location:** `/root/okf/` on PVE host, cloned from `git@github.com:mrich022687/okf.git`
- **Maintained by:** Larry (coder) + Linda (main) — Hermes Agents
- **Last updated:** 2026-06-23
