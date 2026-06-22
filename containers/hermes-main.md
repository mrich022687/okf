---
type: Container
title: Hermes Main Profile Container
description: Default Hermes profile container (separate from coder profile). Runs on a different session.
tags: [hermes, container, default, profile]
timestamp: 2026-06-21T18:15:00Z
---

# Hermes Main Profile

## Overview
The default Hermes profile — runs from a different shell session than the coder profile.

- **Data location:** `/root/.hermes/` (skills, plugins, cron, memories)
- **Config backup:** Included in the config-backup-20260615 tarball

## Cross-Profile Rules
The coder profile should NOT modify the main profile's skills/plugins/cron/memories unless explicitly directed by the user.
