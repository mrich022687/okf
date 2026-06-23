---
type: Network
title: SSH Access
description: SSH configuration, host aliases, and access methods for all homelab devices.
tags: [ssh, access, config, hosts]
timestamp: 2026-06-23T00:00:00Z
---

# SSH Access

## Host Aliases

All configured in `/root/.ssh/config` on the Hermes coder container (CT 106).

| Alias | Target | IP/Port | Notes |
|-------|--------|---------|-------|
| `pve` | root@pve | 192.168.12.132 | Main Proxmox host |
| `pve2` | root@pve2 | 10.10.10.2 | DL360 via 40G link (or .50 when LAN connected) |
| `q1900m` | michael@q1900m | 192.168.12.208 | Use local IP, not Tailscale |
| `z390` | michael-rich@z390 | 192.168.12.246 | Main desktop |
| `laptop` | michael-rich@laptop | 192.168.12.240 | GF's laptop |
| `8200` | Michael@8200 | 192.168.12.163 | Windows (mpSSH) |
| `vm100` | mike@vm100 | 100.72.54.55:22 | Via PVE port forward |
| `moto-root` | u0_a394@moto-root | 100.97.184.43:8022 | Via PVE port forward |
| `moto-g-power` | u0_a394@moto-g-power | 100.108.119.78:8022 | Via PVE port forward |

## Key-Based Access
- All hosts accept the coder container's SSH key
- pve2 accessible only via 40G link until management NIC is connected
- GitHub deploy key at `/root/.ssh/github_okf` for OKF repo pushes
