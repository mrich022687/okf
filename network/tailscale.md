---
type: Network
title: Tailscale Network
description: Tailscale tailnet configuration for secure device access across the homelab.
tags: [tailscale, vpn, mesh, network]
timestamp: 2026-06-21T18:15:00Z
---

# Tailscale Network

## Tailnet
- **Name:** tail9fd7ba.ts.net
- **Type:** Tailscale tailnet (mesh VPN)

## Connected Devices
All devices in the homelab are connected via Tailscale, providing encrypted mesh networking.

## Notes
- Tailscale SSH intercepts on the Q1900M — when connecting via Tailscale IP, SSH goes through Tailscale's SSH server. Use the local IP (192.168.12.208) for direct SSH access.
- Managed via [tailscale-network-management](skills/tailscale-network-management.md) skill.
