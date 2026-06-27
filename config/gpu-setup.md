---
type: Config
title: GPU Configuration
description: GPU allocation across the homelab. Current layout.
tags: [gpu, nvidia, p4, p100, configuration]
timestamp: 2026-06-27T21:00:00Z
---

# GPU Configuration

## Inventory

| # | Card | Location | Status | Use |
|---|------|----------|--------|-----|
| 1 | Tesla P100-16GB #1 | Main PVE (host) | ✅ | Ollama + vision inference |
| 2 | Tesla P100-16GB #2 | Main PVE (host) | ✅ | CT 103 + CT 104 LXC shared |
| 3 | Tesla P4-8GB #1 | pve2 (DL360) | ✅ | CT 206 (Larry) LXC shared |
| 4 | Tesla P4-8GB #2 | pve2 (DL360) | ✅ | CT 206 (Larry) LXC shared |
| 5 | Tesla P4-8GB #3 | pve3 (Compaq 8200) | ✅ | Standalone Ollama (llava:7b) |
| 6 | Tesla P4-8GB #4 | Uninstalled (spare) | 📦 | Pending destination |

**Total VRAM deployed:** 56 GB (plus 8 GB spare)

## GPU Sharing Model

All GPUs are shared with LXC containers via NVIDIA device cgroup + library bind-mounts — no VM passthrough. This allows flexible workload routing.

## Current Roles

| Server | GPU Config | Role |
|--------|-----------|------|
| **Main PVE** | 2× P100-16GB | Heavy inference, vision, Ollama |
| **pve2** | 2× P4-8GB | STT, embeddings, Larry (coding agent) |
| **pve3** | 1× P4-8GB | Light inference, vision (llava:7b) |
| **Spare** | 1× P4-8GB | Available for future node |

## Ollama Models

Ollama API accessible at `http://localhost:11434` (on each host) or via LAN at `http://192.168.12.132:11434` (main PVE) / `http://192.168.12.163:11434` (pve3).

### Main PVE (2× P100)

| Model | Size | VRAM Needs | Purpose |
|-------|------|-----------|---------|
| `llava:13b` | 8.0 GB | ~10-12 GB | Vision analysis (photos, documents) |
| `deepseek-coder:6.7b` | 3.8 GB | ~4-6 GB | Code generation, autocomplete, Continue.dev |

### pve3 (1× P4)

| Model | Size | VRAM Needs | Purpose |
|-------|------|-----------|---------|
| `llava:7b` | 4.7 GB | ~5.5 GB | Lightweight vision (remote Ollama for Hermes vision) |

## GPU Dashboard

Two options for monitoring all 5 GPUs across the cluster:

**Terminal:** Run `gpu-dashboard.sh` on main PVE. Shows all GPUs with temp/power/utilization/VRAM.

**Web:** `http://192.168.12.132:8080` — systemd service `gpu-dashboard.service`, auto-starts on boot. UFW ports 8080 open for LAN (192.168.12.0/24) and Tailscale (100.64.0.0/10).

## Thermal Performance

| Node | GPU | Idle Temp | Load Temp (sustained) | Cooling Method |
|------|-----|:---------:|:--------------------:|---------------|
| Main PVE | P100-16GB | ~35°C | ~75°C | Server chassis fans (2U rackmount) |
| pve2 | P4-8GB ×2 | ~35°C | ~57°C | DL360 server fans (high CFM) |
| **pve3** | **P4-8GB** | **32°C** | **52°C** | **Blower on CPU fan header (splitter)** |

**⚠️ pve3 critical fix:** The P4 blower fan was originally on the FAN2 header (idle speed, 88°C peak). Moving it to the CPU fan header via a 3-way splitter dropped peak temps by **36°C** to **52°C**. See [compaq-8200.md](../hardware/compaq-8200.md#cooling--p4-blower-fan-fix) for full details.

## Driver Versions

| Node | Driver | Install Method | Notes |
|------|--------|---------------|-------|
| Main PVE | 580.167.08 | apt (Debian non-free) | Works with P100s |
| pve2 | 580.167.08 | apt (Debian non-free) | Works with P4s |
| pve3 | 580.167.08 | NVIDIA .run installer | APT pkgs conflicted on this kernel |

## pve3 Driver Notes

The Debian `nvidia-driver` 550.x was too old to build on Proxmox kernel 7.0.2-6-pve (GCC incompatibilities with removed kernel APIs). CUDA repo had version-mix APT conflicts. Solution: NVIDIA Tesla `.run` installer from `us.download.nvidia.com/tesla/580.167.08/`.
