---
type: Project
title: K3s Cluster
description: Lightweight Kubernetes cluster running on Proxmox LXC containers.
tags: [k3s, kubernetes, cluster, containers]
timestamp: 2026-06-23T00:00:00Z
---

# K3s Cluster

## Current Status
- **Server:** CT 104 (pve) — currently stopped
- **API:** pve:6443 (HTTPS)
- **Network:** Flannel CNI on 10.42.0.0/16

## Nodes
| Node | CT/VM | Status | Spec |
|------|-------|--------|------|
| k3s-server | CT 104 | Stopped | 2C/8G |
| (workers) | — | — | Not yet provisioned |

## Notes
- K3s installed via official install script
- Uses embedded etcd (or SQLite for single-node)
- Flannel CNI with host-gw backend
- Traefik ingress controller (default)
