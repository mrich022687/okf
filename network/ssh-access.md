---
type: Network
title: SSH Access Configuration
description: SSH key-based access setup across all devices. Configuration and host aliases.
tags: [ssh, access, configuration, keys]
timestamp: 2026-06-21T18:15:00Z
---

# SSH Access Configuration

## Host Aliases

All defined in `/root/.ssh/config` on the Hermes coder container:

| Alias | Target | Method |
|-------|--------|--------|
| `pve` | root@192.168.12.132 | Direct |
| `q1900m` | michael@192.168.12.208 | Direct (avoids Tailscale SSH intercept) |
| `z390` | michael-rich@192.168.12.246 | Direct |
| `8200` | Michael@192.168.12.163 | Direct (Windows) |
| `vm100` | mike@100.72.54.55 | Via PVE |
| `laptop` | michael-rich@192.168.12.240 | Direct |
| `moto-root` | u0_a394@100.97.184.43:8022 | Via PVE |
| `moto-g-power` | u0_a394@100.108.119.78:8022 | Via PVE |

## Key Setup
SSH keys were set up using the [hermes-ssh-key-setup](skills/hermes-ssh-key-setup.md) workflow.

All 8 devices accessible as of last verified.
