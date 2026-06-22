---
type: Project
title: Voice Pipeline
description: Self-hosted voice AI pipeline — Speech-to-Text → LLM → Text-to-Speech. Runs on VM100 with GPU acceleration.
tags: [voice, stt, tts, llm, whisper, xtts, llama]
timestamp: 2026-06-21T18:15:00Z
---

# Voice Pipeline

## Overview
A fully self-hosted voice AI pipeline running on an Ubuntu VM. Michael speaks into the phone, speech goes through STT → LLM → TTS, and the response plays back on the phone.

## Architecture
```
Phone → STT (faster-whisper, GPU 0, P4) → LLM (llama.cpp, CPU, port 8080) → TTS (XTTS v2, GPU 1, P4) → Phone
```

### Components
- **STT Server:** FastAPI on VM100 (port 8000), uses faster-whisper large-v3 on GPU 0
- **LLM Server:** llama.cpp (systemd `llm-server.service`, port 8080), CPU-only inference
- **TTS Worker:** `~/tts_worker.py` (Python 3.11, `tts-env` venv), XTTS v2 on GPU 1
- **Voice Server:** FastAPI orchestrator (systemd `voice-server.service`, port 8000)

## GPU Setup
| GPU | Model | Task |
|-----|-------|------|
| GPU 0 | Tesla P4 | STT (faster-whisper) |
| GPU 1 | Tesla P4 | TTS (XTTS v2) |

## Known Fixes
- **PyTorch 2.6 XTTS fix:** Monkey-patch `torch.load` with `weights_only=False` before importing TTS.api
- **Block patch in voice_server.py:** The fix is written into the server script so it persists across restarts

## Server Info
- **VM100:** Ubuntu VM, IP 100.72.54.55 (via Tailscale), SSH alias `ssh vm100`
- **LLM Server:** systemd unit `llm-server.service`
- **Voice Server:** systemd unit `voice-server.service`

## Status
✅ Operational as of last testing
