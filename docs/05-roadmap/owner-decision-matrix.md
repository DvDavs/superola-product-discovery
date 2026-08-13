# Owner Decision Matrix — P06

## Use and status

This is the prioritized decision agenda for the next owner conversation. “Current recommendation” means `PROPOSED — OWNER VALIDATION REQUIRED` unless an accepted David decision is explicitly named. Silence does not approve a recommendation.

| ID | Decision | Current recommendation | Alternative | Why it matters | What changes if owner disagrees | Needed by | Status |
|---|---|---|---|---|---|---|---|
| D1 | Booking/payment in first release? | No: Branch L stops at RFQ/conversation/reported outcome. | Branch T transactional first release. | Highest scope, compliance, TCO, UX, and operating lever. | Pause Branch L planning; add booking, availability, money movement, disputes, support, reconciliation, and new evaluation. | Before implementation planning | `OPEN — OWNER DECISION REQUIRED` |
| D2 | Guaranteed availability? | No universal promise/calendar; accepting requests is not guaranteed date availability. | Advisory or reservable availability. | Changes discovery truth, calendars, provider workload, booking semantics, and `G-06`. | Reopen availability/domain/UX/cost work. | Before customer-facing copy and discovery implementation | `G-06 UNSATISFIED`; baseline is `DAVID_DIRECTIVE`, not owner approval |
| D3 | Single-recipient RFQ? | Yes; customer deliberately selects one provider for each new request. | Curated routing or multi-recipient model. | Controls consent, spam, routing, comparison, state, and operations. | Re-estimate demand model and UX; add routing/recipient policies. | Before RFQ implementation | `PROPOSED — OWNER VALIDATION REQUIRED` |
| D4 | Future explicit multi-recipient authorization? | Preserve only as a future decision branch; no automatic fan-out. | Never support it, or later allow visible recipient selection with explicit consent. | Affects future routing, limits, abuse, comparison, disclosure, and monetization. | No V1 impact if deferred; later design changes. | After pilot evidence | `FUTURE — OWNER DECISION BRANCH` |
| D5 | One Account both roles? | Yes; roles derive from behavior, not registration type. | Mutually exclusive account types. | Prevents duplicate identities and future identity/consent migration. | Would reopen an accepted decision and require explicit superseding rationale. | Already settled for new product | `ACCEPTED — DAVID_DIRECTIVE (ADR-004)`; owner awareness only |
| D6 | Launch geography? | One bounded geography chosen by supply, demand, relationships, data quality, and ops. | Multi-market or cross-country launch. | Drives localization, legal, SEO, support, place model, and liquidity. | Re-scope operations, policy, content, and cost. | Before pilot cohort recruitment | `OPEN — OWNER/BUSINESS DECISION` |
| D7 | Launch categories? | Small coherent archetype/category set with adequate density. | Broad catalogue/all categories. | Determines onboarding questions, supply quality, demand relevance, and operator burden. | Broader taxonomy/content/QA and weaker liquidity risk. | Before provider onboarding | `OPEN — OWNER/BUSINESS DECISION` |
| D8 | Legacy migration expectations? | Keep migrate/claim/curated/fresh/hybrid open until P05. | Owner mandates preservation or fresh start. | Changes cold start, SEO, legal/privacy, cleanup, claims, and cost. | P05 question set and later implementation scope change; cannot bypass evidence. | Before P05 classification and any import | `UNKNOWN — INTERNAL ACCESS REQUIRED` |
| D9 | Legacy provider permission/consent? | Assume no lawful use or outreach until verified. | Owner/counsel demonstrate a usable basis and approved cohort. | Blocks contact, claim invitations, publication, and migration. | Enables only the approved uses; same publication gates still apply. | Before P05 data use or outreach | `OPEN — OWNER + COUNSEL`; P05 dependent |
| D10 | Provider phone public or controlled disclosure? | Controlled policy seam; do not hard-code unconditional disclosure. | Public phone, post-request disclosure, or in-platform only. | Affects privacy, conversion, attribution, notifications, and migration parity. | Render/delivery policy changes; storage model should not. | Before public profile/RFQ launch | `OPEN — OWNER/POLICY DECISION` |
| D11 | Subscription? | Free onboarding; test only after attributable provider value. | Early paid tier, permanent free tier, or no subscription. | Affects supply friction, billing, entitlements, retention, and support. | Monetization stage and entitlement scope change. | After pilot value evidence; earlier only for research | `PROPOSED — OWNER VALIDATION REQUIRED` |
| D12 | Sponsored placement? | Later manual fixed-fee experiment after organic liquidity; eligibility first. | No sponsorship, subscription-first, CPC/auction, other inventory. | Affects trust, ranking disclosure, sales, measurement, and operations. | Monetization roadmap and ranking policy change. | Before charging or showing paid inventory | `PROPOSED — OWNER VALIDATION REQUIRED` |
| D13 | Meaning/history of “Destacado”? | Treat current public occurrence only as a suggestion-group heading; historical meaning unknown. | Owner confirms prior paid/organic prominence policy. | Prevents copying an unexplained legacy label into sponsorship. | Historical records/policies may become P05 inputs. | Owner reconciliation/P05 | `OBSERVED — LIVE_PLATFORM`; history `UNKNOWN` |
| D14 | Does Digital Consulting survive? | Keep outside core V1 until strategy is known. | Retire, operate separately, or use as marketplace upsell. | Affects staffing, neutrality, navigation, revenue story, and P07. | Requires separate business/service scope, not silent marketplace coupling. | Before owner-facing roadmap and rebuild scope | `OBSERVED — LIVE_PLATFORM`; `EXISTING PUBLIC BUSINESS LINE — OWNER STRATEGY UNKNOWN` |
| D15 | Does shop/merch survive? | Do not include in marketplace V1. | Preserve separately or retire. | WooCommerce presence does not prove strategic value; commerce adds operations. | Separate commerce scope/integration or decommission plan. | Before rebuild scope/cutover | `OBSERVED — LIVE_PLATFORM`; strategy `UNKNOWN` |
| D16 | Owner feature/monetization document? | Supply and reconcile before approving product scope. | Explicitly proceed without it under a bounded exception. | May distinguish commitments from brainstorms and contradict current hypotheses. | Reconciliation may change priorities, gates, and narrative. | Before owner approval of MVP | `NOT RECEIVED` |
| D17 | Owner UI/UX material? | Supply and reconcile before approving P04/P06 claims. | Explicitly proceed without it under bounded assumptions. | May expose legacy commitments or desired journeys absent from current evidence. | UX deltas and scope consequences must be recorded. | Before design acceptance | `NOT RECEIVED` |
| D18 | Pilot success criteria? | Define outcomes, learning questions, stop/expansion rules; do not invent targets. | Proceed with qualitative learning only. | Prevents post-hoc success definitions and uncontrolled expansion. | Measurement plan may lack decision thresholds, but definitions still ship. | Before pilot launch | `OPEN — OWNER/BUSINESS DECISION` |

## Top 10 for the next owner meeting

1. D1 — Branch L or transactional first release.
2. D2 — what “available” promises.
3. D3 — deliberate single-recipient RFQ acceptance.
4. D6 — pilot geography.
5. D7 — pilot categories/archetypes.
6. D8/D9 — legacy expectation and lawful permission/consent.
7. D10 — provider phone/contact disclosure.
8. D18 — pilot success and stop criteria.
9. D12/D11 — first monetization hypothesis and timing.
10. D14/D15/D16/D17 — surviving public business lines and missing owner materials.

## Decisions that may safely remain open during reversible foundation work

- Exact sponsored packaging, price, inventory, and later subscription plan.
- Future explicit multi-recipient authorization.
- Transaction design when Branch L is selected.
- Fine ranking weights, live facet counts, rendered maps, and public price—provided the launch fallback omits them.
- Legacy migration approach—provided launch supply is curated fresh and Legacy Integration remains empty.
- Retention/expiry durations—provided the mechanisms are configurable and values are approved before launch.

These may remain open only if implementation does not silently choose an irreversible default and each has a named pre-launch or pre-activation gate.
