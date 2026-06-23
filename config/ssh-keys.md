---
type: Config
title: SSH Keys
description: SSH key management and access configuration across all homelab devices.
tags: [ssh, keys, access, security]
timestamp: 2026-06-23T00:00:00Z
---

# SSH Keys

## Key Locations
- **Coder container (CT 106):** `/root/.ssh/` — all device keys
- **Main container:** `/root/.ssh/` — own key
- **PVE host:** `/root/.ssh/` — jump host

## Host Aliases (from coder container)
| Alias | Target | Method |
|-------|--------|--------|
| `pve` | root@192.168.12.132 | Direct |
| `pve2` | root@10.10.10.2 | Via 40G link (or 192.168.12.50 when LAN connected) |
| `q1900m` | michael@192.168.12.208 | Direct (not Tailscale IP) |
| `z390` | michael-rich@192.168.12.246 | Direct |
| `laptop` | michael-rich@192.168.12.240 | Direct |
| `8200` | Michael@192.168.12.163 | Windows (mpSSH) |
| `vm100` | mike@100.72.54.55 | Via PVE port forward |
| `moto-root` | u0_a394@100.97.184.43:8022 | Via PVE port forward |
| `moto-g-power` | u0_a394@100.108.119.78:8022 | Via PVE port forward |

## GitHub Deploy Key
- **Purpose:** Push OKF knowledge base to github.com/mrich022687/okf.git
- **Key:** `/root/.ssh/github_okf` (ED25519, no passphrase)
- **Added to GitHub:** SSH deploy key on repo settings
