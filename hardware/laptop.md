# Dell Inspiron Laptop (Michael's Work Laptop)

## Overview
- **Make/Model:** Dell Inspiron (dual-boot)
- **OS 1:** Ubuntu (primary for CAN/Bus work — full SocketCAN, gs_usb, can-utils)
- **OS 2:** Windows (not used for CAN work — driver tool only)
- **Boot choice:** Ubuntu for any CAN bus / OBD2 / remote starter work

## Network Access

| OS | LAN IP | Tailscale IP | User |
|---|---|---|---|
| Ubuntu | 192.168.12.240 | (on tailnet) | michael-rich |
| Windows | DHCP | 100.107.180.3 | mrich |

- **SSH (Ubuntu):** `ssh laptop` — uses michael-rich@192.168.12.240
- **SSH (Windows):** `ssh laptop-windows` — uses mrich@100.107.180.3 via PVE proxy
- **SSH already set up:** OpenSSH server installed on both OS
- **Tailscale:** Connected on both OS — reachable via tailnet when not on home LAN

## CAN / OBD2 Equipment to Test

Proceed in order, only move to next if previous is insufficient:

### Phase 1 — CH340 ELM327 Clone (USB)
- **Dongle:** 1a86:7523, ELM327 v1.5 at 38400 baud
- **On Ubuntu:** Shows as /dev/ttyUSB0 (CH340 driver built into kernel)
- **Goal:** Read OBD2 PIDs and try AT MA (monitor all) mode for raw CAN frames
- **Already tested on Moto Root** — works for PIDs, AT MA for raw frames untested on laptop
- **Python script approach:** pyserial + python-can talking to serial device

### Phase 2 — OBDLink MX+ (Bluetooth SPP)
- **MAC:** 00:04:3E:8A:E7:15
- **Method:** Bluetooth SPP from Ubuntu — Python RFCOMM socket to send AT commands
- **Goal:** AT MA mode for raw CAN frame capture (confirmed working with CAN Commander on Android)
- **Note:** Same ELM327 AT command set, just over Bluetooth instead of USB

### Phase 3 — CANable 2 / RH02 Plus (USB CAN FD)
- **VID/PID:** 0x16d0:0x117e
- **Firmware:** candleLight (gs_usb compatible)
- **On Ubuntu:** Load `gs_usb` kernel module → shows as `can0` SocketCAN interface
- **Goal:** Full bus sniffing with candump + frame injection with cansend
- **Setup commands:**
  ```bash
  sudo modprobe gs_usb
  sudo ip link set can0 type can bitrate 500000
  sudo ip link set can0 up
  candump -l can0        # log everything to file
  cansend can0 7DF#0201050000000000   # send a message
  ```
- **Ford E-450 bus:** HS CAN 500k, ISO 15765-4 (11-bit), wires at remote starter module

## Remote Starter Signal Capture Plan
1. Connect CANable to Ubuntu laptop
2. SSH in, run candump logging
3. User triggers remote starter
4. Pull the log file, analyze CAN frames
5. Replay sequence to replicate remote start

## OKF Location
- This file: `/tank/data/okf/hardware/laptop.md`
- CAN reference: `/tank/data/okf/references/can-bus-reverse-engineering.md`
- Project tracking: `/tank/data/okf/projects/can-bus-reverse-engineering.md`
