---
type: Project
title: CAN Commander
description: Android app for CAN bus reverse engineering. Connects to OBDLink MX+ via Bluetooth and supports SocketCAN for USB2CAN adapters.
tags: [android, can, obd, obdlink, bluetooth, socketcan, reverse-engineering]
timestamp: 2026-06-21T18:15:00Z
---

# CAN Commander

## Overview
Android app for reverse-engineering CAN bus signals on emergency vehicles. Connects to OBDLink MX+ (Bluetooth) or USB2CAN adapters (SocketCAN).

## Platform
- **Android APK:** `/home/android/cancommander/` on PVE
- **Desktop app:** `/home/android/cancommander-desktop/`

## Hardware
- **OBDLink MX+** — Bluetooth SPP, MAC: 00:04:3E:8A:E7:15
- **USB2CAN** — SocketCAN/SLCAN interface

## Key Features
- Abstract CanController interface (ELM327 BT / SocketCAN)
- 4-state connection state machine (DISCONNECTED/CONNECTING/CONNECTED/DISCONNECTING)
- ATMA non-blocking read loop with re-entry on NO DATA
- Real bonded Bluetooth device list (no hardcoded placeholders)
- Configurable signal buttons for CAN ID monitoring

## History
Built and debugged through multiple iterations — see [changelog](log/changelog.md) for the full saga of bugs found and fixed during live vehicle testing.
