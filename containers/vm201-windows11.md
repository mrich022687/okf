---
type: VM
title: VM 201 — Windows 11 Pro (pve2)
description: Windows 11 Pro VM on pve2 (DL360 Gen9) — replacement for destroyed VM 101. Complete debloat, network recovery, OpenSSH setup, and optimization documentation.
tags: [windows, vm, debloat, openssh, pve2, vm201, dl360]
timestamp: 2026-06-28T18:00:00Z
---

# VM 201 — Windows 11 on pve2

## Overview

Windows 11 Pro VM running on pve2 (DL360 Gen9), repurposed from the Windows license of the destroyed VM 101 (lost to ZFS corruption on main PVE). This VM serves as Michael's personal Windows environment for iLO console access, Windows apps, and general administrative use.

| Field | Value |
|-------|-------|
| **VM ID** | 201 |
| **Host** | pve2 (192.168.12.50) |
| **IP** | 192.168.12.234/24 (DHCP) |
| **OS** | Windows 11 Pro (build 26200) |
| **License** | Preserved from VM 101 via SMBIOS UUID |
| **SMBIOS UUID** | `74a66e32-d27c-4a5d-af1b-63d0fac6508d` |
| **BIOS** | SeaBIOS |
| **Secure Boot** | Off |
| **RAM** | 8 GB (balloon, host min 4 GB) |
| **vCPUs** | 6 cores |
| **Disk** | 100 GB SATA (virtio) |
| **NIC** | virtio, MAC `AE:61:5A:19:EB:75` |
| **SSH** | Port 22, user `WIN11-VM_pve2`, password `Win11pve2` |
| **QEMU Agent** | Installed at `C:\Program Files\Guestfs\` — auto-starting |
| **Local Account** | `WIN11-VM_pve2` (local admin) |

---

## Issue Log — Every Problem, Root Cause, and Fix

### Issue 1: VM 101 Destroyed (ZFS Corruption)

- **Problem:** VM 101 on main PVE suffered ZFS pool corruption (tank pool had 1,163 permanent errors across drives)
- **Fix:** VM destroyed, license preserved. Recreated as VM 201 on pve2 with same SMBIOS UUID `74a66e32-d27c-4a5d-af1b-63d0fac6508d` so Windows activation carried over
- **Lesson:** Keep critical VMs on nodes with healthy storage

### Issue 2: Network Destroyed via QEMU device_del

- **Problem:** Assistant ran `device_del net0` in QEMU monitor to cut network during a hung DISM install. This removed the NIC at the hypervisor level entirely
- **Effect:** Windows showed no network adapter at all. QEMU device state corrupted
- **Fix:** Removed the hotplugged NIC residue, then re-added NIC via `qm set 201 --net0 virtio,bridge=vmbr0,macaddr=AE:61:5A:19:EB:75`. This generated a fresh VirtIO NIC
- **Result:** Windows detected new hardware, needed driver install
- **Lesson:** Never use `device_del` on a VM's primary NIC from QEMU monitor. The only safe ways to change a NIC are via `qm set` while VM is stopped, or by hotplugging an *additional* NIC

### Issue 3: VirtIO Drivers Missing After NIC Recreation

- **Problem:** After NIC was recreated with a new MAC, Windows didn't have the VirtIO drivers loaded for the new device. PCI device showed yellow triangle in Device Manager
- **Fix:** Installed VirtIO drivers manually via Device Manager → Browse for drivers → select from the VirtIO ISO mounted in Proxmox
- **Result:** NIC detected, DHCP lease obtained at 192.168.12.234
- **Note:** The VirtIO drivers also fix balloon, memory, and storage drivers

### Issue 4: APIPA (169.254.x.x) After Reboot

- **Problem:** After several reboots during setup, VM would get a link-local APIPA address instead of a DHCP lease
- **Root Cause:** Windows DHCP client needed renewal, or the VirtIO driver initialization was slightly delayed during boot
- **Fix:** `ipconfig /release && ipconfig /renew` from admin PowerShell, or simply reboot
- **Lesson:** If a fresh NIC gets APIPA, first verify the NIC model matches what's installed, then renew DHCP

### Issue 5: RAM Pegged at 100% During Setup

- **Problem:** With 16 GB host allocation, Windows showed 100% memory utilization (Proxmox showed allocated amount, Windows guest showed everything in use)
- **Root Cause:** Windows balloon driver wasn't working, and DISM operations consumed significant RAM
- **Fix:** Temporarily increased RAM to 32 GB to allow DISM/OpenSSH install. Later dropped to permanent config:
  ```
  # In /etc/pve/nodes/pve2/qemu-server/201.conf
  memory: 8192
  balloon: 8192
  ```
- **Actual idle usage:** ~3 GB
- **Lesson:** Windows VMs need more headroom during package installs. Balloon memory works after VirtIO drivers are installed

### Issue 6: OpenSSH Wouldn't Install — DISM Hung

- **Problem 1 (DISM hung):** `Add-WindowsCapability -Online -Name OpenSSH.Server` hung indefinitely at 0%
  - **Root Cause:** Proxmox showed RAM as allocated but Windows was swapping. 16 GB was insufficient for DISM operations
  - **Fix:** Downloaded OpenSSH MSI via browser and installed with `msiexec /i "OpenSSH-Win64.msi" /qn`
  
- **Problem 2 (sshd vanished after reboot):** After a system reboot, sshd service was gone. OpenSSH binaries existed at `C:\Windows\System32\OpenSSH\` but no service registered
  - **Root Cause:** MSI installation didn't properly persist the service registration across reboots (or a Windows update/feature reset cleared it)
  - **Fix:** Re-ran DISM successfully with 32 GB RAM, then when ssyd still didn't appear, manually created the service:
    ```
    sc.exe create sshd binPath="C:\Windows\System32\OpenSSH\sshd.exe" start=auto DisplayName="OpenSSH SSH Server" depend="Tcpip"
    sc.exe start sshd
    cd C:\Windows\System32\OpenSSH
    .\ssh-keygen.exe -A
    ```
  - **Lesson:** DISM OpenSSH install on Windows 11 preview builds (26200) has inconsistent service registration. Always verify with `Get-Service sshd` after install. Manual `sc.exe create` is the reliable fallback

### Issue 7: QEMU Guest Agent Intermittent

- **Problem:** Guest agent (`qemu-ga`) would randomly stop responding, making `qm guest exec` calls fail with "QEMU guest agent is not running"
- **Root Cause:** Unknown — likely timing-related on boot or after certain system operations
- **Fix:** Restart the service from within the VM:
  ```
  Start-Service qemu-ga
  ```
  Already set to auto-start
- **Lesson:** Keep a fallback connection method (SSH or console access) for when the guest agent flakes out

### Issue 8: Forgot VM Password

- **Problem:** Michael couldn't log in after a reboot
- **Fix:** Reset via guest agent from Proxmox host:
  ```
  qm guest passwd 201 WIN11-VM_pve2 Win11pve2
  ```
  (This command requires the password to be piped via stdin; direct argument passing didn't work)
- **Updated credentials:** Stored in password vault at `/root/.hermes/vault/`

### Issue 9: OneDrive + Telemetry Would Re-Enable

- **Problem:** Some debloated settings (telemetry, OneDrive auto-start) would come back after Windows updates or certain app installs
- **Root Cause:** Windows 11 is designed to re-enable these features silently
- **Fix:** Aggressive registry + service-based deactivation:
  - `sc.exe config DiagTrack start=disabled`
  - `reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\Windows Search" /v AllowCortana /t REG_DWORD /d 0 /f`
  - Registry policies survive updates better than UI-based settings

### Issue 10: OpenSSH Firewall Rule Missing

- **Problem:** After manual OpenSSH install, the firewall rule wasn't automatically created
- **Fix:** 
  ```
  New-NetFirewallRule -DisplayName "OpenSSH Server (sshd)" -Direction Inbound -Protocol TCP -LocalPort 22 -Action Allow
  ```
- **Lesson:** Manual service creation means manual firewall rules. Always add both steps

---

## Complete Debloat Checklist — All Changes Applied

### Xbox — Full Annihilation
- Services disabled: `XblAuthManager`, `XblGameSave`, `XboxNetApiSvc`, `XboxGipSvc`
- App packages removed: `Microsoft.XboxGameCallableUI` (only remaining Xbox component — system-level, can't remove)
- Xbox Game Bar removed
- App provisioning packages removed so reinstall is blocked

### Microsoft Bloatware Removed
- `*bing*`, `*skype*`, `*solitaire*`, `*news*`, `*finance*`, `*sports*`, `*zune*`, `*officehub*`
- `*getstarted*`, `*yourphone*`, `*clipchamp*`, `*windowscommunications*`
- Removed via `Get-AppxPackage | Remove-AppxPackage`

### Services Disabled
| Service | Why |
|---------|-----|
| `WSearch` (Windows Search) | Unnecessary — saves ~200 MB RAM, reduces disk I/O |
| `DiagTrack` (Telemetry) | Privacy — sends data to Microsoft |
| `dmwappushservice` | Device management push — not needed |
| `WMPNetworkSvc` | Legacy media sharing |
| `RemoteRegistry` | Security — remote registry access |
| `SysMain` (Superfetch) | Can cause high disk usage on VMs |

### Privacy Cleanup
- Advertising ID disabled
- Telemetry set to 0 (Security)
- Cortana completely disabled via group policy
- Windows tips/suggestions disabled
- SubscribedContent features disabled

### Performance Tweaks
| Tweak | Detail |
|-------|--------|
| Power plan | High Performance (`8c5e7fda-e8bf-4a96-9a85-a6e23a8c635c`) |
| Visual effects | Adjust for best performance |
| Taskbar animations | Disabled |
| Balloon tips | Disabled |
| Game Mode | Disabled |
| Standby timeout | Never (AC) |
| Hibernate timeout | Never (AC) |
| Boot startup delay | Removed (registry) |
| File extensions | Shown |
| Hidden files | Shown |

### Not Removed (Keepers)
- Windows Terminal
- Calculator
- Notepad
- Paint
- Snipping Tool
- Windows Security/Defender

---

## SSH Access

```
Host: 192.168.12.234
Port: 22
User: WIN11-VM_pve2
Password: Win11pve2
Shell: PowerShell (set via HKLM\SOFTWARE\OpenSSH\DefaultShell)
```

**First-time SSH connect:**
```bash
ssh win11-vm_pve2@192.168.12.234
# Password: Win11pve2
```

**Or using sshpass for scripting:**
```bash
sshpass -p "Win11pve2" ssh win11-vm_pve2@192.168.12.234 "command"
```

---

## QEMU Guest Agent Commands

From any Proxmox host (must be ssh'd into pve2):

```bash
# Check guest agent is alive
qm guest cmd 201 ping

# Run a command via guest agent (stdin method - most reliable for PowerShell)
echo "Get-Service sshd" | qm guest exec 201 --pass-stdin 1 -- powershell -NoProfile -

# Check OS info
qm guest cmd 201 get-osinfo

# Get logged-in user
pvesh get /nodes/pve2/qemu/201/agent/get-users

# Reset password (requires stdin pipe)
echo "NewPassword" | qm guest passwd 201 USERNAME
```

**Known issue:** Guest agent is intermittent. If `qm guest exec` returns "QEMU guest agent is not running", either wait a few seconds and retry, or restart the service from within the VM console.

---

## Backup

VM 201 is included in the nightly `vzdump` cron:
```bash
# In /etc/pve/vzdump.cron — added to existing job
vzdump 201 --mode snapshot --compress zstd --storage q1900 --prune-backups keep-last=3
```

Backups go to Q1900 NFS at `192.168.12.208:/srv/backups`.

---

## VM Config Reference

Full VM config on pve2 (`/etc/pve/nodes/pve2/qemu-server/201.conf`):

```ini
balloon: 8192
bios: seabios
boot: order=sata0;ide2;net0
cores: 6
cpu: x86-64-v2-AES
ide2: none,media=cdrom
machine: pc-i440fx-9.0
memory: 8192
meta: creation-qemu=9.0.2,ctime=1719168000
name: win11
net0: virtio=AE:61:5A:19:EB:75,bridge=vmbr0
numa: 0
onboot: 1
ostype: win11
sata0: pve2-extra:vm-201-disk-0,size=100G
scsihw: virtio-scsi-pci
smbios1: uuid=74a66e32-d27c-4a5d-af1b-63d0fac6508d
sockets: 1
vga: virtio
```

---

## Quick Reference — Common Tasks via SSH

```powershell
# Check services
Get-Service sshd,qemu-ga | Select Name,Status,StartType

# Restart VM remotely
powershell -Command "Restart-Computer -Force"

# Check disk
Get-PSDrive C | Select Used,Free

# Check debloat status
Get-Service XblAuthManager,XblGameSave,XboxNetApiSvc,WSearch,DiagTrack | Select Name,StartType,Status

# Reapply if needed
sc.exe config <service> start=disabled
```
