---
type: Project
title: Retro Games
description: Android retro games app — Tanks, Pong, and Snake in a single Jetpack Compose app.
tags: [android, games, retro, compose, tanks, pong, snake]
timestamp: 2026-06-21T18:15:00Z
---

# Retro Games

## Overview
Three retro games in a single Jetpack Compose Android app.

## Games
| Game | Type | Controls |
|------|------|----------|
| **Tanks** | 2-player hot-seat artillery | Angle + power buttons + FIRE |
| **Pong** | 1-player vs CPU (vertical) | Touch-slide anywhere on screen |
| **Snake** | Classic snake | Swipe gestures |

## Source
- **Location:** `/home/android/retrogames/` on PVE
- **Package:** `com.richai.retrogames`
- **APK:** `Retro Games.apk` on Moto Root

## Build
```bash
cd /home/android/retrogames
export ANDROID_HOME=/opt/android-sdk
./gradlew assembleDebug --no-daemon
```

## Version History
| Version | Date | Changes |
|---------|------|---------|
| 1.0 | Session 23 | Initial release |
| 1.1 | Session 24 | Swipe controls, smooth terrain, vertical Pong, slower Snake |
| 1.2 | Session 24 | Difficulty dropdowns, power multiplier 5.0, paddle angle 3.0 |
| 1.3 | 2026-06-21 | Per-player saved angle/power in Tanks |

## Latest Change (v1.3)
Each player in Tanks now has their own saved angle and power:
- P1: `p1Angle` (default 45°), `p1Power` (default 50)
- P2: `p2Angle` (default 45°), `p2Power` (default 50)
- Settings persist between turns — other player never sees them
