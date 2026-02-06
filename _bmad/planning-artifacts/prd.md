---
stepsCompleted: ['step-01-init', 'step-02-discovery', 'step-03-success']
inputDocuments:
  - '_bmad/planning-artifacts/product-brief-WardenWeb-2026-02-05.md'
  - '_bmad/brainstorming/brainstorming-session-2026-02-05.md'
workflowType: 'prd'
documentCounts:
  briefs: 1
  research: 0
  brainstorming: 1
  projectDocs: 0
classification:
  projectType: 'web_app'
  domain: 'general'
  complexity: 'low-medium'
  projectContext: 'greenfield'
date: '2026-02-05'
author: 'Developer'
---

# Product Requirements Document - WardenWeb

**Author:** Developer
**Date:** 2026-02-05

## Success Criteria

### User Success

| Outcome | Metric | Target |
|---------|--------|--------|
| Smooth subscription | Checkout completion without support | 100% of first 20 coaches |
| Self-service account management | Users can upgrade/cancel/redeem without help | No support tickets for standard flows |
| Clear billing visibility | Users find payment info in dashboard | Accessible in 1 click from dashboard |

### Business Success

| Metric | Target | Timeframe |
|--------|--------|-----------|
| Paying coaches | 20 | 3 months |
| Monthly churn | < 15% | Ongoing |
| Coupon → Retained | ≥ 10% | After trial period ends |
| Monthly → Yearly upgrade | Track (no target for MVP) | Ongoing |

### Technical Success

| Requirement | Success Criteria |
|-------------|------------------|
| Webhook reliability | `invoice.paid` and `customer.subscription.deleted` processed without failures |
| Auth stability | Google Sign-In + Email/Password work consistently across browsers |
| Data integrity | Firestore `users/{uid}/subscription` always reflects Stripe state |
| Payment sync | No orphan subscriptions (Stripe active but DB says inactive, or vice versa) |

### Measurable Outcomes

**MVP is successful when:**
- First 20 coaches subscribe and manage accounts without support intervention
- ≥ 10% of coupon users convert to paid after trial
- Zero critical payment/auth failures in first 30 days
- Stripe ↔ Firestore subscription state stays synchronized

## Product Scope

### MVP - Minimum Viable Product

| Feature | Description |
|---------|-------------|
| Landing Page | Value proposition, app description, CTA to pricing |
| Pricing + Checkout | Single-page: €7.99/mo or €79.90/yr, Firebase auth, Stripe payment |
| Account Dashboard | Email, next payment date, history, upgrade/cancel, redeem coupon |
| Privacy Policy | Legal template |
| Terms of Service | Legal template |

### Growth Features (Post-MVP)

| Feature | Value |
|---------|-------|
| Discord OAuth | Meet users where they are (EVA community on Discord) |
| Coupon Admin UI | Self-service batch generation for campaigns |
| Advanced Analytics | Custom funnel dashboard beyond Firebase Analytics |

### Vision (Future)

| Feature | Value |
|---------|-------|
| Referral System | Coaches invite coaches, earn free months |
| Team Subscriptions | Coach pays for whole team at discount |
| Localization | Full French UI (beyond tagline) |
