# Phased Roadmap — Evidence Gates, Not Dates

## How to read this roadmap

This roadmap sequences decisions and evidence. It is not a delivery schedule, staffing promise, or approval to build. Dates require a selected product branch, team capacity, accepted technical decisions, and—where legacy supply is involved—P05 evidence.

The recommended path is Branch L, a leads/RFQ marketplace. If the owner requires Branch T in the first release, the roadmap forks at Stage 0 and must be re-scoped before implementation; Stage 8 is only the later-evolution path for a Branch L launch.

```text
Stage 0 branch decision
├─ Branch T required → pause → transactional scope/TCO/compliance replan
└─ Branch L selected → Stage 1 → Stage 2 → Stage 3 → Stage 4 → Stage 5
                                                  ├─ optional Stage 6 monetization
                                                  ├─ Stage 7 optimization/expansion
                                                  └─ later Stage 8 transaction consideration

P05 access track: owner approval → access/controls → audit → migration decision
                                       └─ approved cohort may enter Stage 2 later
```

## Stage 0 — Select the product and pilot envelope

| Field | Definition |
|---|---|
| Goal | Decide whether planning proceeds on Branch L or is reworked for Branch T; bound the market and owner expectations. |
| Scope | Booking/payment classification, availability promise, single-recipient boundary, launch geography, launch categories/archetypes, locale, contact disclosure, legacy expectation, Digital Consulting/shop disposition, pilot success/stop criteria, and owner-material reconciliation. |
| Entry criteria | P06 decision package available. |
| Exit criteria | Branch L/T selected or explicitly left as a blocking decision; a bounded pilot envelope exists; owner disagreements and their consequences are recorded; technical decisions needed for foundation work have named dispositions. |
| Owner decisions required | D1–D4, D6–D10, D14–D18 in the owner decision matrix. |
| Evidence required | Owner answers; feature/monetization and UI/UX materials; business relationships and demand knowledge; current business-line performance where relevant. |
| Risks | Scope ambiguity, unavailable owner materials, `G-06`, false preservation of legacy features, US/MX assumptions, schedule pressure. |
| Dependencies | None beyond current discovery artifacts; P05 is not required to choose fresh-supply Branch L. |
| Explicitly not done | No implementation, P05, migration, ad-tech, transaction design, city/country selection by the product team, or numeric targets. |
| Metrics | Decision coverage and unresolved-blocker count only; no product KPI exists yet. |

**Branch fork:** If the owner requires a transactional first release, pause the Branch L sequence and produce a new scope/TCO/compliance/operations plan for Branch T before Stage 1. Do not treat transactions as a late add-on to an already planned lead marketplace.

## Stage 1 — Governed foundation and operating readiness

| Field | Definition |
|---|---|
| Goal | Establish the reversible rules and measurement needed by the bounded pilot. |
| Scope | Dual-role Account behavior; taxonomy/place governance; privacy/data classification; publication and eligibility gates; reporting/blocking; analytics event contract; operator queue ownership/escalation/stop rules; organic `placementBasis`; notification state; proposed rendering/auth/hosting decisions only after their named approvals. |
| Entry criteria | Branch L envelope selected; pilot market-selection criteria agreed; required technical ADRs accepted or explicitly held with a safe fallback. |
| Exit criteria | Metric definitions and privacy constraints are testable; each launch queue has an owner, escalation path, language coverage, backlog measure, and stop rule; no open policy is accidentally encoded as irreversible behavior. |
| Owner decisions required | Contact disclosure policy, pilot criteria, market/category envelope; owner awareness of dual-role behavior. |
| Evidence required | Security/privacy review, TCO assumptions with human effort separate from cash cost, accessibility acceptance approach, and agreed product vocabulary. |
| Risks | Analytics absent, operator load, notification/contact disclosure, multilingual leakage, overbuilt admin tooling, held ADRs treated as accepted. |
| Dependencies | Stage 0. P05 may start separately only after access and controls exist. |
| Explicitly not done | No generic entitlement/campaign engine, universal calendar, booking/payment, broad import, AI critical path, or distributed architecture. |
| Metrics | Instrumentation coverage; event validation failures; queue ownership coverage; notification failure; report/block volume; privacy-safe analytics quality. |

## Stage 2 — Curated, publishable supply

| Field | Definition |
|---|---|
| Goal | Create enough trustworthy provider supply in the bounded market to test demand. |
| Scope | Fresh provider onboarding, Business/ProviderProfile, offerings, governed category attributes, declared service area, verification facts, media rights, profile completion, publication review, freshness/intake state. |
| Entry criteria | Foundation controls and pilot envelope exist; a curated provider acquisition plan and operator capacity are named. |
| Exit criteria | A bounded cohort meets the same publication gates regardless of source; coverage gaps and inactive/unconfirmed supply are visible; providers can maintain their profile on mobile. |
| Owner decisions required | Launch categories/geography; verification and profile-quality policy; whether any owner relationships seed the cohort. |
| Evidence required | Provider outreach/verification results, category and geography coverage, profile completion and publication-failure reasons. |
| Risks | Provider cold start, stale/unreachable supply, taxonomy/location quality, media rights, moderation load. |
| Dependencies | Stage 1. Legacy supply is excluded unless a later P05 decision admits a cohort. |
| Explicitly not done | No publication of `LegacyProviderRecord`; no relaxed gate for imported records; no paid bypass; no guaranteed availability claim. |
| Metrics | Publishable providers, published providers, completion, category/geography coverage, intake status, active/unconfirmed/stale states, time and operator effort to publication. |

## Stage 3 — Honest discovery

| Field | Definition |
|---|---|
| Goal | Let customers find eligible providers without overstating coverage, availability, price, or sponsorship. |
| Scope | Category/place search, deterministic eligibility, results, profiles, zero-result handling, organic ordering, public projections, SEO/canonical rules for approved surfaces, and `placementBasis=organic`. |
| Entry criteria | Sufficient publishable supply in the chosen market cells; governed taxonomy/place data; public-content and contact rules. |
| Exit criteria | Search results are explainable; zero results distinguish category and coverage gaps; profile views and result-to-profile movement are measured; public pages pass quality/canonical gates. |
| Owner decisions required | Public phone disclosure; price-display decision (`Q-040`) if price is considered; approved public language for availability. |
| Evidence required | Search behavior, zero-result patterns, result quality review, geographic comprehension, SEO baseline where available. |
| Risks | Thin SEO pages, geography error, category drift, misleading availability, price incomparability, ranking opacity. |
| Dependencies | Stage 2. |
| Explicitly not done | No sponsored reordering, price sort, activity/response-speed ranking without safe data, rendered map, automatic matching/fan-out, or public availability calendar. |
| Metrics | Searches, zero-result rate, category/place selected, candidate set, profile views, result-to-profile CTR, search latency, relaxation use, coverage gaps. |

## Stage 4 — Single-recipient RFQ loop

| Field | Definition |
|---|---|
| Goal | Complete the first measurable two-sided interaction. |
| Scope | Local/server RequestDraft boundary, progressive intake, explicit recipient review, verification, new single-recipient ServiceRequest, delivery, clarification/decline/offer/NoResponse, asynchronous text, notifications separate from conversation, withdrawal, reporting/blocking, and customer-reported outcome. |
| Entry criteria | Discovery works in the bounded market; verification and notification paths are selected; contact, retention, expiry, and NoResponse policies are launch-ready. |
| Exit criteria | A request can move through every happy and failure state without duplicate sends or silent rerouting; in-app state remains authoritative; operator exceptions have stop rules. |
| Owner decisions required | Explicit acceptance of single recipient; verification timing; contact disclosure; customer/provider primary outcome; policy windows. |
| Evidence required | Funnel tests, accessibility checks, notification failure behavior, abuse controls, mobile provider response, privacy review. |
| Risks | Composer/verification abandonment, provider silence, notification failure confused with NoResponse, contact leakage, moderation load, ambiguous outcome. |
| Dependencies | Stages 1–3. |
| Explicitly not done | No recipient set, automatic reroute/fan-out, booking acceptance, payment, realtime/presence/read receipts, or transaction-derived review. |
| Metrics | Request starts, per-step abandonment, verification completion/abandonment, submitted/delivered, clarification, decline, offer, NoResponse, conversation activity, notification delivery failure, reported outcome. |

## Stage 5 — Bounded pilot and alternatives experiment

| Field | Definition |
|---|---|
| Goal | Determine whether the marketplace loop creates useful outcomes and whether deliberate alternatives are associated with additional useful opportunities; causal incrementality requires a separate approved design. |
| Scope | Curated demand acquisition, live operating review, cohort health, quality correction, and—only after the primary path works—a bounded alternative-provider experiment with “See more,” eligible alternatives, compatible draft reuse, fresh recipient review, and a new explicit single-recipient send. |
| Entry criteria | Stage 4 acceptance; owner-approved pilot criteria; enough curated supply and operational capacity; instrumentation verified. Before alternatives activate, a bounded UX delta for the alternatives/list/reuse surfaces must pass owner scope approval plus UX, accessibility, privacy, trust, and content review; this is not a reopening of P04 now. |
| Exit criteria | Evidence review states what worked, failed, and is unknown by market cell; alternative-associated requests/outcomes are distinguishable descriptively from ordinary repeat requests. Causal incrementality is reported only if a predeclared comparison design was approved and executed. Expansion/stop decision recorded. |
| Owner decisions required | Actual pilot market/cohort, acquisition channel, success/stop criteria, and approval to test alternatives. |
| Evidence required | Supply, discovery, demand, trust, outcome, alternatives, and human-operations data; qualitative customer/provider feedback; approved UX delta and its accessibility/privacy/trust validation before alternatives activate. |
| Risks | Demand cold start, weak response, alternative spam perception, multiple-open-request ambiguity, biased curated cohort, operator overload. |
| Dependencies | Stage 4. |
| Explicitly not done | No all-market launch, automatic fan-out, sibling auto-closure, sponsored placement, migration assumption, or numeric threshold invented after seeing results. |
| Metrics | Request-to-response, time to first response, offer rate, no-response, customer-proceeding, repeat demand, provider coverage, alternatives impression/click/See More, reused draft start/submit, alternative response and alternative-associated reported outcome, operator hours/backlog. Incremental uplift only under the measurement plan's causal-design gate. |

## Stage 6 — Simplest monetization experiment

| Field | Definition |
|---|---|
| Goal | Test willingness to pay without building ad-tech or damaging organic trust. |
| Scope | First a non-binding sales/packaging test; then, if evidence supports it, a manual time-bounded fixed-fee sponsored placement in one healthy market cell, with fixed inventory, transparent disclosure, fair rotation, eligibility prerequisite, and separate organic/sponsored measurement. |
| Entry criteria | Credible organic liquidity; attributable provider value; eligible inventory; owner-approved policy and offer; support/billing capacity; trustworthy `placementBasis` data. |
| Exit criteria | Provider interest, operational effort, trust effects, and sponsored-associated lead value are documented; causal lift is claimed only under a predeclared comparison design. Continue/stop/redesign decision made without requiring sunk ad-tech cost. |
| Owner decisions required | Offer, price, term, inventory, disclosure, refund/credit policy, sales owner, and success/stop criteria. |
| Evidence required | Provider willingness to pay, organic baseline, slot scarcity, position effect, complaints, sponsored-associated leads/outcomes; causal incremental leads/outcomes only with approved assignment/holdout and uncertainty reporting. |
| Risks | Paid placement bypassing relevance, unclear “Destacado” meaning, fairness, concentration, unsellable inventory, overbuilding, manual sales/support load. |
| Dependencies | Stage 5 evidence. |
| Explicitly not done | No CPC, bidding, auction, self-serve campaign manager, generic entitlement engine, or paid publication bypass. |
| Metrics | Eligible sponsored inventory, impressions, clicks, leads, revenue/spend where enabled, fill/rotation, organic vs sponsored outcomes, complaints/refunds, operator hours. |

## Stage 7 — Marketplace optimization and evidence-led expansion

| Field | Definition |
|---|---|
| Goal | Improve healthy market cells and add scope only when evidence and operations support it. |
| Scope | Supply-quality interventions, ranking experiments, additional cohorts/categories/geographies/locales, notification improvement, subscription consideration, and tooling only for measured operator bottlenecks. |
| Entry criteria | Pilot decision documented; any activated monetization experiment has its own decision record; expansion does not exceed operator stop rules; taxonomy/geography and localization quality are ready. |
| Exit criteria | Each expansion has a measured hypothesis, rollback/stop rule, and sustainable cash/human operating profile. |
| Owner decisions required | Expansion order, investment level, subscription packaging, market-specific policies. |
| Evidence required | Repeatable liquidity, retention, demand, supply acquisition, ranking fairness, support capacity, and unit economics. |
| Risks | Premature geographic spread, opaque ranking, subscription before value, tooling sprawl, multilingual/SEO debt, human TCO dominating cash cost. |
| Dependencies | Stage 5. Stage 6 is optional and becomes a dependency only for monetization-specific optimization. P05 evidence may introduce a separately approved legacy cohort. |
| Explicitly not done | No expansion solely because 43,361 public listing URLs exist; no ranking signal without data-quality and fairness proof. |
| Metrics | Market-cell health, repeat demand, provider retention/freshness, cohort acquisition cost/effort, organic ranking outcomes, support load, cash cost and human sensitivity reported separately. |

## Stage 8 — Transaction extension consideration

| Field | Definition |
|---|---|
| Goal | Decide whether evidence justifies evolving from leads to an in-platform transaction marketplace. |
| Scope | Discovery and design only until gates pass: booking commitment, availability/holds, processor, deposits, payout, cancellation/refund/dispute, guarantee, fees/taxes, fraud, support, reconciliation, transaction-derived reviews, compliance and TCO. |
| Entry criteria | Stable marketplace loop, demonstrated demand for in-platform commitment, provider willingness, resolved availability/pricing semantics, operational/legal/payment readiness, and plausible unit economics. |
| Exit criteria | A separately approved transaction proposal and architecture/UX/cost plan—or a documented decision to remain a lead marketplace. |
| Owner decisions required | Transaction strategy, markets, fee model, guarantees, risk ownership, support posture, and investment. |
| Evidence required | Request volume, off-platform leakage/demand, response reliability, provider adoption, price normalization, refund/dispute capacity, fraud/compliance readiness, unit economics. |
| Risks | Feature-cluster inflation, financial loss, support burden, regulatory variation, payout identity, tax, chargebacks, false review/guarantee claims. |
| Dependencies | Evidence from Stages 5–7. This stage does not apply if Branch T was selected at Stage 0; that branch requires replanning before Stage 1. |
| Explicitly not done | No assumption that adding a payment provider completes the transaction product; no numeric gate without evidence. |
| Metrics | Defined only in the later transaction plan: booking/payment completion, cancellation/refund/dispute/fraud/reconciliation and unit economics, with provenance. |

## Pilot market selection criteria

P06 does not select a city, country, category, or locale. The owner/business choice should optimize for:

- enough publishable supply and category density;
- observable or reachable demand;
- owner/business relationships that make curation feasible;
- geography and category data that can be governed accurately;
- a manageable trust/support workload and language footprint;
- instrumentation and acquisition paths that permit learning;
- a cohort small enough to stop or correct without misleading a broad market.

The pilot must not try to activate all 43,361 public listing URLs.

## Operational load by maturity

| Stage of market | Main human work | Planning consequence |
|---|---|---|
| Pilot | Provider curation, publication help, verification/notification exceptions, reports, direct support, manual measurement review. | David/operator time competes directly with delivery; stop rules matter before traffic. |
| Early | Taxonomy/location governance, supply freshness, recurring support/moderation, sales/placement operations, cohort expansion, notification troubleshooting. | Dedicated operating capacity may become necessary before infrastructure cost is material. |
| Growth | Multi-market policy, quality/fraud systems, support coverage, monetization operations, localization/SEO governance, data quality, possible transaction readiness. | Human operations may dominate cash infrastructure; automation should target measured queues, not anticipated complexity. |

### Existing P03 planning frame

The P03 cost model supplies the following `ASSUMPTION` scenarios for the current leads envelope. They are neither forecasts nor commitments and exclude booking/payment and unapproved legacy-claim workloads. Human sensitivity uses **USD $75/hour for infrastructure operations and USD $40/hour for marketplace operations**; those rates are assumptions, not compensation commitments.

| Scenario | Infrastructure/service cash | Infrastructure operations at USD $75/hour | Marketplace operations at USD $40/hour | Illustrative human-cost sensitivity using those rates |
|---|---:|---:|---:|---:|
| Pilot | USD $75.98/month | 3.25 hours/month | 8.35 hours/month | Approximately USD $578/month |
| Early | USD $192.99/month | 5.00 hours/month | 55.42 hours/month | Approximately USD $2,592/month |
| Growth | USD $560.79/month | 9.00 hours/month | 340.58 hours/month | Approximately USD $14,298/month |

Source: `docs/03-technology/cost-model.md`. The hourly-rate and workload assumptions must remain visible wherever the human sensitivity is shown. Infrastructure/service cash and human operational sensitivity must never be combined as one owner-facing headline. Any Branch T or approved legacy-claim workload requires a new model, not a multiplier applied to these values.

## Team and specialist needs by decision, not calendar

P06 does not invent headcount. It identifies responsibilities that must have named coverage before their stage begins:

| Responsibility | Needed for | Why it cannot be implicit |
|---|---|---|
| Owner/business decision maker | Stage 0 and every expansion/monetization gate | Selects branch, market, categories, business-line strategy, pilot criteria, and investment. |
| Product/engineering owner | All stages | Keeps the bounded hypothesis, domain seams, measurement, and stop conditions coherent. |
| Marketplace operator | Stages 1–7 | Owns provider curation, publication exceptions, reports, support, freshness, queue capacity, and later manual monetization. |
| UX/accessibility validation | Stages 3–5 | Verifies progressive intake, verification, provider mobile response, conditional questions, and failure recovery. |
| Privacy/security/legal counsel | Contact/retention policy, P05, monetization, and Branch T | David cannot establish lawful basis, market obligations, consent, payment, tax, or guarantee policy alone. |
| Data/analytics ownership | Stages 1–7 | Maintains event definitions, data quality, privacy-safe segmentation, experiments, and honest causal language. |
| SEO/content/localization ownership | Public discovery and cutover | Governs useful public pages, canonicals/redirects, bilingual quality, and legacy equity. |
| Legacy/data migration specialist | P05 track only after access | Audits schema/data, provenance, dedupe, quality, suppression, rollback, and publishable yield. |
| Payment/risk/finance operations | Branch T only | Processor integration is insufficient without payouts, refunds, disputes, fraud, reconciliation, tax/fees, and support ownership. |

A responsibility may be covered part-time or by one person wearing multiple hats at Pilot, but each needs a named owner, capacity assumption, escalation path, and stop rule. Growth automation follows measured workload; it does not replace ownership.
