# P01 Launch-Slice Recommendation

> Status: `PROPOSED — OWNER VALIDATION REQUIRED`. This is an outcome-led hypothesis for downstream discovery, not an approved roadmap, estimate, architecture, or implementation commitment.

## Alternatives compared

| Criterion | A — Discovery Marketplace | B — Marketplace Leads | C — Transactional Marketplace |
|---|---|---|---|
| Boundary | Public providers/search/profiles/contact path. | Discovery + one-recipient direct RFQ + clarify/quote/decline + in-platform text conversation + customer-reported outcome. | Marketplace Leads + booking + customer payment/deposit + provider payout and transaction operations. |
| Customer value | Faster discovery, but risks becoming a directory with stale contacts and no response accountability. | A concrete path from discovery to a useful provider answer and decision, without requiring Superola to complete the transaction. | End-to-end commitment and payment convenience if users trust the platform. |
| Provider value | Presence and possible direct inquiries; attribution and lead quality are weak. | Relevant contextual requests, response control, conversation, and measurable opportunity value. | Commitment/payment support and transaction history, but more rules/fees/operational dependency. |
| Operator/business value | Lowest feature scope; limited proof that the marketplace creates exchange. | Measures request, response, offer, conversation, and customer-reported outcome; supports later subscription evidence. | Rich transaction data and possible transaction revenue, with materially higher support and liability. |
| Learning value | Learns supply, searches, profiles, and clicks; weak signal on successful connection. | Learns demand quality, provider responsiveness, offer behavior, contact boundary, and progression. | Learns full commerce behavior, but failures are harder to attribute across product, payment, disputes, trust, and operations. |
| Monetization potential | Subscription/promotion may be difficult to justify without attributable outcomes. | Provider subscription can be tested after attributable value; sponsored demand can be observed later. | Subscription, transaction fees, and protection products are possible but incentive explanation becomes more complex. |
| Trust requirements | Profile control, freshness, duplicates, content/reporting, truthful ranking/contact. | A plus customer/provider contact verification, request ownership/abuse controls, response behavior, text reporting, and honest offer state. | B plus auditable booking, payment security, cancellation/refund/dispute/protection, payout identity, transaction/review truth. |
| Operational complexity | Low/medium: taxonomy, publication, moderation, stale supply, contact issues. | Medium: RFQ lifecycle, notification failure, no response, text moderation, outcome uncertainty. | High: payment exceptions, payout, refunds, disputes, cancellations, fraud, chargebacks, reconciliation, and transaction support. |
| Compliance implications | Public content/privacy, communications consent, consumer claims, localization. | A plus sensitive request/conversation data, notification consent, retention/correction/deletion, and abuse reporting. | B plus payment, marketplace payout, tax/reporting, fraud controls, card/data obligations, and consumer transaction rules by market. |
| Legacy-data dependency | Optional for cold-start advantage; fresh onboarding remains an alternative. | Optional but potentially valuable. Imported records cannot receive RFQs until lawful use, reachability, control, and publication gates pass. | Very high risk: migrated trust/contact/payment assumptions cannot support transactions without stronger revalidation. |
| Major implementation scope | Governed supply, search, profiles, marketplace discovery, trust minimum, operations, measurement. | A plus customer participation, direct RFQ, clarify/quote/decline, asynchronous text, one notification channel, reports, and outcome prompt. | B plus booking commitments, payment/deposit, payout, refunds/cancellation/disputes, reconciliation, and transaction support. |
| Major risks | Directory trap, stale contact, weak provider ROI, little two-sided learning. | No-response loop, contact escape, customer verification friction, category quote variability, notification failure, insufficient local liquidity. | Scope/timeline blow-up, compliance/operations burden, false trust, poor dispute economics, and premature market constraints. |

## Recommendation

**B — Marketplace Leads: `PROPOSED — OWNER VALIDATION REQUIRED`.**

It is the smallest current candidate that completes a measurable two-sided loop for both customer and provider after applying the bounded B0 validation envelope below. It produces better evidence than profile/contact alone while avoiding the coupled booking/payment/payout/dispute scope cluster.

Each V1 RFQ has exactly one customer-selected recipient and an independent lifecycle. Reusing a private draft requires deliberate provider selection and fresh confirmation. V1 has no recipient set, automatic eligibility/routing, shared response window, fan-out, auto-closure of other RFQs, or automatic reroute.

General matching is a distinct future hypothesis. It adds consent, provider preferences/eligibility, recipient provenance, fan-out caps, deduplication, abuse limits, response windows, closure, rerouting, notification pressure, and local-liquidity operations. None is validated for Superola today.

## B0 — bounded validation envelope for Marketplace Leads

B0 is not a fourth product strategy; it is the bounded, lower-scope product hypothesis for testing alternative B without silently building a general platform. P01 does not claim it is the lowest-cost validation method.

- Begin with one owner-approved geography, one production locale, and the smallest coherent category archetype for the costing/learning baseline; add cohorts only through explicit gates.
- Prefer curated fresh providers. Claim/dispute capability is conditional on an audited, lawful imported/preloaded cohort; broad legacy migration is not assumed.
- Use deterministic marketplace search and provider profiles that are publicly browseable by people in the proposed slice; crawler/indexing acquisition needs an approved public-distribution policy.
- Verify customer contact/account control just in time before provider delivery; P04 must compare pre-submit and guest-then-verify alternatives.
- Create one independent RFQ per recipient. A reused draft never becomes automatic matching.
- Make the universal provider response `clarify`, `decline`, or a minimum text quote/offer. Structured quote fields, versions, and expiry are category-gated.
- Bound conversation to asynchronous text attached to the RFQ. Exclude presence, typing, read receipts, attachments, audio/video, realtime transport requirements, SMS, and push from the baseline.
- Use one approved minimum notification channel with minimal sensitive content and visible delivery/failure state; no vendor is selected in P01.
- Operate self-service happy paths plus manual exception queues. Do not build a generic admin suite, entitlement engine, or sponsored-campaign system.
- Record customer-reported `proceed`, `not proceeding`, or `unknown`, with provenance; do not call it conversion, booking, payment, or service completion.

## Included in the proposed V1/B0 hypothesis

- Bounded launch cohort selected through owner reconciliation.
- Governed provider/service taxonomy and category-aware service-area/profile semantics for that cohort.
- Provider registration and fresh-business establishment; duplicate reporting/manual exception handling.
- Publishable provider profiles with permissioned category-relevant media and freshness state.
- Publicly browseable human marketplace discovery, organic relevance, profiles, and useful empty/not-accepting-RFQ states; crawler/indexing policy remains an approval gate.
- Just-in-time customer contact/account verification plus owned request status, withdrawal, and closure.
- One-recipient direct RFQ with independent lifecycle.
- Provider clarification, decline, or category-appropriate minimum quote/offer.
- Asynchronous in-platform text and one separate minimum notification channel.
- No-response, spam/abuse/report/block, provider deactivation, and request closure paths.
- Customer-reported outcome signal and supply/demand/marketplace/quality/monetization evidence events.
- Exception-only operator procedures for taxonomy/publication, duplicates, reports, stale supply, and lifecycle visibility.
- Free provider onboarding/eligibility; no generalized plan/trial/proration/entitlement automation.

## Explicitly deferred or conditional

- Automatic general matching, multi-provider broadcast, recipient sets, and automatic rerouting.
- Imported-profile claim/dispute tooling unless a legacy/preloaded cohort passes audit, legal/privacy, economics, and operating-capacity gates.
- Booking acceptance as a platform commercial commitment.
- Customer payment/deposit, provider payout, refunds, disputes, guarantees, protection, and transaction-derived reviews/badges.
- Universal date/resource availability, holds, realtime availability, and calendar sync. “Accepting RFQs” is not a promise of date availability.
- Rich conversation features, attachments, presence, read receipts, realtime transport requirements, SMS, and push.
- Implementable subscription entitlements until package/trial policy and provider value are validated.
- Sponsored placement until credible organic liquidity, disclosure, allocation, measurement, provider demand, and operations exist.
- Natural-language intent on the V1 critical path. Any experiment is separate, independently approved, budgeted, and stoppable.
- External-agent actions and MCP/A2A/A2UI/UCP/ACP adapters; no protocol-only deliverable.
- Native mobile launch unless owner/user evidence changes the channel priority.

## Public distribution and AI placement

### Human marketplace foundation

The proposed slice requires useful provider/service discovery for people. If profiles are approved for public/indexable distribution, the minimum hygiene is useful visible content, stable canonical URLs, governed taxonomy/geography, truthful metadata, a basic sitemap/canonical/robots policy, freshness/publication rules, and referral attribution.

An SEO location-page program, extra locales, public price/date-availability claims, JSON-LD surfaces, AI feeds, and model calls are not implied by that hygiene. Each needs a named acquisition hypothesis, content/maintenance economics, and approval.

### Separate optional experiment

Natural-language intent → structured constraints → validation → deterministic search/ranking is outside the V1 critical path. It requires independent approval, a cost ceiling, quality baseline, privacy boundary, deterministic fallback, fallback-rate measurement, and stop rule.

### Future

External-agent availability/RFQ/quote-status/booking/payment actions and protocol adapters. Reconsider only with a named channel/partner, target-market access, stable approved marketplace actions, security/privacy/operations approval, and measurable economics.

## Cost and operating boundary — unresolved approval gate

P01 does not fabricate developer-time ranges or start P03/P05/P06. The qualitative alternative comparison is sufficient to recommend what to validate, **not** sufficient to approve scope, budget, staffing, or schedule.

Before approval, a capability-level TCO record must compare non-build/concierge or prototype-only validation alternatives plus at least A, B0, and an expanded B across:

| Cost dimension | What must be quantified | B0 control / reconsideration trigger |
|---|---|---|
| One-time delivery | Product/domain/UX work, implementation, accessibility/localization, security/privacy, data preparation, launch validation | Baseline one geography/locale/category archetype; add only when local health and evidence justify incremental scope. |
| Recurring fixed spend | Hosting/operations, support tooling, monitoring, delivery channels, security/compliance, minimum staffing | No technology choice in P01; compare fixed burden before approval. |
| Variable unit cost | Per active provider, search/profile view, RFQ, message, notification, media unit, moderation case, and later model query | Set approved unit ceilings and volume scenarios; optional AI has a separate cost stop rule. |
| Operator workload | Taxonomy/publication exceptions, duplicates, reports, stale-provider handling, notification failures, customer/provider support, language coverage | Measure cases and staff-hours per active-provider/RFQ cohort, backlog age, and resolution time; pause expansion when capacity is exceeded. |
| Migration/compliance | Data audit/cleanup, lawful use/consent, media rights, reachability, taxonomy mapping, privacy/retention/deletion, market-specific obligations | Compare no migration/fresh onboarding, sampled curated import/claim pilot, and broader migration. Expand only when usable-supply yield beats fresh acquisition/re-onboarding economics. |
| Lock-in/exit | Canonical data ownership, bulk export, deletion proof, usage/egress charges, replacement effort, dual-run/cutover, acceptable downtime | P03 build-vs-buy work must record exit and reconsideration thresholds before any service choice. |

Imported records must remain traceable to source and separable until approved. No migration option is the default solely because legacy volume exists.

## Operating envelope

- Start with invite/curated supply where possible, self-service happy paths, and exception-only manual operations.
- Define queue owner, escalation path, language coverage, backlog age, resolution-time measure, and stop/expansion rule before launch approval.
- Notification delivery failure is not provider non-response. “Not accepting RFQs” is not date/resource unavailability.
- Retention, correction/export/deletion, abuse-evidence handling, and replacement/exit expectations for RFQ text and notifications must be defined before technology evaluation.
- Expand category/geography/locale only after supply/demand health, support capacity, compliance readiness, taxonomy/content maintenance, and incremental TCO pass approved gates.

## Approval gates before downstream scope is accepted

1. Owner validates or modifies actor outcomes, the direct-RFQ boundary, one-recipient semantics, and V1 endpoint.
2. Launch category, geography, locale/language, public-distribution policy, and operating capacity are bounded.
3. Legacy alternatives are compared: no migration/fresh onboarding, sampled curated import/claim pilot, and broader migration. Lawful usability, reachability, taxonomy mapping, media rights, manual review time, cost per publishable provider, traceability, rollback, and exit are measured before expansion.
4. Customer verification timing, contact disclosure, simultaneous-open RFQs, response expectations, moderation, retention/deletion, and trust terminology policies are explicit.
5. Provider subscription staging and what remains free are validated without setting a final price or building entitlements in P01.
6. Capability-level TCO, operator workload, compliance/migration effort, exit cost, and reconsideration thresholds are accepted before budget/schedule/scope approval.
7. Owner reconciliation and explicit authorization precede P02/P04; P03 later evaluates technology only against validated decision problems.

P06 remains responsible for an approved roadmap, delivery ranges, dependencies, and committed MVP after relevant product, domain, UX, migration, compliance, cost, and team evidence exists.
