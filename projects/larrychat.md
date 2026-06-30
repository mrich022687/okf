---
type: Project
title: LarryChat — Self-Hosted Team Chat Application
description: Android + FastAPI real-time chat app with WebSocket, voice messages, and agent integration
tags: [larrychat, android, fastapi, websocket, chat, agents, voice]
timestamp: 2026-06-30T04:00:00Z
---

# LarryChat

## Overview

Self-hosted team chat application with real-time messaging, voice message support, and AI agent integration. Built on FastAPI + SQLite backend with a Kotlin/Compose Android client.

**Status:** v3 deployed and working. Currently shut down to conserve resources.

## Architecture

```
Phone (Android/Kotlin) <-- REST + WebSocket --> PVE:8900 (FastAPI/SQLite) <-- Agent API --> Hermes agents
```

## Backend

- **Server:** `/opt/chat-server/server_v3.py` on pve (192.168.12.132:8900)
- **Framework:** FastAPI + uvicorn
- **Database:** SQLite WAL mode at `/opt/chat-server/chat_v3.db`
- **Service:** systemd unit `larrychat-v3.service`
- **Venue:** `/opt/chat-server/venv/`

### API Endpoints

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `/api/login` | POST | User login → session token |
| `/api/send` | POST | Send text message (human) |
| `/api/messages` | GET | Fetch conversation messages |
| `/api/conversations` | GET | List user's conversations |
| `/api/messages/read` | POST | Mark messages as read |
| `/api/voice/send` | POST | Upload voice message (Opus) |
| `/api/voice/{filename}` | GET | Download voice audio |
| `/api/agent/send` | POST | Agent sends message (uses `key` auth) |
| `/api/agent/pending` | GET | Get unread human messages for agents |
| `/ws/{conv_id}` | WebSocket | Real-time message push |

### Conversations
- `team` — General team chat (Michael, Linda, Larry)
- `dm_michael_larry` — Direct: Michael ↔ Larry
- `dm_michael_linda` — Direct: Michael ↔ Linda
- `dm_linda_larry` — Direct: Linda ↔ Larry

### Agent Auth
- **Larry:** `larry-agent-key-2026`
- **Linda:** `linda-agent-key-2026`
- Agents use `/api/agent/send` with `key` field (NOT `/api/send` with `token`)

### Read Receipts
- `read` flag: 0=unread, 1=read
- `/api/messages/read` marks messages read in bulk
- Agent messages arrive with `read=1` by default
- `/api/agent/pending?key=...&mark_read=true` claims messages atomically

## Android Client

- **Location:** `/home/android/larrychat-app/` on pve
- **Language:** Kotlin + Jetpack Compose + Material 3
- **APK:** `app/build/outputs/apk/debug/app-debug.apk` (~16MB)
- **Target:** Moto Root (100.97.184.43, ADB ZY22X7M87D)

### Components
| File | Purpose |
|------|---------|
| `ApiClient.kt` | REST API client |
| `WebSocketManager.kt` | Real-time push via WebSocket |
| `MainActivity.kt` | Entry point, navigation |
| `AuthScreen.kt` | Login with configurable URL |
| `ConversationListScreen.kt` | List with unread badges |
| `ChatScreen.kt` | Message bubbles, input, WebSocket |
| `VoiceRecorder.kt` | Opus recording via MediaRecorder |

### Features
- Configurable server URL (default: `100.66.74.76:8900`)
- Real-time messaging via WebSocket
- Voice message recording (Opus) and upload
- Read receipts (✓ single, ✓✓ double)
- Conversation list with unread badges
- Settings: Voice mode selector, TTS toggle

## Agent Integration

### Cronjob Architecture
- **Job:** `c036be54be4b` — polls every 60s for pending human messages
- **Script:** `agent_check_larrychat.sh` — fetches pending, marks read
- **Pattern:** Script output injected as context → agent responds via `/api/agent/send`
- **Pitfall (fixed):** Prompt must reference injected output, NOT re-run script

### Webhook Push
- Human messages touch `/tmp/larrychat_webhook_trigger` on pve
- Agents can watch this file as push notification

## Voice Messages

### Current: Record-then-Send
- Android: MediaRecorder → Opus → POST `/api/voice/send`
- Server: stores in `/opt/chat-server/audio/vm_<uuid>.opus`
- Playback: GET `/api/voice/{filename}` → audio/opus

### Future: Streaming
- WebSocket STT → LLM → TTS pipeline
- Requires VM100 voice pipeline (offline)
- TTS: server XTTS-v2 or phone built-in

## Resource Impact

| Component | CPU | Memory | Notes |
|-----------|-----|--------|-------|
| `larrychat-v3` | <1% | ~33MB | FastAPI single worker |
| Agent cronjob | LLM tokens | ~0 | Per-message API call |
| Android app | Phone CPU | ~100MB | Only when open |

## Shutdown/Startup

```bash
# Start
ssh pve "systemctl start larrychat-v3"

# Stop
ssh pve "systemctl stop larrychat-v3"

# Enable at boot
ssh pve "systemctl enable larrychat-v3"

# Disable at boot
ssh pve "systemctl disable larrychat-v3"
```

## Known Issues

1. **Agent cronjob prompt bug (FIXED):** Telling agent to re-run fetch script consumed messages silently.
2. **Conversation routing:** Responding in wrong conversation causes invisible replies.
3. **Voice pipeline offline:** VM100 not running — streaming unavailable.
4. **Model sensitivity:** Flash adequate for chat; V4 Pro 3× cost.

## Version History

| Version | Date | Changes |
|---------|------|---------|
| v1 | 2026-06-26 | Flask + polling watcher |
| v2 | 2026-06-27 | WebSocket added, broken polling |
| v3 | 2026-06-29 | Full FastAPI rewrite, agent API, voice, working cronjob |
