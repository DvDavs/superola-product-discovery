# MVP Definition — Decision Baseline

## Decision statement

`PROPOSED — OWNER VALIDATION REQUIRED`

The recommended first release is **Branch L — Marketplace Leads/RFQ**: the smallest coherent slice that lets a customer express a real event need, deliberately choose one provider, receive a meaningful provider response, continue an asynchronous text conversation, and report what happened without Superola claiming that a booking, payment, or service completion occurred.

```text
Need / event
→ discovery
→ provider profile
→ deliberate provider selection
→ single-recipient ServiceRequest / RFQ
→ provider clarification / decline / offer / NoResponse
→ asynchronous text conversation
→ customer-reported outcome
```

This is a decision baseline, not an approved implementation scope. `G-06` remains `UNSATISFIED`, relevant ADRs remain proposed, and owner gates remain open.

## Why this is the smallest coherent launch slice

- A directory/contact-only release can show supply but cannot reliably measure whether demand became a provider opportunity or useful response.
- A single-recipient RFQ loop creates value and measurable marketplace behavior for both sides without requiring Superola to become the transaction counterparty.
- Booking/payment is not one extra screen. It adds commitments, money movement, support duties, fraud exposure, reconciliation, and review provenance.
- A bounded pilot can use curated fresh supply and does not depend on a broad legacy migration.

## Branch L and Branch T

| Dimension | Branch L — Leads/RFQ V1 | Branch T — Transactional first release |
|---|---|---|
| Current disposition | Recommended baseline. | Not selected; owner-triggered alternative only. |
| Customer endpoint | Provider response, conversation, and customer-reported outcome. | Auditable booking/payment outcome. |
| Core additions | Discovery, one-recipient request, response states, conversation, notifications, outcome report. | Branch L plus booking object/state, availability commitment, payment/deposit, payout, cancellation, refund, dispute, guarantee, fees/tax, fraud, support, reconciliation, transaction-derived reviews. |
| Operational profile | Marketplace moderation, verification, notification, no-response, and support queues. | Materially higher financial, compliance, fraud, dispute, refund, payout, and reconciliation operations. |
| Evidence dependency | Bounded supply/demand and owner validation. | Explicit owner requirement plus market, legal, payments, operations, unit-economics, and trust evidence. |
| Status | `PROPOSED — OWNER VALIDATION REQUIRED`. | `FUTURE` unless the owner requires it for the first release. |

Public use of “Solicitud de Reserva” and reservation language is `OBSERVED — LIVE_PLATFORM`. It does **not** establish whether a structured transaction capability exists internally. The owner must classify it as: capability to preserve, incomplete plan, obsolete marketing, later desired scope, or first-release requirement. The authorization brief also reports money-back-guarantee marketing, but that claim is not present in the canonical public audit; it remains an owner-reconciliation question until evidence is registered.

## Capability priority model

Complexity values are comparative (`Low`, `Medium`, `High`, `Very High`), not estimates. Evidence confidence describes the confidence in the need or constraint, not implementation certainty.

| Capability | MVP importance | User value | Marketplace value | Technical complexity | Operational complexity | Evidence confidence | Owner decision required? | P05 dependent? | Recommended stage | Reason |
|---|---|---:|---:|---:|---:|---|---|---|---|---|
| Account/auth | MUST | High | High | Medium | Medium | High | Technical holds remain | No | Foundation | Required for verified participation, ownership, security, and durable state. |
| Dual customer/provider context | MUST | High | Medium | Low/Medium | Low | High | Owner awareness only; `ADR-004` is ACCEPTED by David | Legacy UX reconciliation only | Foundation | One Account may act in both roles; do not reproduce legacy forced account typing. |
| Provider onboarding | MUST | High | High | Medium | Medium | High | Publication policy details | No | Supply | Creates controlled, maintainable supply. |
| Provider profile | MUST | High | High | Medium | Medium | High | Public contact and content rules | No | Supply | Core trust and discovery object. |
| Service offerings/categories | MUST | High | High | Medium | Medium | Medium/High | Launch categories | No | Supply | Connects provider claims to demand. |
| Governed taxonomy | MUST | Medium | High | Medium | Medium | High | Governance owner | P05 only for legacy mapping | Foundation | Prevents provider-created category drift and supports search, measurement, and sponsorship. |
| Place/geography | MUST | High | High | Medium | Medium | High | Launch geography | P05 only for legacy mapping | Foundation | Required for relevant discovery and market-cell measurement. |
| Service area | MUST | High | High | Medium | Medium | Medium/High | Semantics by category | P05 only for legacy mapping | Supply | Eligibility must represent declared coverage without promising availability. |
| Discovery/search | MUST | High | High | Medium | Medium | High | Ranking policy approval later | No | Discovery | Entry point for demand; deterministic and explainable at launch. |
| Alternative providers | SHOULD | Medium/High | High | Medium | Medium | Medium | Placement/reuse policy | No | Pilot extension | Useful recovery and comparison path, but not required to prove the first single-recipient loop. |
| Request draft | MUST | High | High | Medium | Medium | High | Expiry/storage policy | No | RFQ | Preserves event context through verification and review. |
| ServiceRequest/RFQ | MUST | High | High | Medium/High | Medium | High | Single-recipient acceptance | No | RFQ | Core demand artifact and attribution point. |
| Verification | MUST | High | High | Medium | Medium | Medium/High | Timing/channel/policy | No | RFQ | Controls abuse and delivery; must be measured separately from form abandonment. |
| Provider response | MUST | High | High | Medium | Medium | High | Response-policy windows | No | RFQ | Clarification, decline, offer, and non-punitive NoResponse complete the provider side. |
| Async conversation | MUST | High | High | Medium/High | High | High | Contact-disclosure and retention policy | No | RFQ | Enables clarification without equating messaging with notifications. |
| Notifications | MUST | High | High | Medium | Medium/High | High | Channel approval | No | RFQ | Best-effort prompts; in-app state remains authoritative. |
| Reported outcome | MUST | Medium | High | Medium | Medium | Medium | Prompt/reason policy | No | RFQ | Provides attributable learning without claiming transaction completion. |
| Operator queues | MUST, bounded | Medium | High | Medium | High | Medium | Owners, escalation, stop rules | Claim queue is P05-conditional | Foundation through Pilot | Exception handling is required, but a generic admin suite is not. |
| Reporting/blocking | MUST | High | High | Medium | High | High | Moderation policy | No | Foundation/RFQ | Minimum trust and abuse control. |
| Analytics | MUST | Medium | High | Medium | Medium | High | Metric targets later | No | Foundation | Day-one instrumentation is necessary because no behavioral baseline exists. |
| Sponsored placement | LATER | Medium | Medium/High | Medium | High | Low/Medium | Yes | No | Monetization experiment | Requires organic eligibility, disclosure, inventory, and trustworthy measurement first. |
| Subscriptions | LATER | Medium | Medium | Medium/High | Medium/High | Low | Yes | No | Monetization/Growth | Recurring billing and entitlements should follow attributable provider value. |
| Availability/calendar | NOT V1 | Potentially high | Medium | High | High | Low | `G-06` yes | No | Transaction readiness | `accepting requests != guaranteed date availability`; no universal calendar. |
| Booking | NOT V1 | Potentially high | High | Very High | Very High | Low | Yes, high leverage | Internal legacy capability unknown | Transaction branch | Introduces commitments and transaction truth. |
| Payments | NOT V1 | Potentially high | High | Very High | Very High | Low | Yes, high leverage | Internal legacy capability unknown | Transaction branch | Pulls deposits, payouts, refunds, disputes, fraud, tax, and reconciliation into scope. |
| Reviews | NOT V1 | Medium | Medium | High | High | Low | Provenance policy | Legacy reviews require P05 | Transaction branch or later trust work | Transaction-derived reviews require an auditable transaction; generic self-reported reviews risk false trust. |
| Legacy claims | CONDITIONAL/LATER | High for legacy providers | Medium/High | High | Very High | Low | Yes + counsel | Yes | After P05 decision | Claim binds control and must not auto-publish. |
| Legacy migration | NOT V1 dependency | Indeterminate | Potentially high | High | Very High | Low | Yes + counsel | Yes | Access-dependent track | No migration strategy is defensible before audit. |
| Digital consultancy | NOT CORE V1 | Existing-line value unknown | Strategy unknown | Unknown | Unknown | Public existence high; strategy low | Yes | Internal performance evidence | Owner strategy branch | Existing public business line; do not fold it into marketplace architecture without strategy. |
| Shop/merchandise | NOT V1 | Unknown | Unknown | High | High | Low | Yes | Internal performance evidence | Owner strategy branch | Legacy presence alone is not a reason to rebuild commerce scope. |

## Alternative providers

### Recommended treatment

`PROPOSED — OWNER VALIDATION REQUIRED`: preserve a V1 seam and instrument the entry points, then test the complete alternative-provider flow as a bounded pilot extension after the primary single-recipient path is working.

```text
alternative impression
→ See more
→ eligible alternative list
→ customer deliberately selects a provider
→ compatible EventContext and category answers are copied into a new draft
→ customer reviews recipient and reused information
→ customer explicitly sends a new single-recipient request
```

Compatible reuse may include EventContext, dates/logistics, budget if supplied, event/location context, and category-compatible answers. Recipient, eligibility, stale values, and incompatible category answers must be re-evaluated. A copied draft is not a sent request.

Automatic fan-out remains `NOT V1`. A future branch may ask whether the customer explicitly authorizes a visible set of eligible recipients, but it would require consent evidence, recipient limits, deduplication, throttling, disclosure, provider experience rules, response comparison, abuse controls, and monetization policy. P06 records the branch and does not design it.

Owner decision branch for later: **“Would you want the customer to explicitly authorize sending the request to these selected businesses too? Yes / No.”** Either answer leaves V1 single-recipient and deliberate.

## Alternative-provider ranking policy

David's desired ordering—category, area, sponsored placement, profile quality, price, recent activity, response speed—is a `PROPOSED` input, not a canonical ranking formula.

The defensible policy has two layers:

1. **Eligibility:** category relevance, service-area/location eligibility, publication gates, and safety/moderation restrictions. Paid status cannot bypass this layer.
2. **Placement/ranking:** disclosed paid placement and organic ordering operate only inside the eligible set, with `placementBasis` recorded for every impression.

At launch, price, recent activity, and response speed are unsafe ranking inputs unless their definitions, coverage, freshness, manipulation resistance, missing-data treatment, and fairness consequences are evidenced. Profile completeness is a publication/trust signal before it is a competitive ranking lever. Sponsored placement must not imply quality, availability, endorsement, or superior outcome.

## Monetization candidates

All candidate models are `PROPOSED — OWNER VALIDATION REQUIRED`.

| Model | Build complexity | Trust risk | Operating burden | Measurement needed | Best stage | Liquidity dependency | Primary concern |
|---|---|---|---|---|---|---|---|
| Subscription tier | Medium/High | Medium | Medium/High | Attributable leads, retention, entitlement use | Early/Growth | Medium/High | Charging before providers receive value. |
| Fixed-fee sponsored placement | Low/Medium if manual and bounded | Medium/High | Medium | Eligible impressions, clicks, leads, organic comparison | Early | High | Disclosure, fair rotation, and scarce inventory. |
| Category sponsorship | Medium | High | High | Category reach, sponsored-associated outcomes; causal lift only under an approved design; concentration | Growth | High | Category capture by one payer. |
| Geography sponsorship | Medium | High | High | Geographic reach and eligibility accuracy | Growth | High | Weak location data creates misleading inventory. |
| Homepage placement | Low/Medium | Medium/High | Medium | Qualified reach and downstream leads | Early/Growth | Medium | Broad traffic may be irrelevant and hard to value. |
| Provider boost | Medium | High | High | Incrementality, frequency, fairness, fatigue | Growth | High | Easy to become opaque pay-to-win ordering. |
| CPC | High | High | High | Valid click, fraud, billing, attribution | Growth | High | Click fraud and weak provider-value attribution. |
| Budget auction/bidding | Very High | Very High | Very High | Auction integrity, budgets, pacing, fraud, attribution | Not before Growth | Very High | Ad-tech system and fairness burden are disproportionate. |
| Hybrid | Very High | Very High | Very High | All component measures and interaction effects | Growth only | Very High | Complexity hides which proposition works. |

### First experiment recommendation

After a pilot market cell demonstrates credible organic supply and demand, test **a manually operated, time-bounded, fixed-fee sponsored placement** with a small fixed inventory, transparent “Sponsored” disclosure, fair rotation, organic eligibility as a prerequisite, and no auction/CPC engine. Before charging, a non-binding sales test may validate provider interest and packaging language. Stop or redesign if organic outcomes deteriorate, complaints rise, eligible inventory is too thin, or providers cannot connect placement to attributable value.

## What the current public platform should inform, not dictate

- Existing public categories, listings, URLs, role-typed registration, booking language, “Destacado,” Digital Consulting, and shop surfaces are evidence of historical/public surface choices—not automatically requirements. The guarantee claim from the P06 authorization brief awaits registered evidence or owner confirmation.
- Preserve useful vocabulary, SEO equity, provider recognition, and owner/business knowledge where verified.
- Do not copy uncontrolled taxonomy, unreliable geography, unclear ownership, forced customer-versus-business registration, ambiguous availability/booking promises, unexplained prominence, or unverified content into the new product.
- Whether public phone numbers remain public is an owner/privacy policy decision; the architecture already preserves a disclosure seam.

## What can start while decisions remain open

Reversible engineering planning can refine the governed taxonomy workflow, place model, dual-role account behavior, analytics event contract, privacy classification, publication gates, deterministic eligibility, and isolated Branch L states. It may also preserve seams for alternative request reuse, `placementBasis`, and legacy isolation without activating those features.

Implementation must not silently settle launch geography/categories, availability promise, contact disclosure, booking/payment boundary, legacy permission/migration, monetization packaging, or pilot success criteria. Technical decisions on hold—including `ADR-020`—still require their named owner.

## Transaction-evolution gates

Move from leads marketplace toward transaction marketplace only when evidence supports the coupled operational burden:

- sustained request volume and stable provider response behavior;
- repeated customer demand for in-platform commitment or payment;
- provider willingness to manage availability and accept platform transaction rules;
- sufficiently normalized price and service terms to make commitments intelligible;
- resolved availability semantics and cancellation boundaries;
- demonstrated refund, support, dispute, fraud, and reconciliation capacity;
- acceptable unit economics after cash cost and human operations are shown separately;
- legal, tax, payment, privacy, and market-specific readiness;
- trustworthy transaction provenance for reviews and guarantees.

No numeric gate is asserted because no authoritative baseline or business target exists.
