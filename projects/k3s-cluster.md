---
type: Project
title: K3s Cluster
description: Lightweight Kubernetes cluster running on Proxmox LXC containers.
tags: [k3s, kubernetes, cluster, containers]
timestamp: 2026-06-23T00:00:00Z
---

# K3s Cluster

## Current Status
- **Server:** Main PVE host (192.168.12.132), NOT CT 104
- **API:** pve:6443 (HTTPS)
- **Network:** Flannel CNI on 10.42.0.0/16
- **CPU impact:** Constant ~11% CPU baseline on main PVE from K3s server process
- **⚠️ Issue:** 14+ zombie dashboard pods causing scheduler churn — see changelog

## Nodes
| Node | Location | Status | Spec |
|------|----------|--------|------|
| k3s-server | Main PVE host | Running | (direct host install) |
| kubernetes-dashboard | Host | Degraded | 15 pod replicas, 14 dead |
| (workers) | — | — | Not yet provisioned |

## Notes
- K3s installed via official install script
- Uses embedded etcd (or SQLite for single-node)
- Flannel CNI with host-gw backend
- Traefik ingress controller (default)
