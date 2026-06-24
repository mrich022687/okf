---
type: Project
title: CAN Bus Reverse Engineering — Emergency Vehicle Upfitting
description: CAN bus signal identification and reverse engineering for emergency vehicle upfitting. Work done by Michael and Linda. See the full reference document for vehicle architectures, wiring, and CAN bus details.
tags: [can, can-bus, reverse-engineering, upfitting, emergency-vehicle]
timestamp: 2026-06-24T00:00:00Z
status: In Progress
contributors: [Michael, Linda, Larry]
---

# CAN Bus Reverse Engineering — Emergency Vehicle Upfitting

## Overview

CAN bus reverse engineering work performed by **Michael** and **Linda** for emergency vehicle upfitting at Hughes Fire.

## Latest Progress (2026-06-24)

### CH340 ELM327 v1.5 Clone — Working on Moto Root
- **Adapter:** CH340/CH341A clone (ID 1a86:7523, bcdDevice 0x8134)
- **Platform:** Moto Root (rooted Android 13) via libusb in Termux
- **Key discovery:** Baud rate formula from `usb-serial-for-android` library is the correct init
- **Baud formula:** `BAUDBASE_FACTOR=1532620800`, dual control transfer sequence
- **Vehicle tested:** 2015 Ford E-450 (at Hughes Fire)
- **Results:** Confirmed ISO 15765-4 CAN 11/500, all PIDs readable (12.7V, RPM, coolant, speed)

### CANable 2 Clone (RH02) — Detected
- **Adapter:** CANable 2 firmware (VID 0x16d0, PID 0x117e)
- **Interface 0:** CDC-ACM control (class 02/02/01)
- **Interface 1:** CDC-ACM data (class 0A/00/00)
- **Status:** libusb claims IFACE 0 but IFACE 1 is BUSY (Android usbfs)
- **Needed:** SocketCAN on proper Linux for full capability (candump, cansend, cangw)

### AndrOBD App — Installed for Testing
- **Source:** F-Droid (com.fr3ts0n.ecu.gui.androbd)
- **Version:** V2.7.9
- **Protocol:** Uses usb-serial-for-android — should work with CH340 directly

## Documents

- **[CAN Bus Reverse Engineering Reference](/references/can-bus-reverse-engineering.md)** — Full technical reference covering hardware, vehicle architectures, CAN bus streaming, TCP bridge, and CAN Commander app
- **[CAN Commander App](/projects/can-commander.md)** — Android app for signal monitoring
- **[Ford Upfitter Guides](/references/ford-upfitter-guides.md)** — Factory wiring documentation
