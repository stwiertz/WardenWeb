---
stepsCompleted: [1, 2, 3]
inputDocuments:
  - '_bmad/planning-artifacts/prd.md'
  - '_bmad/planning-artifacts/product-brief-WardenWeb-2026-02-05.md'
  - '_bmad/brainstorming/brainstorming-session-2026-02-05.md'
  - '_bmad/planning-artifacts/prd-validation-report.md'
workflowType: 'architecture'
project_name: 'WardenWeb'
user_name: 'Developer'
date: '2026-02-06'
---

# Architecture Decision Document

_This document builds collaboratively through step-by-step discovery. Sections are appended as we work through each architectural decision together._

## Project Context Analysis

### Requirements Overview

**Functional Requirements:**
34 FRs across 8 categories. The system is primarily a service orchestration layer:
- Authentication (FR1-4): Firebase Auth with Google + Email/Password
- Discovery (FR5-7): Static/SSR landing page with CTAs
- Checkout (FR8-12): Stripe-powered subscription with coupon support
- Dashboard (FR13-20): Read subscription state from Firestore, trigger Stripe actions
- Webhooks (FR21-25): Server-side Stripe event processing → Firestore state updates
- Legal (FR26-29): Static pages + cookie consent gating
- Account Deletion (FR30-31): MVP via support email, cascading delete across services
- Security (FR32-34): Firestore rules, auth validation, secret management

**Non-Functional Requirements:**
16 NFRs across 4 categories:
- Performance: LCP < 2.5s, FID < 100ms, CLS < 0.1, dashboard < 2s, checkout < 30s
- Security: HTTPS, HttpOnly cookies, webhook signature verification, PCI via Stripe
- Integration: Retry logic for Stripe API (3 attempts), idempotent webhook processing, 200 response within 5s
- Data Integrity: Firestore-Stripe sync within 30s, at-least-once webhook delivery with idempotency

**Scale & Complexity:**

- Primary domain: Full-stack web application (Next.js)
- Complexity level: Low-Medium
- Estimated architectural components: ~8 (landing, pricing/checkout, auth, dashboard, webhooks API, Firestore data layer, Stripe integration layer, legal pages)

### Technical Constraints & Dependencies

- **Firebase project shared** between mobile app and web portal (same auth, same Firestore)
- **Stripe as payment processor** — all card handling delegated, PCI compliance via Stripe
- **Firestore europe-west region** locked for GDPR compliance
- **No WebSocket/real-time** — webhook-driven async state updates
- **Mobile-first responsive** — 320px-768px primary, progressive enhancement to desktop
- **Browser support** — Last 2 versions of Chrome, Firefox, Safari, Edge (no IE11)
- **Cookie consent required** before loading Firebase Analytics

### Cross-Cutting Concerns Identified

- **Authentication**: Protects dashboard, checkout, API routes. Firebase Auth token validation on every protected request.
- **Stripe-Firestore Sync**: The most critical architectural concern. Webhooks must reliably update Firestore to reflect Stripe state. Idempotency and signature verification are mandatory.
- **GDPR Compliance**: Affects data storage location, cookie consent flow, account deletion cascade, and privacy policy content.
- **Error Handling**: Payment failures, auth failures, and webhook processing failures all need distinct handling patterns visible to the user.
- **Environment Configuration**: Stripe keys, Firebase config, webhook secrets must be managed securely across development and production.

## Starter Template Evaluation

### Primary Technology Domain

Full-stack web application based on project requirements analysis. Next.js provides React frontend + API routes for Stripe webhooks in a single deployment.

### Starter Options Considered

**Option 1: `create-next-app` (Official Next.js CLI)**
- Next.js 16.1.x (latest stable, Feb 2026)
- Defaults: TypeScript, Tailwind CSS, ESLint, App Router, Turbopack
- Add Firebase/Stripe manually
- Pros: Minimal, standard conventions, zero cost, AI-agent-friendly
- Cons: No pre-wired integrations

**Option 2: SaaS Boilerplates (Divjoy, Makerkit, supastarter)**
- Pre-built Firebase + Stripe + auth flows
- Pros: Faster initial integration setup
- Cons: Paid ($100-400+), excessive features (team billing, admin, email), opinionated non-standard patterns, harder for AI agents to follow

### Selected Starter: create-next-app (Next.js 16.1.x)

**Rationale for Selection:**
- Project complexity (low-medium) doesn't justify boilerplate overhead
- Standard Next.js conventions ensure AI agent consistency
- Firebase SDK and Stripe SDK are well-documented for manual integration
- No unnecessary features bloating the codebase
- Free, always up-to-date, community-supported

**Initialization Command:**

```bash
npx create-next-app@latest wardenweb --typescript --tailwind --eslint --app --src-dir --turbopack --import-alias "@/*"
```

**Architectural Decisions Provided by Starter:**

**Language & Runtime:**
TypeScript (strict mode), Node.js runtime for API routes

**Styling Solution:**
Tailwind CSS v4 (utility-first, configured out of the box)

**Build Tooling:**
Turbopack (stable default bundler in Next.js 16), optimized production builds

**Testing Framework:**
Not included — will be decided in Step 4

**Code Organization:**
App Router with `src/` directory, `@/*` import alias

**Development Experience:**
Turbopack hot reload, TypeScript type checking, ESLint linting

**Note:** Project initialization using this command should be the first implementation story.
