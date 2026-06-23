---
type: Network
title: Network Topology
description: Complete network layout including IPs, hostnames, 40G interconnect, and access methods across all devices.
tags: [network, topology, ip, ssh, tailscale, 40g, mellanox]
timestamp: 2026-06-23T00:00:00Z
---

# Network Topology

## IP Assignments

| Device | Local IP | 40G Link | Tailscale | SSH Alias | User |
|--------|----------|----------|-----------|-----------|------|
| PVE (Proxmox) | 192.168.12.132 | 10.10.10.1/30 | tail9fd7ba.ts.net | `ssh pve` | root |
| pve2 (DL360 Gen9) | 192.168.12.50 | 10.10.10.2/30 | — | `ssh pve2` | root |
| DL360 iLO 4 | 192.168.12.100 | — | — | Web/SSH GUI | admin |
| Q1900M | 192.168.12.208 | — | tail9fd7ba.ts.net | `ssh q1900m` | michael |
| Z390 | 192.168.12.246 | — | tail9fd7ba.ts.net | `ssh z390` | michael-rich |
| Compaq 8200 | 192.168.12.163 | — | tail9fd7ba.ts.net | `ssh 8200` | Michael |
| Moto Root | 100.97.184.43:8022 | — | tail9fd7ba.ts.net | `ssh moto-root` | u0_a394 |
| Moto G Power | 100.108.119.78:8022 | — | tail9fd7ba.ts.net | `ssh moto-g-power` | u0_a394 |
| Laptop | 192.168.12.240 | — | tail9fd7ba.ts.net | `ssh laptop` | michael-rich |
| VM100 (Ubuntu) | 100.72.54.55 | — | tail9fd7ba.ts.net | `ssh vm100` | mike |

## 40G Interconnect

Direct-attach Mellanox ConnectX-3 Pro link between PVE and pve2:

| Side | Server | Interface | IP | MTU |
|------|--------|----------|-----|-----|
| A | pve (main) | eno49d1 | 10.10.10.1/30 | 9000 |
| B | pve2 (DL360) | enp4s0 | 10.10.10.2/30 | 9000 |

- **Speed:** 40 Gbps (40000baseCR4/Full), ~26 Gbps real-world throughput
- **Latency:** ~0.2ms
- **Connection:** Direct-attach copper cable (no switch)
- **Fix:** mlx4_core port_type_array=2,2 forces Ethernet mode on both ports

## SSH Config
Located at `/root/.ssh/config` on the Hermes coder container.

### Important SSH Notes
- pve2: Only reachable via 40G link (10.10.10.2) — vmbr0 (192.168.12.50) is currently disconnected
- Q1900M: Tailscale SSH intercepts on tailscale IPs — use local IP for direct SSH
- Moto Root/G Power: SSH routed through PVE (port forwarding)
- 8200: Windows machine running mpSSH_0.2.1

## Network Services
| Service | Host | Port |
|---------|------|------|
| Proxmox API (pve) | pve:8006 | HTTPS |
| Proxmox API (pve2) | pve2:8006 | HTTPS |
| iLO 4 (pve2) | 192.168.12.100 | HTTPS/SSH |
| Samba (q1900) | q1900m:445 | SMB |
| NFS backups | q1900m:/srv/backups | NFS v3/4 |
| NFS isos | q1900m:/srv/isos | NFS v3/4 |
| K3s API | pve:6443 | HTTPS |
