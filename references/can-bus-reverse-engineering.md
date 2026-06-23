---
type: reference
title: CAN Bus Reverse Engineering
tags: [automotive, can-bus, obd2, obdlink, ford, ram, emergency-vehicle]
vehicles:
  - 2024 Ford F-450/F-550 Super Duty (VIN 1FDUF4HN6REE99454)
  - RAM 4500/5500 Heavy Duty (documented architecture)
---

# CAN Bus Reverse Engineering

## Overview

This document covers the complete CAN bus reverse engineering workflow developed for emergency vehicle upfitting at Hughes Fire. It includes hardware, software, vehicle architectures, remote bridge setup, and the custom CAN Commander Android app.

---

## 1. Hardware Inventory

### OBDLink MX+ (Primary OBD2 Adapter)
- **Chipset:** STN2255 v5.12.4 (dual-CAN capable)
- **ELM327 compatibility:** v1.4b
- **Connection:** Bluetooth SPP (Serial Port Profile)
- **Baud rate:** 115200
- **Pair PIN:** 1234
- **Power:** From OBD2 port (Pin 16, 12V) — NO internal battery
  - ~39 mA idle, ~69 mA active
  - BatterySaver sleep mode (~1.8 mA)
  - Will NOT power on without vehicle connection
- **CAN access:** Single CAN controller, software-switched between HS and MS transceivers
  - Pin 6/14 = HS-CAN (500 kbps)
  - Pin 3/11 = MS-CAN (125 kbps) — requires STN native commands
- **Cannot monitor both buses simultaneously** — needs OBDLink EX for dual

### OBDX Pro VX (ECU Programming)
- J2534 PassThru — ECU programming (GM, Toyota, Honda, etc.)
- Multi-protocol: SW-CAN (GMLAN 33.3 kbps), dual CAN, UART/KWP2000, VPW/PWM
- USB CDC ACM — best for phone OTG (kernel driver usually present)
- Overkill for pure sniffing, essential for ECU programming

### USB-to-CAN FD Adapter
- Bi-directional, for non-OBD2 CAN networks
- Connects to CAN-H/CAN-L on target network
- Virtual serial port or SocketCAN device
- Essential for splice pack taps and body bus access

### ELM327 USB
- CH340/CP210x chipset
- Needs kernel driver — does NOT work on stock Android without root
- Works on Windows/Linux laptops

---

## 2. Vehicles Tested / Documented

### 2024 Ford F-450/F-550 Super Duty
- **VIN:** 1FDUF4HN6REE99454
- **Plant:** Kentucky Truck Plant
- **OBD Protocol:** ISO 15765-4 CAN (11-bit, 500 kbps) — ATSP6
- **Configuration:** 11.8-13.0V ignition-on (engine off)
- **Live test date:** Confirmed via PowerShell TCP bridge over Tailscale

#### CAN Bus Architecture (2023-2026)

| Bus | Speed | OBD2? | Key Modules |
|:---|:----:|:-----:|:-----------|
| HS-CAN1 (Powertrain) | 500 kbps | YES (pins 6/14) | PCM, TCM, ABS, ESC, 4WD, TBCM |
| HS-CAN2 (Chassis) | 500 kbps | NO | BCM, SCCM, RCM, GWM, IPMA |
| HS-CAN3 (Upfitter) | 500 kbps | NO | UFM, TPMS, Trailer Controls |
| MS-CAN | 125 kbps | NO | Door modules, Seats, Sunroof |
| Trailer CAN | 500 kbps | NO | Trailer camera, TPMS, Brake controller |

#### Wire Colors
- **HS-CAN1:** H = Brown/White, L = Blue/Orange (OBD2 pins 6/14)
- **HS-CAN2:** H = White/Red (BCM C2280C pin 23), L = Brown/White (pin 24)
- **HS-CAN3:** H = White/Red, L = White/Blue (UFM connector pins 1/2)
- **MS-CAN:** H = Orange/Violet, L = Orange/Brown

#### Ford Upfitter Module (UFM)
- **Location:** Behind passenger glove box
- **4-Pin Connector:** Pin 1 = CAN H (HS-CAN3) White/Red, Pin 2 = CAN L White/Blue, Pin 3 = GND, Pin 4 = 12V
- **CAN IDs:** 0x3B0 (switch state), 0x3B1 (diagnostics), 0x3B2 (output control), 0x3B3 (input sense)
- **Outputs:** 4 low-current switches (2.5A each), 3 high-current relays (40A each)

### RAM 4500/5500 Heavy Duty (2023-2026)
- Architecture documented for future testing

| Bus | Speed | OBD2? | Key Modules |
|:---|:----:|:-----:|:-----------|
| CAN-C (Chassis) | 500 kbps | YES | PCM, TCM, ABS, SCM, VSIM |
| CAN-IHS (Interior HS) | 500 kbps | NO | BCM, RF Hub, HVAC, VSIM |
| CAN-B (Body) | 125 kbps | NO | Doors, Seats, Overhead console |

#### Vehicle System Interface Module (VSIM)
- **Location:** Driver-side lower dash, near steering column
- **12-Pin:** Pins 1-2 = CAN-C (Yellow/Black, Yellow/White), Pins 3-4 = CAN-IHS (Green/Black, Green/White)
- **CAN IDs:** 0x1F0 (switch state), 0x1F1 (output control), 0x1F2 (diagnostics)
- **Outputs:** 10 programmable inputs, 4 high-current outputs (20A each, 60A total)

---

## 3. OBDLink MX+ — Raw CAN Streaming

### Critical: STN2255 vs STN1170
- Always run `STI` on first connect to identify the chip
- STN2255 = dual-CAN, supports MS-CAN access via native commands
- STN1170 = single-CAN, older chip
- OBDLink MX+ uses STN2255 v5.12.4

### Reliable ELM327 Init Sequence

```
ATZ          → reset (2-3s boot time)
ATRV         → check battery voltage (11.8V+ needed)
ATE0         → echo off
ATH1         → headers on (include CAN ID in output)
ATCAF0       → CAN auto-format off (raw CAN data)
ATSP6        → ISO 15765-4 CAN, 11-bit, 500k (INSTANT)
ATCFC0       → CAN flow control off
ATR1         → responses always on
```

**DO NOT use ATSPA0** — auto-detect takes 15-30+ seconds with no feedback, freezes apps

**DO NOT pipeline AT commands** — ELM327 v1.4b does NOT queue commands. Send one at a time with 1.5-3s delays between each.

### Canonical OBD2 PID Queries

```
0100 → 41 00 BF 9E A8 17  (supported PIDs 01-20)
010C → 41 0C 00 00        (RPM — 0 = engine off)
010D → 41 0D 00           (vehicle speed)
0105 → 41 05 3B           (coolant temp: 0x3B - 40 = 19°C)
0104 → 41 04 00           (engine load %)
0902 → multi-frame VIN    (ISO-TP, frames 10/21/22)
```

### STN2255 Native Commands

| Command | Purpose |
|---------|---------|
| `STI` | Chip version info (returns STN2255 v5.12.4) |
| `STP 54` | MS-CAN, 29-bit, 125 kbps |
| `STP 55` | MS-CAN, 11-bit, 125 kbps |
| `STPBR 125000` | Set baud rate override |
| `STMA` | Monitor All (enhanced ATMA) |
| `STPX <header> <data>` | Atomic CAN transmit |
| `STCFIG 2,0,2,125000` | Configure CAN channel 2 for MS-CAN |
| `STCMM 2` | Switch active channel to channel 2 |

### MS-CAN Access (Known Gap)
- STN2255 hardware supports MS-CAN via pins 3/11
- Standard AT commands (`ATSP A`) do NOT work for MS-CAN
- STN native commands (`STP 54`, `STP 55`, `STCFIG`, `STCMM`) may work but need testing
- Some newer Ford Super Duty models use HS2-CAN (500k) on pins 3/11 instead of traditional MS-CAN (125k) — test both

### ATMA Monitoring — The NO DATA Problem

ATMA does NOT stream forever. If no CAN frames arrive for ~5 seconds, STN chips print `NO DATA` and EXIT monitor mode.

**Fix:** Non-blocking read loop with auto-re-entry:

```kotlin
while (isActive) {
    if (reader.ready()) {
        val line = r.readLine()
        if (line.contains("NO DATA") || line.contains("STOPPED")) {
            sendCmd("ATMA")  // re-enter monitor mode
            continue
        }
        parseAndHandle(line)
    } else {
        // Keepalive: re-enter ATMA after 4s of idle
        if (idle > 4000) sendCmd("ATMA")
        delay(50)
    }
}
```

---

## 4. TCP Bridge Over Tailscale

### Purpose
Enables remote CAN bus access from the Hermes server to a vehicle via a laptop with OBDLink MX+ connected over Bluetooth.

### Architecture
```
Vehicle OBD2 → OBDLink MX+ → Bluetooth → Windows Laptop COM9 → TCP Bridge → Tailscale → Hermes Server
```

### Setup Steps

**1. Pair OBDLink MX+ to Windows laptop**
- PIN: 1234
- COM port appears in Device Manager > Ports (COM & LPT)
- Windows may create TWO ports (Outgoing + Incoming) — always use Outgoing
- "Not connected" in Bluetooth settings is a RED HERRING — COM port binding is independent

**2. Option A: PowerShell TCP Bridge (preferred, no downloads)**

```powershell
$port = new-Object System.IO.Ports.SerialPort COM9,115200,None,8,one
$port.ReadTimeout = 200
$port.Open()
$listener = [System.Net.Sockets.TcpListener]::new(24049)
$listener.Start()
$c = $listener.AcceptTcpClient()
$ns = $c.GetStream()
$buf = New-Object byte[] 4096
while($true) {
    try { $n = $port.Read($buf,0,$buf.Length); if($n -gt 0) { $ns.Write($buf,0,$n); $ns.Flush() } } catch {}
    try { if($ns.DataAvailable) { $n = $ns.Read($buf,0,$buf.Length); if($n -gt 0) { $port.Write($buf,0,$n) } } } catch {}
    Start-Sleep -Milliseconds 5
}
```

**3. Option B: com2tcp.exe (tiny download)**
```cmd
com2tcp --baud 115200 \\.\COM9 24049
```

**4. Connect from server**
```python
import socket
s = socket.socket()
s.connect(('<laptop-tailscale-ip>', 24049))
s.sendall(b'ATZ\r\n')
# ... standard AT command sequence
```

### Known Issues
- **Port lock after crash:** Close PowerShell entirely, open fresh. If TCP port still blocked, switch to 24050.
- **COM port denied:** Close SavvyCAN or anything else using the port
- **\r vs \r\n:** ELM327 sees \n as a separate empty command — use \r only

---

## 5. CAN Commander Android App

### Overview
Custom Jetpack Compose Android app installed on the Moto Root phone for mobile CAN bus control. Built for Rich AI, LLC.

### App Modes

| Mode | Description | Interface |
|------|-------------|-----------|
| OBDLink MX+ via Bluetooth | Phone talks to OBDLink MX+ over Bluetooth SPP | `ObdLinkMxPlusController` |
| USB2CAN via Phone OTG | Local SocketCAN (can0) on phone | `SocketCanController("local")` |
| USB2CAN via Remote SSH | Streams candump over Tailscale SSH to a laptop | `SocketCanController("user@host")` |

### App Features
- START/STOP monitoring button
- ACC 1 / ACC 2 configurable signal injection buttons
- Settings screen: adapter type, BT device, SSH host, CAN ID/data per button
- Real-time CAN message display in dark monospace terminal theme
- Full Material 3 dark color scheme

### CanController Interface
```kotlin
interface CanController {
    suspend fun connect(address: String): Boolean
    suspend fun sendCanFrame(canId: String, data: String, dlc: Int = 8)
    suspend fun startMonitoring(onMessageReceived: (CanMessage) -> Unit)
    suspend fun sendRawCommand(command: String): String
}
```

### Debug History — Bugs Fixed During Live Testing

| Bug | Symptom | Fix |
|-----|---------|-----|
| ATSPA0 auto-detect | App stuck "Connecting..." 30s+ | Changed to ATSP6 |
| ATS0 spaces off | Parser returned null for all frames | Removed ATS0 |
| Blocking readLine() | App hung after ATMA exited with NO DATA | Non-blocking reader.ready() + re-entry loop |
| ATAT1 adaptive timing | Unpredictable monitoring | Removed |
| No keepalive | ATMA silently exited, loop never re-entered | Added 4s keepalive timer |
| \r\n instead of \r | Bluetooth desync and disconnect | Changed to \r only |
| Fake BT device list | Placeholder MACs, not real paired devices | Read bondedDevices from BluetoothAdapter |

### Deployment
```bash
scp -P 8022 app-debug.apk u0_a394@<phone-tailscale-ip>:~/cancommander.apk
ssh -p 8022 u0_a394@<phone-tailscale-ip> \
  "su -c 'cp .../cancommander.apk /data/local/tmp/ && pm install -r /data/local/tmp/cancommander.apk'"
```

### Build
```bash
cd /home/android/cancommander
export ANDROID_HOME=/opt/android-sdk
./gradlew assembleDebug --no-daemon
```

Source code at `/home/android/cancommander/` on the Proxmox host.

---

## 6. Remote Assistance Workflow

### Default: Capture & Decode (No Remote Access)
1. User captures CAN log in SavvyCAN, saves as .CSV
2. User sends .CSV via Telegram
3. Agent analyzes hex patterns, identifies CAN IDs and byte mappings
4. Agent returns decoded analysis

### Live: TCP Bridge Over Tailscale
1. User connects OBDLink MX+ to laptop via Bluetooth
2. User runs PowerShell TCP bridge script
3. Agent connects to laptop Tailscale IP on bridge port
4. Agent sends AT commands in real-time

### Fallback: Phone Bridge
1. Moto Root phone runs Serial Bluetooth Terminal app
2. App bridges OBDLink MX+ Bluetooth to local TCP server
3. Agent connects over Tailscale to phone

---

## 7. Quick Reference — AT Command Tips

| Goal | Command | Notes |
|------|---------|-------|
| Reset adapter | `ATZ` | 2-3s boot time |
| Check power | `ATRV` | 11.8V+ needed |
| Identify chip | `STI` | vs `ATI` for ELM327 version |
| Set CAN 500k 11b | `ATSP6` | Instant — DO NOT use ATSPA0 |
| Set CAN 500k 29b | `ATSP7` | For extended IDs |
| Read RPM | `010C` | ÷4 for RPM value |
| Read speed | `010D` | km/h |
| Read VIN | `0902` | Multi-frame, 3s delay |
| Monitor all | `ATMA` | Use non-blocking read loop |
| Raw CAN filters | `ATCF x,y` | Set acceptance mask |
| Send raw frame | `STPX header data` | STN native only |
| Switch to MS-CAN | `STP 55` | 125 kbps, not ATSP A |

---

## 8. Ford Upfitter Resources

The 2025 Ford Body Builder Layout Book is stored in the OKF at:
- `/tank/data/okf/references/ford-upfitter-guides/2025-Ford-Upfitters-Guide.pdf`
- Online: https://www.fordpro.com/en-us/upfit/publications/

Key pages:
- Page 106 — Snowplow circuit / front turn signals
- Page 107 — Rear lighting circuits, end-of-frame blunt cuts, stop/turn pinouts
- Page 108 — FDRS reconfiguration for separate stop/turn

See also: `references/ford-upfitter-guides.md`

---

## 9. References (OKF)

- `references/ford-upfitter-guides.md` — Ford upfitter PDF inventory
- `references/ford-hd-can-bus.md` — Full Ford Super Duty CAN bus architecture
- `references/ram-hd-can-bus.md` — Full RAM HD CAN bus architecture
- `references/android-obd2-bridge.md` — Phone-based OBD2 bridge setup
- `references/android-rooting-guide.md` — Moto G Play rooting procedure
