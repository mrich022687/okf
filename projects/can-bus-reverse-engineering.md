---
type: Project
title: CAN Bus Reverse Engineering - Emergency Vehicle Upfitting
description: CAN bus signal identification and reverse engineering for emergency vehicle upfitting.
tags: [can, can-bus, reverse-engineering, upfitting, emergency-vehicle, ford-f450]
timestamp: 2026-06-25T00:00:00Z
status: In Progress
contributors: [Michael, Linda, Larry]
---

# CAN Bus Reverse Engineering - Emergency Vehicle Upfitting

## Overview

CAN bus reverse engineering for emergency vehicle upfitting at Hughes Fire.

**Current vehicle:** 2025 Ford F-450 (VIN 1FDUF4HN6REE99454)
**Target signal:** Remote start activation from RFA module

## Latest Progress (2026-06-25)

### Platform Shift: Android to Ubuntu Laptop

CAN work migrated from Moto Root (Termux/libusb) to Dell Inspiron 14 7435 Ubuntu 24.04 laptop. All CAN tools now run via SSH from PVE container.

### Adapter Status

| Adapter | HS-CAN 500k | MS-CAN 125k | Notes |
|---------|-------------|-------------|-------|
| CH340 ELM327 v1.5 | Working (AT MA, PID read) | Silent (clone can't decode 125k) | Good for ECU diag only |
| CANable2 RH02 | SocketCAN can0 working | No traffic detected | Wires may need better contact |
| OBDLink MX+ BT | Untested | Untested | Needs BlueZ pairing fix |

### Key Discovery: GWM Blocks MS-CAN at OBD2

The 2025 Ford F-450 Gateway Module (GWM) filters MS-CAN traffic at the OBD2 port. HS-CAN passes through, but body bus frames (RFA, BCM) are blocked. FORScan uses AT PP 2C "Session" command to open the bridge - supported by STN2255 (OBDLink MX+) but NOT by ELM327 clones.

### Remote Start Attempt Results

All button presses produced zero MS-CAN frames at OBD2:
- Key fob lock/unlock buttons
- Interior door lock switches
- Remote start button on key fob (multiple attempts)

Adapter hardware-switched to MS-CAN pins, confirmed by AT DP detection change (from ISO 15765-4 to J1939/250).

### VIN

1FDUF4HN6REE99454 - 2025 Ford F-450 gas engine (decoded from HS-CAN traffic)

### Key Learnings

1. ELM327 clone cannot handle 125k MS-CAN - detects as J1939/250 but reads no frames
2. GWM blocks MS-CAN at OBD2 - need AT PP 2C session or direct wire tap
3. CANable2 with SocketCAN is the reliable capture tool on HS-CAN
4. OBDLink MX+ is the right tool for MS-CAN access through GWM

### Next Steps

1. Fix OBDLink MX+ Bluetooth pairing on Ubuntu (priority #1)
2. Use MX+ with AT PP 2C to open GWM bridge for MS-CAN capture
3. Alternative: Direct wire tap CANable2 to RFA module behind trim
4. Run SavvyCAN for signal analysis once MS-CAN frames are captured

## Documents

- [CAN Bus Reference](references/can-bus-reverse-engineering.md)
- [CAN Commander App](projects/can-commander.md)
- [Ford Upfitter Guides](references/ford-upfitter-guides.md)
