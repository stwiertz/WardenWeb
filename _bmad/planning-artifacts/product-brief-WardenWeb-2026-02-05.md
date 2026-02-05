---
stepsCompleted: [1, 2, 3]
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

---

## Target Users

### Primary Users

#### The Coach — Thomas, 26

**Profile:**
Thomas is an EVA After-h coach who plays weekly sessions with his team. After work, he reviews the recorded gameplay from his couch, wanting to create quick clips with commentary to share with his players. He's tired of juggling generic video editors and abandoned complex solutions like EVA Battle Plan.

**Context & Motivation:**
- Reviews sessions after work, typically tired and relaxing
- Wants lightweight, EVA-specific workflow with minimal friction
- Needs to create clips quickly and share via Discord/WhatsApp
- Values his time — won't tolerate heavy setup or per-use costs

**Current Pain:**
- Generic editing tools are overkill
- EVA Battle Plan requires uploads, tokens, waiting
- Process is so heavy he often skips review entirely

**Success Vision:**
"I can slice my 1h20 session into maps, jump to the round we lost, record my voice-over, and send a clip to the team — all in under 10 minutes from my couch."

---

### Secondary Users

#### Passive Player

**Profile:**
Team members who receive clips from their coach. They watch MP4s shared via Discord/WhatsApp without installing any app.

**WardenWeb Interaction:**
- May visit the landing page out of curiosity
- Sees the value proposition and considers becoming an Active Player
- **Conversion opportunity** — passive recipients can become paying subscribers

#### Active Player

**Profile:**
Players who want to self-analyze their gameplay and develop their own coaching skills. May have converted from Passive Player after seeing valuable clips from their coach.

**WardenWeb Interaction:**
- Subscribes individually (no team billing)
- Manages own account and payment
- Uses the minimap view to develop tactical understanding

---

### User Journeys (WardenWeb)

#### Coach Discovery & Subscription Journey

```
Discord tip / Word-of-mouth
        ↓
Receives coupon link
        ↓
Lands on WardenWeb pricing page
        ↓
Creates account (Google / Email)
        ↓
Enters payment info ("You won't be charged until [date]")
        ↓
Downloads app, tries Warden with coupon period
        ↓
Finds value → Subscription continues
        ↓
Returns to WardenWeb for: billing, upgrade to yearly, redeem new coupon, cancel
```

#### Passive → Active Conversion Journey

```
Receives clip from coach (MP4 via Discord)
        ↓
Impressed by quality / minimap view
        ↓
Visits WardenWeb landing page (curiosity)
        ↓
Understands value proposition
        ↓
Decides to analyze own sessions
        ↓
Subscribes as Active Player
```
