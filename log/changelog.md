---
type: Log
title: Changelog
description: Running log of significant work on the OKF project.
tags: [changelog, log]
timestamp: 2026-06-26T20:30:00Z
---

# Changelog

## 2026-06-30 — Game Streaming Setup — pve3 Sunshine/Moonlight + PS2 Ripping

- **PS2 Game Library:** Ripped 7 PS2 discs to pve3 `/games/PS2/` — Top Gun Combat Zones (579 MB), Gran Turismo 3 (3.7 GB), Gran Turismo 4 (5.0 GB), Sonic Mega Collection Plus (4.4 GB), Pirates of the Caribbean (1.3 GB), Avatar: The Last Airbender (1.6 GB), Crash of the Titans (3.1 GB). Total ~20 GB.
- **pve3 storage:** Created 80 GB ext4 logical volume at `/games` from thin pool (55 GB free)
- **Sunshine installed:** Downloaded + installed LizardByte Sunshine `.deb` on pve3 (Debian Trixie). Service at `/etc/systemd/system/sunshine.service`. Web UI at https://192.168.12.163:47990
- **Emulators:** Installed PCSX2 v2.6.3 + Dolphin Emulator via Flatpak (Flathub)
- **Headless GPU setup:** Xvfb virtual display :99 at 1920x1080. KMS capture requires `nvidia-drm.modeset=1` (persisted at `/etc/modprobe.d/nvidia-drm-modeset.conf`). Xorg headless fallback config at `/etc/X11/xorg.conf.sunshine`
- **UFW:** Opened TCP 47984/47989/47990/48010 + UDP 47998-48010 for Sunshine (LAN-only)
- **RG505:** Moonlight `com.limelight.root` installed via ADB. RTSP handshake working. Video stream being debugged — KMS virtual connector frame capture issue
- **Pitfalls:** TCP 48010 initially missing from UFW. Tesla P4 AV1 not supported. Xvfb alone can't provide Vulkan surfaces. Prebuilt Sunshine lacks NvFBC
- **OKF:** Created `projects/game-streaming.md` (full setup doc), updated `hardware/compaq-8200.md` (gaming services section), updated `hardware/rg505.md` (Moonlight), updated changelog.md

## 2026-06-28 — VM 201 Windows 11 — Complete Debloat & Recovery Documentation

- **VM 201 fully documented:** Created `/tank/data/okf/containers/vm201-windows11.md` with complete issue log (10 documented issues with root causes + fixes), debloat checklist, SSH config, performance tweaks, and VM config reference
- **Xbox obliterated:** Services `XblAuthManager`, `XblGameSave`, `XboxNetApiSvc`, `XboxGipSvc` all disabled. App packages removed. Provisioning packages removed to block reinstall
- **Network recovery:** QEMU `device_del` destroyed the NIC — recreated with new MAC `AE:61:5A:19:EB:75`, VirtIO drivers installed manually, DHCP lease stabilized at 192.168.12.234
- **OpenSSH saga:** DISM hung (RAM starvation). MSI install worked but service vanished after reboot. Final fix: DISM reinstall + manual `sc.exe create sshd` + `ssh-keygen -A` + firewall rule
- **RAM tuning:** Started at 16 GB (pegged at 100%), temporarily bumped to 32 GB for DISM ops, settled at 8 GB balloon (idle ~3 GB)
- **Debloat round 2:** Windows Search, SysMain, Game Mode, visual effects, Cortana, telemetry, advertising ID all disabled. Performance power plan set
- **SSH access:** Running on port 22 with password `Win11pve2`. QEMU guest agent for emergency access
- **Backup:** VM 201 added to nightly vzdump cron (snapshot mode, zstd, keep-last=3, to Q1900)
- **Index updated:** New VM 201 section added to OKF index.md. Backups.md updated with VM 201 info

## 2026-06-27 — pve3 P4 Fan Fix — 36°C Temp Drop

- **pve3 cooling crisis discovered:** P4 blower fan on FAN2 header was running at idle speed while CPU fan header ran at 100%. P4 hit 88°C under load — only 3°C from throttle.
- **Fix:** 3-way fan splitter connecting both CPU fan + P4 blower to the CPU fan header
- **Results:** Peak temp dropped from **88°C → 52°C** (-36°C), idle from **36°C → 32°C**, throttle margin from 3°C → 39°C
- **Lesson:** Auxiliary fan headers on older SFF boards may default to low speed — always verify with load testing
- **OKF:** Updated compaq-8200.md (full cooling section), gpu-setup.md (thermal performance table + critical fix note), changelog.md

## 2026-06-28 — pve3 Security, NIC Fix, K3s Cleanup

- **pve3 NIC:** PHY initialization glitch → corosync drops. Reset NIC, link stable since.
- **pve3 UFW:** Installed + configured — SSH, Proxmox, Ollama, SPICE locked to LAN + Tailscale only. Fail2ban installed (5 attempts → 1h ban).
- **K3s zombie pods:** Cleaned up 24 dead pods (20 metrics-scraper + 4 dashboard, ContainerStatusUnknown). Deployments scaled to 1 replica each.
- **K3s** runs on PVE host (not CT 104).

## 2026-06-27 — Ollama Models, GPU Dashboard, Amanda's HP Tablet

- **Ollama (Main PVE):** deepseek-coder:6.7b pulled for code generation (3.8 GB)
- **GPU Dashboard:** Web dashboard at http://192.168.12.132:8080 — systemd service gpu-dashboard.service showing all 5 GPUs. Terminal dashboard: gpu-dashboard.sh
- **UFW:** Port 8080 opened for LAN (192.168.12.0/24) and Tailscale (100.64.0.0/10)
- **Amanda's HP Pavilion x2:** Discovered at 192.168.12.232, running Linux Lite 7.0 (freezing). SSH access established (manderz user). Needs Lubuntu reinstall on internal storage (was accidentally installed on SD card)
- **OKF:** GPU config updated with Ollama models and dashboard info. New hardware entry created for HP Pavilion x2.

## 2026-06-26 — pve3 GPU, Root Disk Fix, ZFS Cleanup, VM 101 Destroyed

- **Root disk:** Extended from 56 GB → 128 GB (75% → 34% used), 72 GB of LVM space reclaimed
- **ZFS pool tank:** Cleared 1,163 permanent errors — all 3 drives ONLINE, scrub running
- **VM 101 (Windows 11):** Destroyed + cleaned up due to ZFS corruption. License preserved on VM 201 (pve2) — same SMBIOS UUID `74a66e32-d27c-4a5d-af1b-63d0fac6508d`
- **iLO:** Diagnosed embedded NAND flash failure — "write-verify test failure" + "update nand failed". Fix: insert microSD card in internal slot
- **pve3 (Compaq 8200):** Tesla P4-8GB fully operational — driver 580.167.08, CUDA 13.0
- **pve3 GPU install:** BIOS integrated graphics enabled (DisplayPort output), GT 635 removed, NVIDIA .run installer used (APT repos failed due to kernel/driver version conflicts)
- **pve3 persistence:** Systemd service created, auto-load configured, nouveau blacklisted
- **Cluster:** pve3 now at 3 nodes fully quorate with P4 compute capacity
- **OKF:** GPU config, compaq-8200, cluster, and changelog all updated

## 2026-06-26 — GPU Install, Cluster Update, Storage Rename

- **Hardware:** 2× Tesla P4 installed in PVE2 (DL360 Gen9) — stress tested, 0 errors, max 65°C
- **GPU:** CT 106 (Hermes agent) migrated to PVE2 — has full GPU access to both P4s
- **Cluster:** Updated to 3-node config documentation (pve + pve2 + pve3)
- **Storage:** Linda renamed all `ilo-backups`/`ilo-isos` → `q1900-backups`/`q1900-isos` cluster-wide
- **Network:** TP-Link TL-SG108 switch verified — sub-1ms latency, 0% loss
- **PVE:** Boot disk nearly full (56/56GB) — needs cleanup

## 2026-06-25 — PVE3 Cluster Join, Switch Install

- PVE3 (Compaq 8200) joined richai-cluster as 3rd node
- TP-Link TL-SG108 switch installed replacing old router
- All server NICs bridged to vmbr0 with STP

[Earlier entries truncated for brevity]

## 2026-06-28 — K3s Dashboard Cleanup (Larry)

- **Zombie pods cleaned:** Scaled  and  to 1 replica. Deleted 24 dead pods (20 metrics-scraper + 4 dashboard, all ). Both deployments now 1/1 Running.
- **Status:** Resolved. Linda's 🔴 note actioned.

## 2026-06-28 -- K3s Dashboard Cleanup (Larry)

- Zombie pods cleaned: Scaled kubernetes-dashboard and dashboard-metrics-scraper to 1 replica.
  Deleted 24 dead pods (20 metrics-scraper + 4 dashboard, all ContainerStatusUnknown).
  Both deployments now 1/1 Running.
- Status: Resolved. Lindas red note actioned.

## 2026-06-29 — PVE Crash Diagnosis, Android Library, Backup Monitor, Infrastructure Fixes

- **PVE Crash:** Main server (pve) crashed at 16:46 MDT — hardware watchdog triggered hard reset
- **Diagnosis:** watchdog-mux.service killed (SIGKILL) then software watchdog timeout then reset
- **Root cause identified:** BIOS firmware bug (corrupted PMU registers) + NVIDIA driver 580 incompatible with P100s
- **Linda and Larry** independently confirmed same diagnosis — fix plan in pve-proxmox.md
- **Hermes re-located:** Documentation corrected — Larry is CT 206 on pve2, NOT CT 106 on pve (migrated previously, docs were stale)
- **Independence confirmed:** Hermes stays up when pve goes down — separate physical host (pve2/DL360 Gen9)
- **Business dossier:** Complete RichAI Business Dossier created — infrastructure map, 6-division structure, success stories, funding research, file system organization
- **Research complete:** 4 parallel research agents finished — company structures, success stories, funding options, file organization
- **K3s stale nodes removed:** Cleaned 4 NotReady nodes (compaq-8200, inspiron-14, z390, ubuntu-gnome) from cluster — stopped etcd reconciliation churn
- **NFS mounts fixed:** All 3 servers now use `soft` mounts for Q1900 (was `hard` — causes D-state hangs)
- **Android Tools Library:** Created on Q1900 at `/srv/backups/Android tools and software/` — GammaOS (6.5 GB), ADB tools, Unisoc drivers
- **GammaOS firmware:** Downloaded, extracted, staged to both Q1900 (permanent) and laptop (working directory C:\Users\mrich\RG505_GammaOS\)
- **RG505 ROM cleanup:** Deleted 50 duplicate PSP games (44 GB freed), flattened SNES/GB subdirectories, organized hacks folder
- **Q1900 credentials saved** to vault under `Q1900-NAS-iLO`
- **Backup monitor setup:** Twice-daily script (6 AM / 6 PM) checks all backups are current, reports to Telegram
- **RAM upgrade:** 2× 16 GB Micron DDR4 arrived for pve2 — waiting for other pair to install all 4 at once
- **DIMM audits:** Verified main PVE slots are correct per HP spec; pve2 slots confirmed against lid label
- **Q1900 headless conversion planned:** Strip Linux Mint GUI to save ~3 GB RAM
- **OKF docs updated:** pve-proxmox.md (crash analysis + handoff), rg505.md (new), android-tools.md (new), q1900m.md (headless plan), dl360-gen9.md (RAM plan), backups.md (monitor + NFS fix), index.md (new pages), changelog (this entry)

## 2026-06-29 — LarryChat v3 Rebuild & Agent Pipeline

### LarryChat
- **Server v3:** Complete backend rewrite — 340-line FastAPI server (`server_v3.py`) replacing Flask v2
- **New endpoints:** `/api/agent/send`, `/api/agent/pending`, `/api/voice/send`, `/api/voice/{file}`
- **WebSocket:** Proper per-conversation broadcast — messages pushed to ALL connected clients in real-time
- **Agent API:** Separate auth (agent keys vs session tokens), dedicated send endpoint
- **Voice:** Record-then-send Opus messages (streaming deferred until VM100 is up)
- **Read receipts:** Messages marked read on fetch, double-checkmark UI
- **Database:** New `chat_v3.db` with WAL mode, conversations table with participants
- **Android app:** Complete Kotlin/Compose rebuild — 16MB APK deployed to Moto Root
  - Material 3 design, conversation list with badges, real-time WebSocket
  - Voice recording (MediaRecorder → Opus), settings toggle
- **Agent cronjob:** Hermes-managed `c036be54be4b` — polls every 60s, auto-responds
  - **Bug fixed:** Prompt was re-running fetch script (consuming messages silently). Now uses injected script output.

### Hermes Configuration
- **Model:** `deepseek-v4-pro` for coding session; switched to `deepseek-v4-flash` after to conserve tokens
- **Cronjob cleanup:** Removed stale pollers (84c96118fced, c66e184d8061); paused agent responder (c036be54be4b)
- **Shutdown:** LarryChat server stopped + disabled on pve; agent cronjob paused to conserve resources

### Knowledge Base
- Created `projects/larrychat.md` — full architecture, API docs, shutdown procedure
- Created `index.md` — master KB index with all links
- Updated `log/changelog.md` — this entry
