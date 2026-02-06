---
stepsCompleted: [1, 2, 3, 4, 5]
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

## Core Architectural Decisions

### Decision Priority Analysis

**Critical Decisions (Block Implementation):**
- Data validation: Zod
- Auth session management: Server-side session cookies
- Hosting: Vercel
- Component library: shadcn/ui

**Important Decisions (Shape Architecture):**
- Caching strategy: Next.js built-in for static pages
- State management: React Context for auth
- Testing: Vitest + Playwright
- CI/CD: GitHub Actions + Vercel

**Deferred Decisions (Post-MVP):**
- CDN/edge caching optimization
- Monitoring/alerting (Vercel Analytics sufficient for MVP)
- Advanced logging (console logging sufficient for MVP)

### Data Architecture

**Database:** Firestore (Firebase JS SDK v12.9.x)
- Region: europe-west (GDPR)
- Collections: `users/{uid}`, `coupon_batches/{batchId}`
- Security rules: users read/write only own document

**Data Validation:** Zod v3.x
- Runtime validation on all webhook payloads (security-critical)
- Shared schemas between client and server
- TypeScript type inference from schemas
- Rationale: Webhook payloads from Stripe must be validated at runtime, not just at type level

**Caching Strategy:** Next.js built-in caching only
- Static pages (landing, pricing, legal): `cacheLife` with revalidation
- Dashboard: Always fresh reads from Firestore (subscription state changes via webhooks)
- No external caching layer needed at MVP scale

### Authentication & Security

**Session Management:** Server-side session cookies
- Firebase Auth `createSessionCookie()` after client-side sign-in
- HttpOnly, Secure, SameSite=Lax cookies
- 7-day expiry with activity-based refresh (per PRD: auto-logout after 7 days inactivity)
- Rationale: PRD requires HttpOnly cookies; server-side sessions enable SSR auth checks

**Route Protection:** Next.js middleware + per-route API checks
- `middleware.ts`: Validates session cookie, redirects unauthenticated users from `/dashboard/*`
- API routes: Validate session cookie independently (defense in depth)
- Public routes: Landing, pricing, legal pages (no auth required)

**Stripe Webhook Security:** Signature verification (per PRD FR24)
- `stripe.webhooks.constructEvent()` with webhook secret
- Reject unsigned or invalid payloads before processing

### API & Communication Patterns

**API Routes:** Next.js App Router Route Handlers
- `/api/webhooks/stripe` — Stripe webhook endpoint
- `/api/auth/*` — Session management (create/destroy session cookies)
- `/api/subscription/*` — Subscription actions (upgrade, cancel)

**Webhook Idempotency:** Dual strategy
- Event ID deduplication: Store processed Stripe event IDs in Firestore, skip duplicates
- Firestore transactions: Check current state before updating (handles race conditions)
- Rationale: At-least-once delivery requires both dedup and transactional state updates

**Error Handling:** Three-layer approach
- API routes: Structured `{error: {code, message}}` JSON responses
- React error boundaries: Catch unexpected rendering failures
- User-facing error states: Specific components for payment failed, auth failed, generic error
- Stripe webhook errors: Log and return 200 to prevent Stripe retries on permanent failures

### Frontend Architecture

**State Management:** React Context (minimal)
- `AuthContext`: Firebase Auth state (user, loading, error)
- No global store needed — subscription data fetched per-page from Firestore
- Rationale: Portal has minimal client state; adding Zustand/React Query is unnecessary

**Component Library:** shadcn/ui
- Radix UI primitives styled with Tailwind CSS
- Copy-paste ownership (no npm dependency)
- Accessible by default (WCAG 2.1 Level A per PRD)
- Components needed: Button, Card, Dialog, Form, Input, Badge, Alert, Skeleton

**Form Handling:** React Hook Form + Zod
- Checkout form: Plan selection + coupon code
- Auth forms: Email/password sign-in/sign-up
- Zod schemas shared with server-side validation

**Routing Strategy:** Next.js App Router
- `/` — Landing page (SSR, cached)
- `/pricing` — Pricing + checkout (client-side interactivity)
- `/dashboard` — Account dashboard (protected, fresh data)
- `/privacy` — Privacy policy (static)
- `/terms` — Terms of service (static)

### Infrastructure & Deployment

**Hosting:** Vercel
- Native Next.js support, zero-config deployment
- API routes → serverless functions (auto-scaled)
- Edge network for static assets
- Preview deployments on PRs
- Rationale: Best-in-class Next.js hosting, free tier sufficient for MVP

**Testing:** Vitest 4.x + Playwright
- Vitest: Unit tests for utilities, Zod schemas, webhook handlers
- React Testing Library: Component tests
- Playwright: E2E tests for checkout flow, auth flow, dashboard actions
- Rationale: Vitest is Next.js recommended; Playwright covers critical payment flows

**CI/CD:** GitHub Actions + Vercel
- GitHub Actions: Run linting, type checking, unit tests, E2E tests on PR
- Vercel: Automatic preview deployments on PR, production deploy on main merge
- Rationale: Separation of concerns — GH Actions validates code quality, Vercel handles deployment

**Environment Management:**
- `.env.local` — Local development secrets
- `.env.example` — Template with placeholder values (committed)
- Vercel Environment Variables — Production/preview secrets
- Required vars: `STRIPE_SECRET_KEY`, `STRIPE_WEBHOOK_SECRET`, `NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY`, `NEXT_PUBLIC_FIREBASE_*` config values

### Decision Impact Analysis

**Implementation Sequence:**
1. Project initialization (`create-next-app`)
2. Firebase Auth + session cookie setup
3. Firestore data layer + security rules
4. Stripe integration + webhook endpoint
5. UI components (shadcn/ui setup)
6. Page implementations (landing → pricing → dashboard)
7. Testing setup (Vitest + Playwright)
8. CI/CD pipeline (GitHub Actions)
9. Vercel deployment

**Cross-Component Dependencies:**
- Auth decisions affect: middleware, API routes, dashboard, checkout
- Stripe decisions affect: webhook handler, dashboard state display, checkout flow
- Zod schemas affect: webhook validation, form validation, Firestore writes
- shadcn/ui affects: all page implementations

## Implementation Patterns & Consistency Rules

### Pattern Categories Defined

**Critical Conflict Points Identified:** 5 categories where AI agents could make different choices — naming, structure, formats, communication, and process patterns.

### Naming Patterns

**Firestore Naming Conventions:**
- Collection names: `snake_case` plural → `users`, `coupon_batches`
- Document fields: `snake_case` → `current_period_end`, `redeemed_batches`
- Rationale: Matches Stripe webhook field naming (Stripe uses snake_case), avoids constant conversion

**API Naming Conventions:**
- Route handlers: kebab-case directories → `/api/webhooks/stripe`, `/api/auth/session`
- Query parameters: `camelCase` → `?batchId=abc`
- JSON response fields: `camelCase` → `{currentPlan, nextPaymentDate}`

**Code Naming Conventions:**
- Components: `PascalCase` files → `PricingCard.tsx`, `DashboardLayout.tsx`
- Utilities/lib: `camelCase` files → `stripeClient.ts`, `firebaseAdmin.ts`
- Hooks: `camelCase` with `use` prefix → `useAuth.ts`, `useSubscription.ts`
- Types: `PascalCase` → `User`, `Subscription`, `WebhookEvent`
- Constants: `SCREAMING_SNAKE_CASE` → `STRIPE_PLANS`, `AUTH_COOKIE_NAME`
- CSS classes: Tailwind utilities only (no custom CSS class names)

### Structure Patterns

**Project Organization:**
- Components organized by feature, not by type
- `src/components/ui/` for shadcn/ui base components
- `src/components/auth/` for auth-specific components
- `src/components/checkout/` for checkout components
- `src/components/dashboard/` for dashboard components
- `src/components/layout/` for shared layout (Header, Footer, CookieBanner)

**File Structure Patterns:**
- Tests co-located next to source: `src/lib/__tests__/stripe.test.ts`
- E2E tests in top-level `e2e/` directory
- Lib organized by service: `src/lib/firebase/`, `src/lib/stripe/`, `src/lib/schemas/`

### Format Patterns

**API Response Formats:**
```typescript
// Success
{ data: { subscription: {...} } }

// Error
{ error: { code: "SUBSCRIPTION_NOT_FOUND", message: "No active subscription" } }
```

**Data Exchange Formats:**
- Firestore stores `snake_case` (matches Stripe webhook payloads)
- Frontend uses `camelCase` (TypeScript convention)
- Conversion happens in data layer (`src/lib/firebase/`) using Zod `.transform()`
- Dates in Firestore: `Timestamp` objects
- Dates from Stripe: Unix timestamps (seconds)
- Dates in JSON responses: ISO 8601 strings
- Dates in UI: `Intl.DateTimeFormat` (no date library needed)

### Communication Patterns

**Auth Flow Pattern:**
1. Client: Firebase `signInWithPopup()` or `signInWithEmailAndPassword()`
2. Client gets ID token: `user.getIdToken()`
3. POST to `/api/auth/session` with ID token
4. Server creates session cookie via `createSessionCookie()`, returns success
5. Client redirects to dashboard

**Stripe Webhook Event Flow:**
1. Stripe POSTs to `/api/webhooks/stripe`
2. Verify signature → parse event
3. Check event ID dedup in Firestore
4. Route by event type (`invoice.paid`, `customer.subscription.deleted`, `invoice.payment_failed`)
5. Firestore transaction to update `users/{uid}` subscription fields
6. Return 200

**Loading State Pattern:**
- Component-level: `isLoading` boolean + Skeleton component from shadcn/ui
- Page-level: Next.js `loading.tsx` files
- Auth loading: `AuthContext` provides `{user, loading, error}`

### Process Patterns

**Error Handling Patterns:**

| Layer | Pattern |
|-------|---------|
| API routes | Try/catch → structured JSON error response |
| Webhook handler | Try/catch → log error, return 200 (prevent Stripe retries) |
| React components | Error boundaries around feature sections |
| Forms | React Hook Form validation errors → inline display |
| Auth | Redirect to sign-in on 401, toast on other errors |

**Environment Variable Access:**
- Server-only: `process.env.STRIPE_SECRET_KEY` (no `NEXT_PUBLIC_` prefix)
- Client-safe: `process.env.NEXT_PUBLIC_FIREBASE_API_KEY`
- Rule: Server secrets must never appear in client bundles

**Import Order Convention:**
1. React/Next.js imports
2. Third-party libraries
3. `@/` project imports
4. Relative imports
5. Type-only imports last

### Enforcement Guidelines

**All AI Agents MUST:**
- Follow naming conventions exactly (snake_case in Firestore, camelCase in code, PascalCase for components)
- Use Zod schemas for all data flowing between boundaries (webhook → server, server → client)
- Place components in feature directories, not flat in `components/`
- Use the structured API response format for all route handlers
- Never import server-only modules in client components

**Anti-Patterns:**
- Mixing `snake_case` and `camelCase` in Firestore documents
- Using `any` type instead of Zod-inferred types
- Placing API keys or secrets in client-accessible files
- Creating global state for data that should be fetched per-page
- Importing `firebase-admin` in client components
