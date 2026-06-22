---
type: Network
title: Network Topology
description: Complete network layout including IPs, hostnames, and access methods across all devices.
tags: [network, topology, ip, ssh, tailscale]
timestamp: 2026-06-21T18:15:00Z
---

# Network Topology

## IP Assignments

| Device | Local IP | Tailscale | SSH Alias | User |
|--------|----------|-----------|-----------|------|
| PVE (Proxmox) | 192.168.12.132 | tail9fd7ba.ts.net | `ssh pve` | root |
| Q1900M | 192.168.12.208 | tail9fd7ba.ts.net | `ssh q1900m` | michael |
| Z390 | 192.168.12.246 | tail9fd7ba.ts.net | `ssh z390` | michael-rich |
| Compaq 8200 | 192.168.12.163 | tail9fd7ba.ts.net | `ssh 8200` | Michael |
| Moto Root | 100.97.184.43:8022 | tail9fd7ba.ts.net | `ssh moto-root` | u0_a394 |
| Moto G Power | 100.108.119.78:8022 | tail9fd7ba.ts.net | `ssh moto-g-power` | u0_a394 |
| Laptop | 192.168.12.240 | tail9fd7ba.ts.net | `ssh laptop` | michael-rich |
| VM100 (Ubuntu) | 100.72.54.55 | tail9fd7ba.ts.net | `ssh vm100` | mike |

## SSH Config
Located at `/root/.ssh/config` on the Hermes coder container.

### Important SSH Notes
- Q1900M: Tailscale SSH intercepts on tailscale IPs — use local IP for direct SSH
- Moto Root/G Power: SSH routed through PVE (port forwarding)
- 8200: Windows machine running mpSSH_0.2.1

## Network Services
| Service | Host | Port |
|---------|------|------|
| Proxmox API | pve:8006 | HTTPS |
| Samba (q1900) | q1900m:445 | SMB |
| NFS backups | q1900m:/srv/backups | NFS v3/4 |
| NFS isos | q1900m:/srv/isos | NFS v3/4 |
| K3s API | pve:6443 | HTTPS |
