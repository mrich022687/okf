---
type: Config
title: GPU Configuration
description: GPU allocation and setup across the homelab. Includes P4 and P100 cards.
tags: [gpu, nvidia, p4, p100, configuration]
timestamp: 2026-06-21T18:15:00Z
---

# GPU Configuration

## Current Inventory

| Card | Location | Status | Use |
|------|----------|--------|-----|
| Tesla P4 (8GB) | VM100 (Ubuntu) | ✅ Running | STT (GPU 0), TTS (GPU 1) |
| Tesla P100 (16GB) | Z390 | ⚠️ Unreachable | Primary ML card |

## VM100 GPU Setup
- **2× Tesla P4** via PCIe passthrough
- GPU 0: faster-whisper large-v3 (STT)
- GPU 1: XTTS v2 (TTS)

## Planned Expansion
- 2 more P4s and 2 more P100s arriving (PCIe risers expected late June)
- Goal: Consolidate all 4 GPUs in PVE via PCIe risers
- Z390 P100 hits 83°C throttle at ~130W passive — needs active cooling solution

## Notes
- P4 cards are passive — need server airflow
- P100 has active cooler but runs hot in tight spaces
