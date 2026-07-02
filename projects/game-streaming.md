---
type: Project
title: Game Streaming & Emulation
description: PS2/retro game streaming from pve3 Windows 11 VM with Tesla P4 NVENC to RG505 via Moonlight.
tags: [gaming, sunshine, moonlight, ps2, emulation, pve3, rg505, tesla-p4]
timestamp: 2026-07-02T01:00:00Z
---

# Game Streaming & Emulation

## Overview

Game streaming from **pve3** (192.168.12.163) running a **Windows 11 VM (301)** with Tesla P4 GPU passthrough, using Sunshine + NVENC encoding, to Moonlight on the Anbernic RG505. Linux native streaming abandoned — Sandy Bridge iGPU too old.

## Architecture

```
┌─────────┐   Tailscale    ┌──────────────┐
│  RG505  │ ◄───────────── │  pve3 VM 301 │
│Moonlight│                │  Windows 11  │
│         │                │  P4 NVENC    │
└─────────┘                │  Sunshine    │
                           └──────────────┘
```

## VM 301 — Windows 11 Gaming (pve3)

**Windows License:** SMBIOS UUID `74a66e32-d27c-4a5d-af1b-63d0fac6508d` — preserved from VM 201 (originally from VM 101). Product key may also be embedded in registry — extract once VM boots.

**Config (must match VM 201 source exactly):**
- bios: seabios
- machine: pc-q35-11.0
- cpu: host, 6 cores, 8 GB RAM
- disk: sata0 on local-lvm, 100 GB thin
- net0: e1000, bridge vmbr0
- scsihw: virtio-scsi-single
- hostpci0: 01:00.0,pcie=1 (Tesla P4)
- ostype: win11

**Source:** VM 201 on pve2 (192.168.12.50)
- Disk: `/mnt/pve2-extra/images/201/vm-201-disk-0.qcow2` (107 GB, sparse)
- This is the ONLY valid source. DO NOT use `vm-201-disk-3.raw` — it's empty/unused.

**Migration procedure (after pve2 RAM upgrade):**
1. `scp root@192.168.12.50:/mnt/pve2-extra/images/201/vm-201-disk-0.qcow2 /tank/vm201.qcow2`
2. `qemu-img convert -O raw /tank/vm201.qcow2 /tank/vm201.raw`
3. `dd if=/tank/vm201.raw bs=4M | ssh root@192.168.12.163 dd of=/dev/pve/vm-301-disk-0 bs=4M`
4. `qm start 301`

**Post-boot setup (still needed):**
- Install NVIDIA driver (P4 detected at first boot)
- Install IddSampleDriver (virtual display — P4 has no physical outputs)
- Install Sunshine for Windows
- Configure NVENC capture
- Pair with Moonlight on RG505

**DO NOT:**
- Change disk controller type (stay SATA)
- Change NIC type (stay e1000)
- Change BIOS (stay seabios)
- Add EFI disk (not present on source)

## RG505 (Anbernic)

- GammaOS (Android 14), rooted
- Tailscale: 100.94.30.106
- Moonlight root v12.1 installed
- Daijisho frontend — ROM paths already pointed at SD card
- 27 RetroArch playlists generated (8,467 games)
- ROMs on SD card: `/storage/00000000-0000-0000-0000-000000000001/ROMS/`
- ADB: wireless, ports rotate on each restart

## pve3 Hardware Notes

- P4 at PCI 01:00.0, bound to vfio-pci
- IOMMU enabled (intel_iommu=on iommu=pt)
- LVM thin pool: pve/data (100.5 GB after extension)
- FAN2 header underpowered — P4 uses CPU_FAN splitter
- BIOS: press F1 to bypass "front chassis fan not detected"
- 32 GB DDR3, Sandy Bridge CPU
- 1 GbE network

## Games

- PS2 ISOs: 7 titles on pve3 at `/games/PS2/`, also 10 on RG505 SD card
- ROM library: 8,467 games across 27 systems on RG505 SD card
