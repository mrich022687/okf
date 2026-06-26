---
type: Changelog
title: Changelog
description: Chronological record of all work performed by Larry for Michael's homelab.
tags: [log, history, changelog]
timestamp: 2026-06-21T18:15:00Z
---

# Changelog

## 2026-06-21

### OKF Knowledge Base Created
- Created Open Knowledge Format bundle at `/tank/data/okf/` on PVE
- Documented all hardware, network, containers, projects, and config
- Synced to Q1900M at `/srv/backups/okf/`
- Set up for GitHub publishing

### Samba Name Change — Q1900M
- Changed NetBIOS name from `iLO` to `q1900`
- Changed server string from `iLO-NAS` to `q1900-NAS`
- Restarted smbd service

### Ford Upfitter Guide — File Cleanup
- Deleted duplicate: `2026-Ford-Super-Duty-Incomplete-Vehicle-Manual.pdf` (19MB)
- Kept: `2026-Ford-Upfitters-Guide.pdf`

### Container Backup
- Created vzdump of CT 106 (hermes): 999MB at `/tank/dump/dump/`
- Copied to Q1900M at `/home/michael/hermes-ct106-backup.tar.zst`

### Retro Games v1.3 — Tanks Per-Player Settings
- Each player in Tanks now has their own saved angle and power
  - P1: `p1Angle` (default 45°), `p1Power` (default 50)
  - P2: `p2Angle` (default 45°), `p2Power` (default 50)
- Current player's settings persist between turns
- Other player never sees the angle/power used against them
- Built and deployed to Moto Root

### USB Hub Power Cycle
- All USB drives on PVE hub re-enumerated after power cycle
- 5 WesData 64GB drives identified (flashed with various ISOs)
- Drive inventory updated

### Skills System
- Multiple skills created/updated for common workflows
- Skills stored at `/root/.hermes/profiles/coder/skills/`

## Earlier Sessions (Summary)

### Voice Pipeline Setup
- STT/TTS/LLM pipeline on VM100 with 2× Tesla P4
- PyTorch 2.6 XTTS fix applied (weights_only=False)
- Systemd services for LLM server and voice server

### CAN Commander Development
- Android APK for CAN bus reverse engineering
- Multiple bug fixes during live vehicle testing:
  - START button deadlock fix (ConnState enum)
  - ATMA non-blocking read loop with NO DATA re-entry
  - Real bonded device list (no placeholders)
  - Abstract CanController interface pattern
  - SCP pipe fallback for mobile network transfers

### SSH Access Setup
- Key-based SSH to all 8 devices
- SSH config in `/root/.ssh/config`

## 2026-06-21 (Linda)

### OKF Knowledge Base — Shared Repo
- Cloned shared `okf` repo from `git@github.com:mrich022687/okf.git`
- Updated index.md to be a shared KB (Larry + Linda)
- Added Ventoy multi-boot USB concept doc
- Added DL360 Gen9 hardware doc (pending setup)
- Configured git for Hermes main agent

### NFS Share — Fixed Stale Mount
- Q1900 went offline (hard reset), NFS mounts on PVE hung
- iLO (192.168.12.126) was reachable but main OS was down
- After reboot, NFS required lazy unmount and remount

### Ventoy Multi-Boot USB Created
- Downloaded Ventoy v1.1.05 to PVE host
- Installed on WesData drive (sdr, 59G)
- Copied 14 ISOs from Q1900 `/srv/isos/`:
  - Proxmox VE 8, Windows 11, Ubuntu 26.04 Desktop + Server
  - Kali Linux, Linux Lite, Lubuntu, Debian XFCE + Gnome
  - Fedora KDE + Workstation, Linux Mint, OPNsense, virtio-win
- 56G used / 3.5G free

### Lubuntu ISO — Transfer and Backup
- Lubuntu 26.04 ISO was on Windows laptop desktop
- User transferred via USB to Q1900
- Copied to `/srv/isos/` on Q1900 for backup
- Also added to Ventoy USB

### Q1900 Naming Updated
- References changed from "Q1900M iLO box" to "q1900" / "q1900m"
- Memory and docs updated accordingly

## 2026-06-22

### DL360 Gen9 — Initial Setup
- Server racked, powered on, connected to network
- HP Smart Array P440ar — RAID logical drive created (64K strip, read-ahead, write-back with BBU)
- Hostname set to `pve2.lan`, IP 192.168.12.50 planned
- Proxmox VE 8 install pending (Ventoy USB)

### 40G Networking — Mellanox ConnectX-3 Pro
- Installed in main PVE server, detected at PCI 04:00.0 as `eno49d1`
- DL360 will get matching card for inter-server 40G link

### LXC 103 (richai-web) — Permanent DNS Fix
- DNS kept reverting to Tailscale MagicDNS (100.100.100.100), breaking cloudflared tunnel
- Set `pct set 103 -nameserver "1.1.1.1 8.8.8.8"` — survives reboots now
- Website (rich-ai.net) restored

### DL360 Gen9 — iLO 4 Configured
- iLO dedicated port mode set to "On" (dedicated RJ-45 port)
- iLO connected to ASUS router/switch
- DHCP enabled — IP assigned: 192.168.12.100
- DNS name set to `pve2-ilo`
- User account `Michael` created with full admin privileges (password: mcrart8794!)
- Default iLO account: `ILOMXQ80304TS` (sticker password)
- iLO accessible via web GUI at https://192.168.12.100 and SSH on port 22
- DL360 hardware doc updated in OKF

### DL360 Gen9 — Proxmox VE 8 Installed & Running
- Proxmox VE 8 reinstalled on pve2 — IP 192.168.12.50/24, gateway 192.168.12.1, DNS 1.1.1.1
- Enterprise repo disabled, no-subscription repo added
- Passwordless SSH access from main PVE host configured
- Root password: mcrart8794!
- `apt dist-upgrade` still running
- OKF hardware doc fully updated

### 40G Mellanox — ✅ FIXED! 40 Gbps Link Working
- **Root cause:** Cable was plugged into Port 1 which defaults to InfiniBand mode
- **Fix:** `mlx4_core` module parameter `port_type_array=2,2` forces both ports to Ethernet mode
- Module had to be reloaded with the parameter — modprobe.d alone wasn't enough until reload
- Link details:
  - Main PVE: **eno49d1** (Port 2) @ 10.10.10.1/30, MTU 9000
  - PVE2: **enp4s0** (Port 1) @ 10.10.10.2/30, MTU 9000
  - Speed: **40 Gbps** (40000baseCR4/Full), latency ~0.2ms
  - Direct cable between servers, no switch
- Config made permanent in `/etc/network/interfaces` on both sides

### CT 106 (Hermes/Larry) — Tailscale TUN Fix
- Tailscale stopped working: `/dev/net/tun` missing in LXC container
- Fixed: added `lxc.cgroup2.devices.allow: c 10:200 rwm` and TUN mount to config
- Container restarted, TUN device now present
- Tailscale re-auth needed: https://login.tailscale.com/a/69e24140146df

### Network & Infrastructure
- Tailscale tailnet management
- Proxmox VM/container provisioning
- K3s cluster setup
- NFS/Samba share configuration
## 2026-06-23

### Knowledge Base — Cleanup & Sync
- Fixed broken markdown in index.md (leading pipes from Linda's edit)
- Updated network topology: added pve2, iLO 4, 40G interconnect details
- Updated PVE hardware doc: added 40G Mellanox card, PCI device list, cluster role
- Created missing files: config/ssh-keys.md, projects/k3s-cluster.md
- Identified cluster issue: pve2's vmbr0 (192.168.12.50) is DOWN
  - Cluster shows 1/2 votes — not quorate until pve2's management NIC is connected
  - pve2 only reachable via 40G link at 10.10.10.2

### pve2 Hardware Captured
- 2x Xeon E5-2690 v4 (28C/56T @ 2.6GHz)
- 62 GB RAM
- 1x Tesla P4 GPU (GP104, 8GB)
- 1x Mellanox ConnectX-3 Pro (40GbE)

### GPU Inventory Updated
- Confirmed full GPU layout via PCIe scan:
  - **Main PVE:** 2× Tesla P4 (passed through to VM100) + 1× Tesla P100 (unassigned on host)
  - **pve2:** 1× Tesla P4 (PCIe slot 08:00.0)
  - **Z390 desktop:** 💀 Dead — P100 removed and reinstalled in main PVE
- Updated `config/gpu-setup.md`, `hardware/dl360-gen9.md`, `hardware/z390.md`
|- Corrected persistent memory: Z390 dead, P100 back in main PVE

### 🔒 pve2 Security Hardening
|- SSH hardened: key-only auth, root login via key only, password auth disabled
|- fail2ban installed and running — bans after 5 failed attempts in 30m
|- UFW firewall active — default deny incoming
|  - SSH (22): LAN + 40G link only
|  - Proxmox GUI (8006): LAN only
|  - Cluster/SPICE (111, 3128, 60000-60050): 40G link only
|  - 40G interface (enp4s0): fully open (inter-server traffic)
|- iperf3 test port (5201) closed
|- DNS set to 1.1.1.1 / 8.8.8.8 (persistent in /etc/network/interfaces)
|- NAT added on main PVE so pve2 can reach internet via 40G link

### 🌐 pve2 Networking Fixed (nic2 → nic3)
|- Ethernet cable was plugged into **nic3** but vmbr0 was bridged to **nic2**
|- Changed bridge port from nic2 to nic3 — link now up at 1 Gbps
|- pve2 now reachable at 192.168.12.50 over LAN ✅
|- Default route switched from WiFi to ethernet

### 🎮 Tesla P4 Driver Installed on pve2
|- Driver: **NVIDIA 580.167.08** (Tesla Data Center), downloaded 379 MB
|- CUDA 13.0 — nvidia-smi verified ✅
|- Temp: 50°C, Power: 23W/75W idle
|- Fixed: had to unload nouveau driver first (was holding the GPU)

### 🎮 P100 Driver Installed on Main PVE Host
|- Tesla P100-PCIE-16GB (84:00.0) — driver **580.167.08** installed on host
|- 42°C, 30W/250W idle, 16 GB VRAM ✅
|- Both P4s (VM 100) already had 580.159.03 driver — working
|- **All 4 GPUs across homelab now have drivers installed**

## 2026-06-22

### Cluster Fixed — 40G Link Now Active for Corosync
- Changed corosync ring0_addr on both nodes from LAN IPs (192.168.12.x) to 40G link IPs (10.10.10.x)
- Corosync now communicates over the direct 40G Mellanox cable
- **Cluster quorate** — 2/2 votes, both nodes online
- No LAN cable needed for cluster operation

### pve2 Internet — USB WiFi (AR9271)
- Atheros AR9271 USB WiFi adapter (wlx24ec99a73b1d) connected to PeterPanWithMeTwin2
- IP: 192.168.12.195/24 via DHCP, signal -22 dBm, 72 Mbps
- WiFi config added to /etc/network/interfaces (wpa-ssid/wpa-psk)
- Internet working: ping 1.1.1.1 at 38ms
- Realtek RTL8188GU (fast adapter): CD-ROM mode switched successfully (0bda:1a2b → 0bda:c811) but driver (rtw88_8821cu/rtl8xxxu) fails to claim it on PVE kernel 7.0.2 — would need newer kernel

### 2026-06-23 — Ford Upfitter Guides OKF Documentation
- Added  — complete reference doc for all 3 Ford upfitter PDFs
- Copied PDFs to  (PDFs from Q1900M)
- Documented which PDF is the actual wiring guide (2025 BBLB) vs compliance docs (2026/2027 IVMs)
- Included taillamp pinouts (C423 LH, C422 RH), turn signal circuits (CLS23, CLS27), front turn signals (CLS21, CLS25)
- End-of-frame blunt cut wire colors for stop/turn/park/backup circuits
- FDRS reconfiguration instructions for separate stop/turn or LED lamps
- Updated index.md with References section linking to upfitter guides
2026-06-23 — Ford Upfitter Guides OKF Documentation
- Added references/ford-upfitter-guides.md — complete reference doc for all 3 Ford upfitter PDFs
- Copied PDFs to OKF bundle at references/ford-upfitter-guides/ (from Q1900M)
- Documented which PDF is the actual wiring guide (2025 BBLB) vs compliance docs (2026/2027 IVMs)
- Included taillamp pinouts (C423 LH, C422 RH), turn signal circuits (CLS23, CLS27), front turn signals (CLS21, CLS25)
- End-of-frame blunt cut wire colors for stop/turn/park/backup circuits
- FDRS reconfiguration instructions for separate stop/turn or LED lamps
- Updated index.md with References section linking to upfitter guides

## 2026-06-23 — CAN Bus Reverse Engineering OKF Documentation

- Created `references/can-bus-reverse-engineering.md` — comprehensive OKF document covering:
  - Hardware inventory: OBDLink MX+ (STN2255 v5.12.4, dual-CAN), OBDX Pro VX (J2534), USB-to-CAN FD, ELM327 USB
  - Live tested vehicle: 2024 Ford F-450/F-550 Super Duty (VIN 1FDUF4HN6REE99454), ATSP6 protocol confirmed
  - Documented architectures: Ford Super Duty (HS-CAN1/2/3, MS-CAN, UFM) and RAM 4500/5500 HD (CAN-C, CAN-IHS, CAN-B, VSIM)
  - Complete CAN bus architectures with wire colors, module locations, tap points
  - OBDLink MX+ raw CAN streaming via ATMA — including the critical NO DATA re-entry pattern and non-blocking read loop
  - Canonical ELM327 AT command sequence with exact byte-level responses from live testing
  - STN2255 native commands for MS-CAN access (STP 54/55, STCFIG, STCMM) and Ford MS-CAN known gap
  - TCP bridge over Tailscale: PowerShell bridge script, com2tcp.exe, Python bridge option
  - CAN Commander Android App — 3 modes (OBDLink BT, USB2CAN local/remote), CanController interface, deployment via scp+su
  - Bug history from live app testing: ATSPA0 timeout, ATS0 parser failure, blocking readLine hang, Bluetooth \r\n vs \r desync, fake BT device list
  - Quick reference AT command table
  - Ford upfitter guide cross-reference (BBLB pages 106-108)
- Updated index.md with link to CAN bus reference in references section

## 2026-06-24 — CH340 ELM327 v1.5 Clone + CANable 2 Testing on Moto Root

- **CH340 ELM327 clone** — Confirmed working on Moto Root via libusb + Termux
  - Key discovery: Correct baud formula from usb-serial-for-android (BAUDBASE_FACTOR=1532620800, 2 control transfers)
  - ELM327 v1.5 detected at 38400 baud, all PID queries working
  - Tested on 2015 Ford E-450 (ED-4): Protocol = ISO 15765-4 CAN 11/500
  - Scripts saved to Termux home on Moto Root (~/test_real_init.py)
  - Python scripts require su for USB access
- **CANable 2 clone (RH02 Plus, branded Joining)** — Detected on Moto Root USB
  - VID 0x16d0, PID 0x117e, firmware: candleLight (gs_usb compatible)
  - CDC-ACM interfaces but Android usbfs prevents bulk data access
  - Recommended for SocketCAN on proper Linux
- **AndrOBD V2.7.9** — Installed from F-Droid for testing
  - Works with CH340 via usb-serial-for-android library (correct baud handling)
  - Also supports Bluetooth SPP for OBDLink MX+
- **OKF updated:** reference doc (sections 10-13), project status, changelog

## 2026-06-24 (2nd session) — OBDLink MX+ CAN Monitor + PVE SPP Update + P100 Purchase

### CAN / Remote Starter Project
- **OBDLink MX+ Bluetooth** — Paired with Moto Root (MAC 00:04:3E:8A:E7:15, link key confirmed)
- **CAN Commander app** (com.richai.cancommander) — Installed on Moto Root
  - Streams raw CAN frames via AT MA mode — confirmed working on Ford E-450
  - Custom app built by Linda — has Accessory 1/2 buttons for CAN injection
  - No export/save feature — need Bluetooth terminal app for log capture
- **CANable 2 (RH02)** — Still blocked by Android usbfs
  - Plan: Move to Q1900M or WSL2 on 8200 for SocketCAN candump
- **Next:** Capture remote starter signal sequence with Serial Bluetooth Terminal + AT MA mode

### GPU Purchases
- **2x Tesla P100 (16GB)** bought for $150 — arriving ~1 week
  - 4 total P100s eventually (2 more incoming)
- **Best config:** DL380 (2x P100 + 2x P4 = 48GB VRAM), DL360 (3x P4 = 24GB)
  - P100s can't fit in 1U chassis (full-height, dual-slot)

### PVE (DL380 Gen9) — HPE SPP Firmware Update
- **SPP version:** Gen9SPPGen91 (2022_0822.4)
- **Firmware before -> after:**
  - BIOS: P89 2019 -> P89 2021 (same version, newer date)
  - iLO4: 2.03 -> 2.81
  - P440ar RAID: 1.34 -> 7.00
  - NIC Broadcom 5719: v1.46
- **iLO Advanced license** activated — virtual media now available
- **iLO credentials:** michael / mcrart8794! — IP 192.168.12.126
- **Cold boot required** for iLO Advanced features to activate
- **POST notes:** SPP reset POST progress indicators - cosmetic only
  - F1 prompt due to config change detection - clears on next clean boot
- **All 7 drives healthy, all arrays OK, RAID battery OK**

### Network Changes
- **Q1900M internet** — Moved from PVE nic to PVE2 nic2 during PVE shutdown
  - PVE2 vmbr0: nic3 (LAN) + nic2 (Q1900M) + nic4 (spare)
  - Q1900M at 192.168.12.208 reachable at 0.3ms from PVE2
  - Network switch arriving to eliminate direct cable juggling

## 2026-06-25 — Dell Inspiron Laptop Documented in OKF

- **New file:** hardware/laptop.md — Dell Inspiron dual-boot laptop documentation
  - Ubuntu primary for CAN work (SocketCAN, gs_usb, can-utils)
  - SSH config, Tailscale, LAN IPs documented
  - Three-phase CAN test plan (CH340 -> MX+ -> CANable 2)
  - Remote starter signal capture workflow
- **SSH access:** laptop (Ubuntu 192.168.12.240), laptop-windows (Win Tailscale 100.107.180.3)
- **Boot rule:** Ubuntu for any CAN/OBD2 work, Windows not used for this

## 2026-06-25 — CAN Work Migrated to Ubuntu Laptop + 2025 F-450 Testing

### Hardware Environment Change
- **CAN work platform moved** from Moto Root (Android/Termux/libusb) to Dell Inspiron 14 7435 Ubuntu 24.04 laptop
- **Laptop details:** /tank/data/okf/hardware/laptop.md
- **SSH alias:** laptop-ubuntu (192.168.12.240, user michael-rich)
- **All CAN tools now run from laptop** — eliminates Android USB quirks and libusb IFACE BUSY issues

### CH340 ELM327 Clone — Now on Ubuntu Laptop
- **CH340** at /dev/ttyUSB0, 38400 baud, serial works natively via pyserial
- **ELM327 v1.5** confirmed working: AT Z, AT I, AT RV, AT DP
- **HS-CAN (500k):** Fully functional — AT MA captures live continuous traffic
  - RPM 0, Coolant 44°C, Throttle 37%, Run Time 645s
  - VIN decoded from HS-CAN: 1FDUF4HN6REE99454 (2025 Ford F-450)
  - Periodic heartbeat frame at ID 0x59E observed
- **MS-CAN (125k):** Clone detects bus but cannot decode properly
  - AT DP returns "SAE J1939 (CAN 29/250)" — clone misinterpretation of 125k MS-CAN
  - AT MA returns zero frames even with body module activity
  - **Conclusion:** ELM327 clone chip cannot handle 125k MS-CAN natively

### CANable2 on Ubuntu Laptop — SocketCAN Working
- **CANable2** (RH02 clone) at /dev/ttyACM0, slcan mode
- **Interface:** can0 at 500k (HS-CAN) via slcand -s8
- **HS-CAN capture:** Successful candump of live traffic including VIN, heartbeat, periodic frames
- **MS-CAN attempt:** Wires tapped to second CAN pair but no traffic detected
  - Possible causes: poor wire contact, body modules asleep, GWM blocking at OBD2 port
- **SavvyCAN AppImage:** Downloaded to /tmp/ but not yet executed

### Ford Architecture Discoveries (2025 F-450)
- **GWM (Gateway Module)** sits between OBD2 port and MS-CAN bus
  - HS-CAN traffic passes through to OBD2 normally
  - **MS-CAN traffic is filtered/blocked** at the OBD2 port — cannot see RFA, BCM, or body module frames directly from OBD2
  - Confirmed: zero MS-CAN frames at OBD2 even with lock/unlock/remote start button presses
- **RFA (Remote Function Actuator)** — suspected remote start module
  - Likely on MS-CAN or a private CAN bus
  - No frames observed on OBD2 MS-CAN pins during remote start attempts
- **FORScan approach:** Uses AT PP 2C "Session" command to open GWM bridge
  - STN2255/OBDLink MX+ chips support this — ELM327 clone does not
- **Hood interlock:** Prevents engine start but module should still send CAN frames when remote start is requested

### OBDLink MX+ Bluetooth — Attempted but Not Yet Paired
- **MAC:** 00:04:3E:8A:E7:15
- **Pair PIN:** 1234
- **Ubuntu 24.04 bluetoothctl:** Device discovered but pairing fails
  - BlueZ agent registration issue on Ubuntu 24.04
  - Needs proper BT agent setup before pairing succeeds
- **Key advantage:** MX+ supports AT PP 2C to open GWM bridge — essential for MS-CAN access at OBD2 port

### Next Steps / Future Work
1. **Fix OBDLink MX+ Bluetooth pairing** on Ubuntu — this is priority #1 for MS-CAN access
2. **Use MX+ with AT PP 2C "Session"** to open GWM bridge and capture MS-CAN traffic at OBD2 port
3. **Alternative: Wire tap CANable2 directly to RFA module wires** behind trim/glovebox
4. **Run SavvyCAN** for signal analysis and decoding
5. **Capture remote start signal sequence** on MS-CAN (or RFA private bus)

## 2026-06-25 — CAN Work on 2025 Ford F-450 (Ubuntu Laptop Phase)

### Platform Shift
- **CAN work moved** from Moto Root (Android/Termux/libusb) to Dell Inspiron 14 7435 Ubuntu 24.04 laptop
- SSH alias: laptop-ubuntu (michael-rich@192.168.12.240), documented in hardware/laptop.md
- Eliminates Android libusb IFACE BUSY issues and USB re-binding problems

### CH340 ELM327 v1.5 Clone on Ubuntu
- Works natively with pyserial at 38400 baud - no special init needed
- HS-CAN (500k): Fully functional - AT MA captures live traffic, all OBD2 PIDs readable
- MS-CAN (125k): Clone detects bus pin change (AT DP shows J1939/250) but reads zero frames
- **Conclusion:** ELM327 clone cannot handle 125k MS-CAN

### CANable2 on Ubuntu with SocketCAN
- /dev/ttyACM0, slcand mode, can0 at 500k
- HS-CAN capture successful: VIN decoded, heartbeat frames, periodic traffic logged
- MS-CAN attempted on OBD2 pins 3/11: zero traffic detected
- SavvyCAN AppImage downloaded to /tmp/ but not yet run

### Key Discovery: GWM Blocks MS-CAN at OBD2
- 2025 Ford F-450 Gateway Module filters MS-CAN at the OBD2 port
- FORScan uses AT PP 2C "Session" command to open the bridge
- STN2255 (OBDLink MX+) supports this - ELM327 clones do not
- Explains why zero MS-CAN frames captured despite button presses

### OBDLink MX+ Bluetooth Pairing Attempt
- MAC 00:04:3E:8A:E7:15 discovered but pairing fails
- BlueZ agent registration issue on Ubuntu 24.04
- **Priority for next session:** Fix pairing, then use AT PP 2C for MS-CAN access

### Remote Start Signal Capture Attempt
- Lock/unlock, door switches, remote start button - all produced zero MS-CAN frames
- Adapter confirmed on MS-CAN pins (DP detection changed from HS-CAN to J1939)
- **Need:** Open GWM session or direct RFA wire tap to see remote start CAN activity

### VIN Confirmation
- 1FDUF4HN6REE99454 decoded from HS-CAN traffic (2025 Ford F-450 gas engine)

## 2026-06-25 — Network Switch Upgrade + PVE2 SPP Update Start


### Network Re-cabled: ASUS Router Replaced with TL-SG108 Switch
- Old ASUS router (acting as switch) replaced with TP-Link TL-SG108 8-port gigabit switch
- Loop Prevention enabled on switch
- All devices now on single switch: PVE, PVE2, Q1900M, Compaq 8200, iLOs
- Q1900M moved from PVE internal bridging to direct switch connection
- Compaq 8200 got dedicated ethernet (previously limited)

### PVE Network Config Changes
- Added nic2 and nic3 to vmbr0 bridge -- any port on PVE gives network access now
- Enabled STP on vmbr0 (prevents loops with multiple ports to switch)
- Removed dead vmbr2 and vmbr3 bridges (192.168.1.10/24 subnet was unused)
- Backed up old config before changes

### PVE2 Network Fix
- nic0 had link but was not in vmbr0 bridge -- added nic0 to bridge-ports
- Added nic1 to bridge as well for future use
- Enabled STP on vmbr0
- Cluster returned to full quorum (2 nodes)

### Full Network Test Results
- All wired devices sub-millisecond latency (0.15-0.71 ms), 0% packet loss
- Switch throughput: 945 Mbps (line-rate gigabit), zero retransmits
- 40G interlink: 20 Gbps, zero retransmits
- VM100 shows high latency (265ms) -- VM load issue, not cabling
- Cables all good -- no replacements needed

### PVE2 SPP Update Started
- ISO: spp-gen9-2022.iso (4.3 GB) on Q1900M desktop
- PVE2 booting from HPE SPP update ISO via iLO virtual media

### Critical Discovery: iLO Virtual Media Mounting
- Browser URL mounts (NFS/HTTP) and file:// paths are unreliable on iLO4
- The HPE iLO Application on Windows is the ONLY reliable method
- Documented in both dl360-gen9.md and pve-proxmox.md hardware docs
