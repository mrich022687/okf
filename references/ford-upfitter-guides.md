---
type: reference
title: Ford Upfitter Guides
tags: [ford, upfitter, wiring, electrical, super-duty]
location: Q1900M:/srv/isos/ford-upfitter-guide/
master: PVE:/tank/data/okf/references/ford-upfitter-guides/
---

# Ford Upfitter Guides

## File Inventory

| File | Size | Type | Pages | Description |
|------|------|------|-------|-------------|
| `2025-Ford-Upfitters-Guide.pdf` | 48 MB | Body Builders Layout Book (BBLB) | 119 | **Wiring diagrams, pinouts, technical specs** |
| `2026-Ford-Upfitters-Guide.pdf` | 19 MB | Incomplete Vehicle Manual (IVM) | 28 | Regulatory/compliance doc only |
| `2027-Ford-Upfitters-Guide.pdf` | 44 MB | Incomplete Vehicle Manual (IVM) | - | Regulatory/compliance doc only |

> **NOTE:** Only the **2025** PDF is the actual Body Builders Layout Book with wiring diagrams. The 2026 and 2027 PDFs are Incomplete Vehicle Manuals (compliance/regulatory docs required by law).

## Location

- **Q1900M (master copy):** `/srv/isos/ford-upfitter-guide/`
- **PVE OKF bundle:** `/tank/data/okf/references/ford-upfitter-guides/`
- **Ford online:** https://www.fordpro.com/en-us/upfit/publications/

## Vehicles Covered

- **Super Duty F-Series** (2025+)
  - F-250 SRW (10K, 9,900, >10K GVWR)
  - F-350 SRW/DRW
  - F-450 DRW
  - F-550 DRW
  - F-600 DRW
- Chassis Cab variants of all above
- Pickup Box Delete variants

## Key Sections (2025 BBLB)

| Section | Page |
|---------|------|
| Electrical Overview | 51 |
| Snowplow Circuit Information | 106 |
| **Rear Lighting Circuits** | **107** |
| Customer Access Circuits | 100 |
| BCM Fuse Layout | 86 |
| PDB/HCFB Fuse Layout | 87 |
| Vehicle Integration System 2.0 | 111-114 |
| Upfitter Switches | 113-114 |
| Ignition Run Circuits | 115 |

## Rear Lighting Circuit Pinouts

### Taillamp Connectors — Chassis Cab & Pickup Box Delete

| Circuit | Pin | LH (C423) | Color | RH (C422) | Color |
|---------|-----|-----------|-------|-----------|-------|
| Reverse | 1 | CLS10 | GN/WH | CLS10 | GN/WH |
| Ground | 2 | GD476 | BK/WH | GD476 | BK/WH |
| Park Lamps | 3 | CLS77 | BU/WH | CLS77 | BU/WH |
| Stop/Turn | 4 | CLS23 | GY/VE | CLS27 | GN/OG |

### Front Turn Signals (from Snowplow Circuit)
- **LF Turn:** CLS21 
- **RF Turn:** CLS25

### End-of-Frame Customer Access Blunt Cuts

| Wire Color | Circuit | Function |
|------------|---------|----------|
| Violet/Green or Violet/White | - | Service Brake Signal (CHMSL) |
| GY/BN | - | Back-up Lamps |
| WH | GD476 | Ground |
| VT/WH | - | High-Mount Stop Lamp |
| **YE** | **CLS23** | **Left-hand Stop/Turn** |
| **GN** | **CLS27** | **Right-hand Stop/Turn** |
| BN | CLS77 | Park Lamps |

## Turn Signal Tapping Guide

### Combined Stop/Turn (Factory Default)
Chassis Cab and Pickup Box Delete come with combined **Stop/Turn** circuits using PWM power. The taillamp connectors (C423 LH, C422 RH) pin 4 carry both stop and turn signals.

### Separate Stop & Turn (For Upfits Needing This)
1. Use CLS23 (YE wire) for **LH Turn**
2. Use CLS27 (GN wire) for **RH Turn**
3. Use CHMSL circuit (VT/WH) to feed **Stop** signals to both sides
4. Requires **Ford FDRS tool** to reconfigure BCM from "STOP/TURN" to "TURN ONLY"

### LED Lamp Configuration
If using LED taillamps, FDRS tool is needed to:
- Deactivate bulb outage detection
- Change power supply from PWM to DC

### Important Notes
- FET (Field-Effect Transistor) controlled outputs — max 2.9A per circuit
- CHMSL circuit is FET controlled, limited to 5A
- Headlamp switch communicates via **LIN bus** — do NOT connect additional loads directly
- All additional connections should use auxiliary relays
- End-of-frame blunt cuts available on frame left-hand side
