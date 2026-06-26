---
type: Config
title: GPU Configuration
description: GPU allocation across the homelab. Current and planned layout.
tags: [gpu, nvidia, p4, p100, configuration]
timestamp: 2026-06-26T12:00:00Z
---

# GPU Configuration

## Current Layout

| Card | Location | Status | Use |
|------|----------|--------|-----|
| Tesla P4 #1 (8GB) | Main PVE → VM100 | ✅ Running | STT (GPU 0) |
| Tesla P4 #2 (8GB) | Main PVE → VM100 | ✅ Running | TTS (GPU 1) |
| Tesla P100 (16GB) | Main PVE (host) | ⬜ Unassigned | Available |
| Tesla P4 (8GB) | pve2 (DL360 Gen9) | ✅ Driver installed | Available for workloads |

## Planned Layout (soon)

| Server | GPUs | VRAM | Role |
|--------|------|------|------|
| **Main PVE** | 3× P100 | 48 GB | Heavy inference, training |
| **pve2** | 3× P4 | 24 GB | STT, embeddings, smaller models |
| **pve3** | Possible P4 | 8 GB | Light workloads (if added) |

## P4 → pve2 Migration
- Both P4s from main PVE moving to pve2 tonight (2026-06-26)
- pve2 gets a 3rd P4 to total 3× P4
- Main PVE gets all P100s instead

## Storage Names Renamed (2026-06-26)
- `ilo-backups` → `q1900`
- `ilo-isos` → `q1900-isos`
- All references cleaned across fstab, cron, and all 3 cluster nodes
