---
stepsCompleted: [1, 2]
inputDocuments: ['_bmad/brainstorming/brainstorming-session-2026-02-05.md']
date: 2026-02-05
author: Developer
---

# Product Brief: WardenWeb

## Executive Summary

Warden is a mobile video review app built specifically for EVA After-h coaches that transforms paid training sessions into accelerated learning opportunities — like a "Double XP" bonus for team development. By auto-slicing hour-long sessions into map-based episodes, offering a unique tactical minimap view, and enabling voice-first commentary from the couch, Warden eliminates the friction that causes coaches to abandon review entirely.

WardenWeb serves as the subscription management portal ("Reader App" model), enabling coaches to subscribe via Stripe while bypassing app store fees, and providing a professional web presence for Discord-based marketing and word-of-mouth discovery.

**Tagline:** *Progresser plus vite en investissant moins de temps* (Progress faster by investing less time)

---

## Core Vision

### Problem Statement

EVA After-h sessions are paid experiences, yet without structured review, teams "play" without truly "training" — relying on raw experience to progress, which is slow and frustrating. The same mistakes repeat match after match.

### Problem Impact

Coaches currently face an impossible workflow:
- Juggling multiple tools (video player + notes + editing software)
- Generic editing software is complex and overkill for the task
- YouTube clips lack tactical context (no minimap view)
- EVA Battle Plan has upload delays, token costs, and underutilized stats
- **The review process is so heavy that coaches abandon it entirely**

### Why Existing Solutions Fall Short

EVA Battle Plan (primary competitor) requires cloud uploads with delays, charges tokens per analysis, and provides stats that go underutilized. Generic video editing tools are overkill for a coach who just wants to create quick clips with commentary after work.

### Proposed Solution

Warden processes replays entirely on-device with EVA-specific optimizations:
- **Auto-slice by map** — Black screen detection + template matching splits 1h20 sessions in under 2 minutes
- **Unique Minimap View** — Toggle instantly between POV and tactical overhead (no competitor offers this)
- **Voice-first commentary** — Record before, during, and after clips from the couch
- **Standalone clip export** — Players receive MP4s via Discord/WhatsApp without installing any app
- **100% on-device** — No uploads, no cloud costs, works completely offline

### Key Differentiators

| Feature | Why It Matters |
|---------|----------------|
| EVA-Specialized | Pre-configured templates and detection for EVA After-h |
| Minimap Mode | Unique tactical overhead view — no competitor offers this |
| 100% On-Device | No uploads, fixed costs, works offline |
| Voice-First | Minimal friction for a tired coach on the couch |
| Standalone Export | Players don't need the app to receive feedback |
