---
stepsCompleted: ['step-01-init', 'step-02-discovery', 'step-03-success', 'step-04-journeys', 'step-05-domain', 'step-06-innovation']
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

## User Journeys

### Journey 1: Coach Discovery & Subscription (Happy Path)

**Persona:** Thomas, 26 — EVA After-h coach

**Opening Scene:**
Thomas entend parler de Warden sur Discord. Un autre coach partage un lien avec un code promo pour 2 mois gratuits.

**Rising Action:**
1. Clique sur le lien → Landing page WardenWeb
2. Lit la proposition de valeur, reconnaît son problème
3. Clique "Voir les tarifs" → Pricing page
4. Choisit mensuel €7.99 (veut tester d'abord)
5. Se connecte avec Google (rapide, pas de mot de passe)
6. Entre sa carte, voit "Vous ne serez pas débité avant le [date]"
7. Confirme → Compte créé, coupon appliqué

**Climax:**
Thomas télécharge l'app, importe sa première session, exporte son premier clip en 5 minutes.

**Resolution:**
2 mois plus tard, le prélèvement passe. Thomas continue — Warden fait partie de sa routine.

**Capabilities révélées:** Landing page, Pricing display, Google Auth, Stripe Checkout, Coupon redemption, Welcome email

---

### Journey 2: Existing Subscriber — Upgrade to Yearly

**Persona:** Thomas, 3 mois plus tard

**Opening Scene:**
Thomas reçoit sa 3ème facture mensuelle. Il calcule: €7.99 × 12 = €95.88/an vs €79.90 yearly.

**Rising Action:**
1. Se connecte à WardenWeb → Dashboard
2. Voit "Économisez 2 mois en passant à l'annuel"
3. Clique "Upgrade" → Confirmation avec calcul du prorata
4. Confirme → Stripe applique le prorata automatiquement

**Resolution:**
Thomas est sur le plan annuel. Prochain paiement dans 11 mois.

**Capabilities révélées:** Dashboard, Upgrade flow, Proration display, Stripe subscription update

---

### Journey 3: Payment Failure & Recovery

**Persona:** Thomas, 8 mois plus tard — carte expirée

**Opening Scene:**
La carte de Thomas expire. Stripe tente le prélèvement → échec.

**Rising Action:**
1. Stripe envoie un email automatique "Paiement échoué"
2. Thomas se connecte à WardenWeb → Dashboard affiche "⚠️ Paiement en attente"
3. Clique "Mettre à jour ma carte" → Stripe Customer Portal
4. Met à jour sa carte → Stripe retente le paiement

**Resolution:**
Paiement réussi. Subscription status revient à "active". Accès maintenu.

**Edge case:** Si Thomas ne met pas à jour après X jours → subscription annulée automatiquement par Stripe.

**Capabilities révélées:** Subscription status display, Link to Stripe Customer Portal, Webhook handling (`invoice.payment_failed`), Grace period logic

---

### Journey 4: Cancellation Flow

**Persona:** Thomas décide d'arrêter

**Opening Scene:**
Thomas ne joue plus à EVA. Il veut annuler sans friction.

**Rising Action:**
1. Dashboard → "Annuler mon abonnement"
2. Confirmation: "Vous gardez l'accès jusqu'au [date fin période]"
3. Optionnel: "Pourquoi partez-vous?" (non-bloquant)
4. Confirme → Subscription set to cancel at period end

**Resolution:**
Thomas garde l'accès jusqu'à la fin. Pas de mauvaise surprise. S'il revient, il se réabonne facilement.

**Capabilities révélées:** Cancel flow, Access until period end, Optional churn survey, Reactivation path

---

### Journey 5: Passive → Active Conversion

**Persona:** Lucas, 22 — joueur dans l'équipe de Thomas

**Opening Scene:**
Lucas reçoit un clip de Thomas sur Discord. La vue minimap l'impressionne.

**Rising Action:**
1. Curieux → visite le lien WardenWeb dans le clip
2. Landing page → comprend que c'est un outil pour coaches
3. Se dit "Je pourrais analyser mes propres sessions"
4. Pricing → choisit mensuel, crée un compte

**Resolution:**
Lucas devient un Active Player. Potentiel futur coach pour son propre groupe.

**Capabilities révélées:** Landing page clarity, Value proposition for non-coaches, Standalone subscription flow

---

### Journey Requirements Summary

| Capability | Journeys |
|------------|----------|
| Landing Page (value prop, CTA) | J1, J5 |
| Pricing Display | J1, J5 |
| Google Auth + Email/Password | J1, J5 |
| Stripe Checkout | J1, J5 |
| Coupon Redemption | J1 |
| Dashboard (status, dates, actions) | J2, J3, J4 |
| Upgrade to Yearly | J2 |
| Stripe Customer Portal link | J3 |
| Subscription status display | J3, J4 |
| Cancel flow | J4 |
| Webhook handling | J3, J4 |

## Domain-Specific Requirements

### Data Privacy (GDPR)

| Requirement | Implementation |
|-------------|----------------|
| **Base légale** | Contrat (subscription service) |
| **Données collectées** | Email, nom (via Google), historique abonnement, coupons utilisés |
| **Données paiement** | Gérées par Stripe (PCI-DSS compliant) — non stockées |
| **Droit à l'effacement** | Bouton "Supprimer mon compte" dans dashboard |
| **Localisation** | Firestore région `europe-west` |

### Account Deletion Flow

**Trigger:** Bouton "Supprimer mon compte" dans le dashboard

**Actions:**
1. Annuler subscription Stripe (si active)
2. Supprimer document `users/{uid}` dans Firestore
3. Supprimer compte Firebase Auth
4. Confirmation visuelle + déconnexion

### Analytics & Cookies

| Élément | Décision |
|---------|----------|
| Firebase Analytics | Activé (gratuit, events standard) |
| Bandeau cookies | Requis (Firebase Analytics utilise des cookies) |
| Consentement | Charger Analytics uniquement après acceptation |

### Legal Pages

| Page | Contenu requis |
|------|----------------|
| **Privacy Policy** | Données collectées, finalités, droits utilisateur, contact DPO |
| **Terms of Service** | Conditions d'abonnement, annulation, limitations |
| **Cookie Banner** | Choix accepter/refuser analytics |
