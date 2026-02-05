---
stepsCompleted: [1, 2, 3, 4]
inputDocuments: []
session_topic: 'WardenWeb - Mobile App Subscription & Account Management Portal'
session_goals: 'Design web interface for account creation, Stripe subscription (€7.99/mo, €79.90/yr), Firebase auth, landing page, pricing page, account management, coupon system'
selected_approach: 'ai-recommended'
techniques_used: ['Role Playing', 'SCAMPER Method', 'Constraint Mapping']
ideas_generated: 18
context_file: ''
session_active: false
workflow_completed: true
---

# Brainstorming Session Results

**Facilitator:** Developer
**Date:** 2026-02-05

## Session Overview

**Topic:** WardenWeb - Mobile App Subscription & Account Management Portal

**Goals:**
- Design complete web interface structure and user flows
- Plan landing page, pricing page, and account management
- Design Stripe + Firebase/Firestore integration approach
- Explore coupon/discount mechanics

**Key Requirements:**
- Monthly: €7.99/month
- Yearly: €79.90/year (2 months free)
- Firebase/Firestore authentication
- Stripe payments with coupon system
- Coupon logic: €7.99 coupon → 1 free month (monthly) / €7.99 off next year (yearly) / first month free (new subscribers)

## Technique Selection

**Approach:** AI-Recommended Techniques
**Analysis Context:** Web-based subscription portal design with focus on UX flows, Stripe/Firebase integration

**Recommended Techniques:**
1. **Role Playing** (collaborative) - Embody different user personas to understand each journey
2. **SCAMPER Method** (structured) - Systematically challenge each portal element
3. **Constraint Mapping** (deep) - Map all constraints against design decisions

---

## Technique Execution Results

### Technique 1: Role Playing

**Personas Explored:**

#### Persona 1: The Fresh Discoverer (New Subscriber)
- User taps "Subscribe" in app → arrives on web portal
- Should be auto-authenticated if they have an account
- Subscription plan pre-selected, ready to confirm
- Needs to see features unlocked by subscription

**Ideas Generated:**
- **UX-1: Seamless App-to-Web Handoff** - Users arrive authenticated via deep link/Firebase session with plan pre-selected
- **UX-2: Feature Unlock Preview** - Show clear visual list of what they unlock before paying

#### Persona 2: Gift Recipient (Early Adopter)
- Receives coupon link for 2 free months
- Different flow: "Claim your gift" vs "Buy subscription"
- Payment info required upfront for auto-renewal

**Ideas Generated:**
- **UX-3: Gift Flow with Payment Capture** - "Claim 2 free months" → Create account → Enter card with clear "You won't be charged until [date]" messaging

#### Persona 3: Existing Subscriber (Account Management)
- Needs: next payment date, email, payment history
- Actions: upgrade to yearly, redeem coupon, cancel

**Ideas Generated:**
- **UX-4: Minimal Account Dashboard** - Single-view: Email, Next payment date, Payment history, Upgrade/Cancel buttons
- **UX-5: Upgrade Nudge** - Personalized savings calculation based on actual payment history
- **UX-6: Clean Upgrade Timing** - Proration accepted for mid-cycle upgrades
- **UX-7: Dedicated Coupon Redemption** - Separate section on account page with preview before applying

#### Persona 4: Churning User (Cancellation)
- Cancel at end of billing period (keep access until then)
- Frictionless cancellation for V1
- Collect optional churn data for future optimization

**Ideas Generated:**
- **UX-8: Graceful Cancellation Flow** - One-click cancel, access until period ends, builds goodwill
- **UX-9: Churn Data Collection** - Optional "why are you leaving?" after confirmation (non-blocking)

---

### Technique 2: SCAMPER Method

**S - Substitute:**
- Explored auth alternatives: Magic links, Social login
- Decision: Google Sign-In + Email/Password (Discord deferred to V2)

**C - Combine:**
- Combined pricing page + checkout into single page
- Plan selection reveals payment form immediately

**A - Adapt:**
- Adapted Netflix/Spotify model: payment captured upfront for free trials
- Adapted plan comparison visual approach

**E - Eliminate:**
- Eliminated separate login/signup pages (auth integrated into pricing)
- Eliminated password reset complexity (Google auth handles it)
- Eliminated Discord OAuth for V1 (extra dev work)

**R - Reverse:**
- Considered payment-before-account flow
- Rejected (account needed for app sync)

**Ideas Generated:**
- **AUTH-1: Google Sign-In** - Primary social auth, native Firebase support
- **AUTH-2: Email/Password** - Traditional option for users who prefer it
- **AUTH-3: Discord (V2)** - Deferred to post-launch
- **SCAMPER-4: Single-Page Pricing + Checkout** - Reduces drop-off between steps

---

### Technique 3: Constraint Mapping

**Technical Constraints Validated:**

| Area | Constraint | Decision | Status |
|------|------------|----------|--------|
| Stripe | Proration behavior | Accept default proration | ✅ |
| Stripe | Coupon mechanics | amount_off €7.99 via promotion codes | ✅ |
| Stripe | Webhooks needed | invoice.paid, subscription.deleted | ⚠️ Build |
| Firebase | Discord OAuth | Extra work → Defer to V2 | ✅ |
| Firebase | Shared project | Web + Mobile same Firebase | ✅ |
| Firestore | Subscription state | users/{uid}/subscription | ✅ |

**Coupon System Architecture:**

- **COUPON-1: Batch-Based System** - Generate codes in named batches (e.g., "FRIENDS-AND-FAMILY")
- **COUPON-2: One-Per-Batch-Per-User** - User can only redeem one code per batch
- **COUPON-3: Time-Limited Batches** - Expiration at Firestore level + Stripe redeem_by

**Firestore Structure:**
```
coupon_batches/{batchId}
├── name: "Friends & Family"
├── discount_amount: 7.99
├── expires_at: timestamp
└── codes: ["WARDEN-FF-001", "WARDEN-FF-002", ...]

users/{uid}
├── email
├── subscription: {plan, status, current_period_end}
└── redeemed_batches: ["batch-id-1", ...]
```

---

## Idea Organization and Prioritization

### Theme 1: User Flows & Journeys

| ID | Idea | Priority |
|----|------|----------|
| UX-1 | Seamless App-to-Web Handoff | High |
| UX-2 | Feature Unlock Preview | Medium |
| UX-3 | Gift Flow with Payment Capture | High |
| UX-8 | Graceful Cancellation | High |
| UX-9 | Churn Data Collection | Medium |

### Theme 2: Account & Subscription Management

| ID | Idea | Priority |
|----|------|----------|
| UX-4 | Minimal Account Dashboard | High |
| UX-5 | Upgrade Nudge | Low |
| UX-6 | Proration for Upgrades | Medium |
| UX-7 | Dedicated Coupon Section | High |

### Theme 3: Authentication Strategy

| ID | Idea | Priority |
|----|------|----------|
| AUTH-1 | Google Sign-In | High |
| AUTH-2 | Email/Password | High |
| AUTH-3 | Discord (V2) | Deferred |

### Theme 4: Coupon System

| ID | Idea | Priority |
|----|------|----------|
| COUPON-1 | Batch-Based Coupons | High |
| COUPON-2 | One-Per-Batch-Per-User | High |
| COUPON-3 | Time-Limited Batches | High |

---

## V1 Portal Specification

```
WardenWeb Portal - V1 Architecture
══════════════════════════════════════════════════════════

PAGES
─────
├── Landing Page (app description)
├── Pricing + Checkout (single page)
│   ├── Plan selection: Monthly €7.99 / Yearly €79.90
│   ├── Auth: Google Sign-In | Email/Password
│   └── Stripe payment form
├── Account Dashboard
│   ├── Email display
│   ├── Next payment date + amount
│   ├── Payment history
│   ├── [Upgrade to Yearly] button
│   ├── [Redeem Coupon] section
│   └── [Cancel Subscription] button
├── Privacy Policy
└── Terms of Service

AUTH (Firebase)
───────────────
├── Google Sign-In (primary)
└── Email/Password (traditional)

PAYMENTS (Stripe)
─────────────────
├── Products: Monthly (€7.99), Yearly (€79.90)
├── Proration: Enabled (default)
├── Coupons: amount_off €7.99, promotion codes
└── Webhooks: invoice.paid, customer.subscription.deleted

DATA (Firestore)
────────────────
├── users/{uid}
│   ├── email
│   ├── subscription: {plan, status, current_period_end}
│   └── redeemed_batches: ["batch-id-1", ...]
│
└── coupon_batches/{batchId}
    ├── name
    ├── discount_amount
    ├── expires_at
    └── codes: [...]
```

---

## Action Plan

### Immediate Next Steps (This Week)

1. **Set up Firebase project** with Google + Email auth providers
2. **Create Stripe products** for Monthly/Yearly plans
3. **Design landing page** wireframe/content

### Short-Term (Next 2 Weeks)

4. **Build pricing + checkout page** with single-page flow
5. **Implement Stripe integration** with webhooks
6. **Create account dashboard** with subscription management

### Before Launch

7. **Set up coupon batch system** in Firestore
8. **Create legal pages** (Privacy Policy, ToS)
9. **Test app-to-web deep linking**

---

## Session Summary

**Key Achievements:**
- Defined complete V1 portal architecture with 5 pages
- Designed 3 distinct user flows (new subscriber, gift recipient, account management)
- Validated technical approach against Stripe/Firebase constraints
- Created sophisticated coupon system supporting batch management and time limits
- Simplified auth to Google + Email/Password for V1, with Discord roadmapped for V2

**Creative Breakthroughs:**
- Single-page pricing + checkout to reduce conversion drop-off
- Batch-based coupon system with one-per-batch-per-user protection
- Frictionless cancellation with optional churn data collection

**Session Statistics:**
- Total Ideas: 18 concrete decisions
- Techniques Used: 3 (Role Playing, SCAMPER, Constraint Mapping)
- Themes Identified: 4 (User Flows, Account Management, Auth, Coupons)

---

*Session completed: 2026-02-05*
