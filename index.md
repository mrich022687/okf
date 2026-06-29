---
type: Index
title: Hermes Knowledge Base
description: Complete documentation of all systems, configurations, projects, and work performed by Hermes AI agents (Larry + Linda) for Michael's homelab and business (Rich AI, LLC).
tags: [index, overview, homelab, rich-ai]
timestamp: 2026-06-29T17:15:00Z
okf_version: "0.1"
---

# Hermes Knowledge Base

This is an [Open Knowledge Format (OKF) v0.1](https://cloud.google.com/blog/products/data-analytics/how-the-open-knowledge-format-can-improve-data-sharing) bundle documenting Michael's homelab infrastructure. Maintained by two Hermes AI agents:

- **Larry** (coder profile, CT 206) — Coding, Android apps, GPU/ML, retro games
- **Linda** (main profile, host) — Infrastructure, backups, networking, server admin

## Quick Links

### Hardware
- [PVE Proxmox Server](hardware/pve-proxmox.md) — Main virtualization host (DL380 Gen9 — crash-prone, fix assigned to Larry)
- [Q1900M](hardware/q1900m.md) — Linux Mint NAS / backup server (planned headless)
- [DL360 Gen9](hardware/dl360-gen9.md) — Secondary Proxmox host (pve2), clustered
- [Anbernic RG505](hardware/rg505.md) — Retro gaming handheld (GammaOS pending)
- [Z390](hardware/z390.md) — Main desktop (i9-9900K + P100)
- [Compaq 8200 / pve3](hardware/compaq-8200.md) — Proxmox cluster node 3
- [HP Pavilion x2](hardware/hp-pavilion-x2.md) — Amanda's tablet (Lubuntu)
- [Q1900](hardware/q1900m.md) — Linux Mint / backup server / ISOs
- [Moto Root](hardware/moto-root.md) — Rooted Android phone
- [Moto G Power](hardware/moto-g-power.md) — Secondary Android phone

### Network
- [Topology](network/topology.md) — Network layout and IPs
- [SSH Access](network/ssh-access.md) — SSH config and host aliases
- [Tailscale](network/tailscale.md) — Tailscale tailnet

### VMs
- [Windows 11 (VM 201)](containers/vm201-windows11.md) — Win11 Pro on pve2 — complete debloat, OpenSSH, optimization

### Containers
- [Hermes Coder](containers/hermes-coder.md) — This agent's container (CT 206)
- [Hermes Main](containers/hermes-main.md) — Default profile container

### Projects
- [CAN Bus Reverse Engineering](projects/can-bus-reverse-engineering.md) — Emergency vehicle upfitting and signal mapping
- [Voice Pipeline](projects/voice-pipeline.md) — STT → LLM → TTS pipeline
- [CAN Commander](projects/can-commander.md) — CAN bus reverse engineering app
- [Retro Games](projects/retro-games.md) — Android retro games app
- [K3s Cluster](projects/k3s-cluster.md) — Lightweight Kubernetes cluster

### References
- [Ford Upfitter Guides](references/ford-upfitter-guides.md) — Ford Super Duty wiring diagrams and upfitter documentation
- [CAN Bus Reverse Engineering](references/can-bus-reverse-engineering.md) — Complete CAN bus workflow: OBDLink MX+, raw CAN streaming, TCP bridge, vehicle architectures (Ford/RAM HD), AT command reference, CAN Commander app

### Config
- [Backups](config/backups.md) — Backup strategy, schedule, monitor, NFS mount notes
- [SSH Keys](config/ssh-keys.md) — Key management
- [GPU Setup](config/gpu-setup.md) — GPU allocation and config
- [USB Drives](config/usb-drives.md) — USB drive inventory
- [Ventoy Multi-Boot USB](config/ventoy-usb.md) — Multi-boot USB with 14 ISOs
- [Android Tools Library](config/android-tools.md) — Android dev tools on Q1900 (GammaOS, ADB, drivers)

### Log
- [Changelog](log/changelog.md) — Chronological record of all work done

## About This Knowledge Base

- **Format:** OKF v0.1 — markdown files with YAML frontmatter
- **Location:** `/root/okf/` on PVE host, cloned from `git@github.com:mrich022687/okf.git`
- **Maintained by:** Larry (coder) + Linda (main) — Hermes Agents
- **Last updated:** 2026-06-23
