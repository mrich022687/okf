---
type: Server
title: PVE Proxmox Server
description: Main virtualization host running Proxmox VE 7. Hosts all containers, VMs, and the OKF knowledge base. Clustered with pve2 via richai-cluster.
tags: [proxmox, pve, virtualization, server, cluster, mellanox]
timestamp: 2026-06-23T00:00:00Z
resource: ssh://root@192.168.12.132
---

# PVE Proxmox Server

## Overview
The primary server running Proxmox VE (7.0.6-2-pve) on a HP machine with ZFS tank storage. Forms **richai-cluster** with pve2 (DL360 Gen9).

- **Hostname:** pve
- **Role:** Cluster Node 1 (richai-cluster)
- **IP:** 192.168.12.132
- **40G Link:** 10.10.10.1/30 (Mellanox ConnectX-3 Pro -> pve2)
- **SSH:** `root@192.168.12.132` (via `ssh pve`)

## Power Supply

### Current
- **Type:** 2× HP 500W Platinum (720478-B21)
- **Mode:** Redundant (2+0)
- **Draw (idle):** ~210W total (PS1: 90W, PS2: 120W)
- **Safe limit:** 500W (single PSU in redundancy)

### Planned Upgrade
- **Target:** 2× HPE 1200W Platinum (720480-B21) — ordered 2026-06-23 (~80/pair on eBay)
- **Motivation:** Power 2× Tesla P100 (250W each) + 2× Tesla P4 (75W each) + system (~200W)
- **Capacity after upgrade:** 2400W combined, 1200W redundant — sufficient for all GPUs
- **Status:** Pending delivery and installation

## PCI Devices

## Memory
- **Total:** 128 GB (8× 16GB DDR4 ECC RDIMMs)
- **Channels in use:** 2 of 4 per CPU (suboptimal)
- **Current population per CPU:** DIMM 1, 4, 9, 12
- **Target:** 128 GB once relocated to DIMM 1-8 for 4-channel interleave
- **01:00.1** — Matrox G200EH (onboard VGA)
- **05:00.0** — NVIDIA Tesla P4 (GP104, 8GB)
- **0b:00.0** — NVIDIA Tesla P4 (GP104, 8GB)
- **84:00.0** — NVIDIA Tesla P100 PCIe 16GB (GP100, 16GB)
- **04:00.0** — Mellanox ConnectX-3 Pro (MT27520, 40GbE)

## Smart Array P440ar

- **Controller:** Slot 0 (Embedded)
- **Firmware:** 1.34 (outdated — pve2 runs 6.06)
- **Cache:** 2 GB BBWC (1.8 GB available)
- **Battery/Capacitor:** Replaced 2026-06-23 — now OK
- **Cache Ratio:** 10% Read / 90% Write
- **Mode:** RAID 6
- **Drives:** 4× 1.6T SAS connected
- **Temp:** 43°C (controller) / 33°C (cache)


## Storage
- **sda:** 136.7G — OS (LVM root + swap)
- **sdb/sdc/sdd/sde:** 1.6T each — ZFS pool members (tank)
- **tank capacity:** 2.8T total, 2.7T available

### ZFS Datasets on tank
| Mount | Size | Purpose |
|-------|------|---------|
| `/tank/subvol-106-disk-0` | 20G | CT 106 (Hermes coder) |
| `/tank/subvol-103-disk-0` | 4G | CT 103 (richai-web) |
| `/tank/subvol-104-disk-0` | 8G | CT 104 (k3s-server) |
| `/tank/backups` | — | Backup storage |
| `/tank/vms` | — | VM disk storage |
| `/tank/data` | 2.7T | General data (OKF lives here) |
| `/tank/dump` | — | vzdump backups |

## Containers
| VMID | Name | Status | Spec |
|------|------|--------|------|
| 103 | richai-web | Running | 1C/512MB — Cloudflare tunnel host |
| 104 | k3s-server | Stopped | 2C/8G |
| 106 | **hermes (Larry)** | **Running** | **2C/4G — coder profile** |

## VMs
| VMID | Name | Status | Spec | Notes |
|------|------|--------|------|-------|
| 100 | ubuntu-gnome | Running | 6C/16G | Voice pipeline on P4s — disk fixed 06/26 |
| 101 | windows-11 | Stopped | 6C/16G | License safe on VM 201 (pve2) — backup 06/21 on Q1900 |
| 102 | fedora | Stopped | 4C/4G | Clean disk, needs fresh install |

## 40G Mellanox ConnectX-3 Pro
- **PCI:** 04:00.0 — MT27520 Family
- **Interface:** eno49d1 (altname enp4s0d1)
- **IP:** 10.10.10.1/30, MTU 9000
- **Link partner:** pve2 (10.10.10.2/30)
- **Speed:** 40 Gbps, ~26 Gbps real-world
- **Fix:** port_type_array=2,2 in mlx4_core module

## USB Drives (current)
See [USB Drives](config/usb-drives.md) for current inventory.

## Snapshots
- `tank/subvol-106-disk-0@hermes-golden` -- Golden snapshot of CT 106

## References
- [richai-cluster](hardware/richai-cluster.md)
- [DL360 Gen9 / pve2](hardware/dl360-gen9.md)
- [Network Topology](network/topology.md)


## Firmware Updates

### SPP Update (Executed 2026-06-24)
- **SPP version:** Gen9SPPGen91 (2022_0822.4)
- **Status:** Completed -- firmware updated (BIOS, iLO4, P440ar RAID, NIC)
- **Method used:** iLO web UI virtual media (worked for PVE, but see note below)

### Critical Lesson: iLO Virtual Media Mounting

**The only reliable method** for mounting ISO images via iLO virtual media is the **HPE iLO Application on Windows**.

See the [DL360 Gen9 documentation](dl360-gen9.md) under Firmware Updates for full details, including which methods work and which do not.


---

## Crash Diagnosis — June 2026

### Symptom
pve has been spontaneously rebooting since approximately June 20, 2026. The system hard-resets without warning — no graceful shutdown, no kernel panic visible, just instant reboot.

### Crash History
| Date | Uptime Before Crash | Notes |
|------|-------------------|-------|
| 2026-06-20 | 2min, 7min, 46min, 1h19min | 4 crashes in one day |
| 2026-06-23 23:57 | 1d9h | |
| 2026-06-24 17:29 | 17h | |
| 2026-06-24 17:33 | 1min | Immediate re-crash |
| 2026-06-24 18:40 | 41min | |
| 2026-06-24 20:48 | 1min | Immediate re-crash |
| 2026-06-24 22:38 | 19h | |
| 2026-06-26 17:53 | ~3 days | Longest run |
| 2026-06-29 16:46 | ~3 days | Most recent |

### Root Cause (Confirmed — June 29 Investigation)

**Primary trigger: watchdog-mux.service gets killed (SIGKILL)**
```
Jun 29 16:46:37 pve systemd[1]: watchdog-mux.service: Main process exited, code=killed, status=9/KILL
Jun 29 16:46:38 pve kernel: watchdog: watchdog0: watchdog did not stop!
```
The `watchdog-mux` (Proxmox VE watchdog multiplexer) is the process responsible for regularly "petting" the system watchdog. When it's killed (signal 9/SIGKILL), the watchdog timer expires after 10 seconds and the kernel hard-resets the system.

**Underlying issues found by Linda & Larry (independently confirmed):**

1. ❌ **BIOS firmware bug — PMU corruption**
   - `[Firmware Bug]: the BIOS has corrupted hw-PMU resources`
   - The HP BIOS is corrupting CPU performance monitoring registers
   - This can cause random kernel-level hangs

2. ❌ **NVIDIA GPU driver incompatibility**
   - Driver **580.167.08** (June 2026) flags Tesla P100 GPUs as unsupported
   - `NovaCore: Unsupported chipset (architecture 0x13, implementation 0x0)`
   - Two P100 GPUs at PCI 0b:00.0 and 84:00.0
   - When Ollama tries to use the GPUs, GPU discovery watchdog times out repeatedly
   - Ollama/llama-server GPU discovery watchdog timed out — 20+ occurrences across Jun 27-28
   - Driver 580.167.08 was installed via .run installer on pve3; unlikely to be APT on main PVE
   - **If main PVE was also installed via .run, uninstall with `nvidia-uninstall`**
   - **Target driver:** 550.120 or Proxmox repos default

3. ❌ **K3s/Flannel vs Proxmox firewall iptables contention**
   - `pve-firewall: Another app is currently holding the xtables lock`
   - Flannel (K3s CNI) and pve-firewall fight over iptables
   - Non-fatal but adds system instability

4. ❌ **NFS hard mounts on Q1900 (FIXED)**
   - All three servers (pve, pve2, pve3) had `hard` mounts for q1900 and q1900-isos
   - Hard mounts cause D-state hangs when Q1900 hiccups (low-power Atom, slow NFS)
   - **FIXED:** All changed to `soft,timeo=30,retrans=2` on Jun 29

### What DOESN'T Cause It
- ✅ **Not OOM** — 117GB free RAM during normal operation
- ✅ **Not disk full** — 41% root usage, ZFS pool healthy
- ✅ **Not overheating** — CPU at 41C, NICs at 50C
- ✅ **Not ZFS errors** — 0 read/write/checksum errors on pool
- ✅ **Not SSD failure** — SMART healthy across all 3 pool drives
- ✅ **Not iLO watchdog** — IPMI watchdog is stopped (not configured)

### Fix Plan (assigned to Larry, Jun 29)

> **⚠️ HANDOFF TO LARRY:** Michael has directed that Larry (CT 206 on pve2) handle these fixes because:
> - Larry lives on a separate server (pve2) — if the main PVE crashes during changes, Larry stays running
> - Linda lives on the main PVE (the unstable server) — if she starts this work and the server crashes, she goes dark
> - Larry can SSH in from pve2 and diagnose if the main PVE goes offline

**Larry's assignment (in order):**

1. **Stop the auto-reboot cycle** — The watchdog is configured to use the software watchdog (softdog kernel module). Disable it so the system won't reboot itself when watchdog-mux dies.
   - Create `/etc/modprobe.d/softdog-blacklist.conf`: `blacklist softdog`
   - `update-initramfs -u && reboot` (requires downtime)
   - Alternatively: `echo V > /dev/watchdog` to stop the current watchdog timer (immediate, no reboot needed)
   - Risk: Very low

2. **Roll back NVIDIA driver** — Driver 580.167.08 is too new for P100 GPUs ("Unsupported chipset" error).
   - Check install method first: `dkms status | grep nvidia`
   - If .run installed: `nvidia-uninstall`, then install from Proxmox repos
   - If apt installed: `apt remove --purge nvidia-*`, `apt install pve-headers`, `apt install nvidia-driver-550`
   - Risk: Moderate — GPU-dependent VMs/containers need to be down

3. **Fix iptables contention** — Configure Flannel to use nftables instead of iptables, or disable pve-firewall on the K3s bridge.
   - Options: `kubectl -n kube-flannel edit configmap kube-flannel-cfg` (add `--iptables-mode=nft`)
   - Or change pve-firewall to nftables: `pve-firewall uses nftables by default in Proxmox 8+` (check version)

4. **Update HP BIOS** — Optional, lower priority. The PMU firmware bug isn't directly causing crashes but should be fixed.
   - Current: P89 (check with `dmidecode -s bios-version`)
   - Latest SPP available from HP support

### Affected Services (when pve is down)
- LarryChat backend (port 8900)
- K3s control plane
- GPU Dashboard (port 8080)
- OKF knowledge base (tank mount)
- All VMs/CTs on pve (richai-web, k3s-server)
- ZFS tank storage (NFS exports)
- Proxmox cluster quorum

### NOT Affected (pve can die, these stay up)
- **Hermes/Larry** — Runs on pve2 (CT 206), completely independent
- **pve2** — Separate physical host (DL360 Gen9)
- **pve3** — Separate physical host (8200)
- **Q1900M** — Separate physical host (Linux Mint)
- **Telegram** — Larry's communication channel; uses internet, not internal cluster
