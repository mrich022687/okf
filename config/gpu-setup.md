---
type: Config
title: GPU Configuration
description: GPU allocation and setup across the homelab. Three NVIDIA GPUs in main PVE server, one in pve2.
tags: [gpu, nvidia, p4, p100, configuration]
timestamp: 2026-06-23T04:05:00Z
---

# GPU Configuration

## Current Inventory

| Card | Location | Status | Use |
|------|----------|--------|-----|
| Tesla P4 #1 (8GB) | Main PVE → VM100 (Ubuntu) | ✅ Running | STT (faster-whisper, GPU 0) |
| Tesla P4 #2 (8GB) | Main PVE → VM100 (Ubuntu) | ✅ Running | TTS (XTTS v2, GPU 1) |
| Tesla P100 (16GB) | Main PVE (host) | ⬜ Unassigned | Available for assignment |
|| Tesla P4 (8GB) | pve2 (DL360 Gen9) | ✅ Driver installed | Available for workloads |

## Main PVE Server (192.168.12.132)

### VM100 GPU Setup
- **2× Tesla P4** via PCIe passthrough (05:00.0 + 0b:00.0)
- GPU 0: faster-whisper large-v3 (STT)
- GPU 1: XTTS v2 (TTS)
- VM currently **stopped**

### Unassigned GPU
- **Tesla P100 PCIe 16GB** (84:00.0) — sitting on host, not passed to any VM yet
- Can be assigned to a VM for ML workloads or AI inference

## pve2 (DL360 Gen9, 10.10.10.2)

- **1× Tesla P4 (8GB)** — PCIe slot 08:00.0
- **Driver:** NVIDIA 580.167.08 (Tesla Data Center), CUDA 13.0
- **Status:** ✅ Working — 50°C, 23W idle
- Connected to main PVE via 40G Mellanox link

## Notes
- P4 cards are passive — need server airflow
- P100 has active cooler
- Z390 Aorus Elite desktop is DEAD — no GPUs there
