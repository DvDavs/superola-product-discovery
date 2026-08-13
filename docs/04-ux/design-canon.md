# P04 Design Canon — the binding brief the UX architecture was written against

> Status: `PROPOSED — OWNER VALIDATION REQUIRED`. Provenance: `TECHNICAL_DISCOVERY`, under the P04 authorization (`SRC-015`).
> **Nothing here is approved.** No owner gate is satisfied by this document. `G-06` remains UNSATISFIED.
> Scope: the vocabulary, constraints, and decisions that every other `docs/04-ux/` document was written to obey. **Not** visual design.

This is the phase's internal brief, persisted rather than discarded. P04 was written by parallel authors working against one set of rules; **the other P04 documents cite this one by section number**, so discarding it would leave the definitions that decide `ADR-020` in chat history rather than in the repository. Where this document and a specialist document disagree, the specialist document is the later word and this one is the record of what it was asked to do.

It is deliberately terse. It is not a summary of P04 — `docs/04-ux/README.md` is the index.

## 0. What P04 is and is not

P04 — UX Architecture. Design how the Superola V1 marketplace works for customers, providers, and operators. Structural architecture only.

**P04 does NOT produce:** final visual design, colors, branding, typography, a component library, a design system, database tables, or production code. Architecture before aesthetics.

**P04 does NOT start P05, P06, or P07.**

Everything P04 produces is `PROPOSED`. Nothing is approved. P04 does not satisfy any owner gate.

## 1. Evidence and provenance vocabulary

Evidence labels, exact enum, always in backticks: `CONFIRMED` `OBSERVED` `PROPOSED` `ASSUMPTION` `FUTURE` `REJECTED`.

Provenance, exact enum, a separate field, never merged into an evidence label: `OWNER_INTERVIEW` `DAVID_DIRECTIVE` `LIVE_PLATFORM` `PROJECT_DATA` `OWNER_MATERIAL` `EXTERNAL_PRIMARY` `EXTERNAL_SECONDARY` `TECHNICAL_DISCOVERY`.

P04's working-assumption stamp, used wherever a decision rests on one of the five assumptions in §3: `DAVID WORKING ASSUMPTION — OWNER VALIDATION PENDING`.

**No owner gate may be marked satisfied.** `G-06` (what "available" promises a customer) is the gate that formally blocks P04 and it is **unresolved**; P04 proceeds on `WA-01` under explicit David authorization and says so.

**No new evidence status or provenance value was invented.** The GigSalad capture supplied by David is recorded as Evidence `OBSERVED`, Provenance `DAVID_DIRECTIVE` — it reached the repository as David-supplied comparative input, not as an owner requirement and not as a first-party observation by this repository. It was **not independently re-verified against first-party sources during P04**, and no Superola requirement derives from it. Registered as part of `SRC-015`.

## 2. Terminology — naming is an architectural constraint

A term that asserts more than the platform can evidence is a defect.

| Use | Never use |
|---|---|
| `Provider` | `Vendor` (retired for the supply side; `Supplier` or `third party` for technology suppliers) |
| `ProviderProfile`, `ServiceOffering` | `Listing` |
| `ServiceRequest` (canonical) / RFQ (accepted owner-facing synonym) | "lead" as an entity, "inquiry" |
| `ProviderResponse` with kind `clarification` \| `decline` \| `offer` | `Quote` as an entity |
| `RequestDraft` | "saved brief" |
| `RequestIntake` states `accepting` \| `paused` \| `unconfirmed` | `available`, `availability`, any availability boolean |
| `EventContext` (value object owned by a request) | `Event` as an aggregate |
| `ServiceAreaDeclaration` (a provider *claim*) | `Service area` as fact |
| `LocationEligibility` = `eligible` \| `ineligible` \| `undetermined` | coercing `undetermined` |
| `ReportedOutcome` = `proceeding` \| `not_proceeding` \| `unknown`, basis `self_declared` | conversion, booking, payment, completion |
| `placementBasis` (V1 value `organic`) on every result slot | a `featured` or `sponsored` boolean |
| `verificationBasis` on every trust-flavoured fact | bare `verified: true` |
| `Account` (opaque, permanent, not typed by role) | customer account / provider account as types |
| `Business`, `BusinessMembership` | "vendor account" |
| `Place`, `BaseLocation`, `GeoPoint` (precision and provenance mandatory), `Market` (Category × Place) | free-text customer geocoding |
| `NotificationIntent` + `DeliveryAttempt` | conflating a notification with a message |
| `Message`, `ThreadEvent` (a system marker, never a Message) | — |
| `Conversation` | "chat" implying realtime |
| `LegacyProviderRecord`, `OwnershipClaim` | "claimable profile" |
| `Person` — a concept only, never a record | — |

Additional hard rules:

- `V1 has no availability model.` Stated positively. `ADR-005` requires the non-claim to be visible in substance on every profile and search surface.
- "Not accepting requests" is **not** date unavailability and is never worded as either.
- A quote is not a booking. `ReportedOutcome: proceeding` is neither a booking nor a payment.
- `unknown` (an explicit customer answer) is **not** `Unreported` (silence), and **an explicit "prefer not to say" is an answer, not a blank.** Absence must be distinguishable from negation.
- No money-shaped language on public pages or in notifications (`DB-02`) — see `Q-040` for the one place this collides with the domain model.
- Superola must **not** tell owners or users that "in-platform" means contact-protected (`ADR-010`).
- Reserve `verified` for a precisely defined auditable event. V1 has no `verified booking`.
- Currency: USD baseline; owner-facing artifacts write `USD $40 / month`, never a bare `$`.

## 3. Working assumptions

The five `WA-01`–`WA-05` assumptions are the canonical register in `docs/04-ux/README.md`. They are not restated here, to avoid two copies drifting.

## 4. Architecture constraints P04 must not break

From P02 and P03. Not P04's to change.

1. **`ADR-004` (ACCEPTED)** — one `Account` may be both customer and provider. No typed accounts, no mutually exclusive types, no account-type flag. Its stated negative consequence is assigned to P04: *"interface design must make the current role obvious."*
2. **`ADR-011`** — authorization is decided in the domain, never in a route guard, a template, or a client.
3. **`ADR-012`** — one machine-access enforcement point, deny by default; the public projection is an allowlist. A JSON endpoint built for a client island is a machine-access surface.
4. **`ADR-013`** — the platform is fixed. Rendering is explicitly not decided by it.
5. **`ADR-005`** — `V1 has no availability model`; `RequestIntake` is an intake state. The decline reason is captured — optional, provider-authored, never operator-derived, never punitive.
6. **`ADR-006`** — `EligibilityDecision` has six named inputs: publication state (`Published`/`Stale` only), category, `LocationEligibility`, `RequestIntake` (surfaced, not silently excluded), trust state, and entitlement (a constant `true` in V1). **It must never gain a paid branch.** Eligibility is per `ServiceOffering`, never per `Business`.
7. **`ADR-007`** — shared core plus five governed `CategoryArchetype`s: **Mobile performer · Fixed venue · Mobile professional · Delivery and food · Transportation and route.** Adding a Category is a governance act; adding an Archetype is an engineering act. `CategoryAttributeDefinition` is operator-governed, never provider-writable. Provider category input becomes a `CategoryProposal`, never a node.
8. **`ADR-010`** — contact data is never an attribute of a request, message, or notification; it resolves at delivery or render time through a recorded disclosure decision. **Notification body allowlist — permitted:** that an event occurred and its type, the acting party's public display name, a non-guessable link to the authenticated surface, coarse timing. **Forbidden:** counterparty contact data, request free text, event address, event date, guest count, budget, offer amounts or terms, any conversation content.
9. **`ADR-018`** — images hosted; **audio and video linked out, never uploaded**; derivatives pre-generated at upload with an explicit processing state, from a small fixed enumerated set; metadata stripped on re-encode; publication gates on the database fact that media exists, never on whether the delivery layer is currently serving it; per-provider count and per-file size caps; ~200 KB served bytes per anonymous public profile view is the design assumption and 400 KB the reconsideration threshold. Dead external audio and video links are a real state needing a sweep and an operator queue.
10. **`ADR-019` Level 1** — Superola owns canonical `Place` identity; **customer-side location resolves against the governed internal `Place` list with zero third-party calls**; geocoding applies to provider base addresses and unresolved free text only, never per search; `GeoPoint` carries precision and provenance; `undetermined` is surfaced, never coerced.
11. **`ADR-009`** — a `LegacyProviderRecord` is structurally incapable of appearing in discovery or receiving a request. A claim binds ownership and does not publish; it seeds a new `ProviderProfile` in `Draft`. Nothing is disclosed before a claim grant — even a masked contact hint is an enumeration oracle.
12. **`system-architecture.md` §3** — **no realtime transport, no server-initiated push.** No WebSockets, no SSE, no presence, no typing indicators, no read receipts, no push. Polling is expensive and must be costed: a 30-second unread poll across 200 concurrent sessions is ≈6.7 requests/second, comparable to all public traffic at the Growth scenario (`docs/03-technology/technology-evaluation.md` §2).
13. **Search** — a governed `Category` or archetype reference is the primary key, never free text. Free text is optional secondary matching over governed labels, synonyms, and narrative. The desired date is **request context only, never a filter**. A governed synonym and alias table is required and must be tried before any model is funded. Filtering and eligibility operate on language-neutral identifiers.
14. **Sponsored** — `placementBasis` on every result slot and every measurement event, only value `organic` in V1, plus a per-slot disclosure marker and a ranking explanation. Sponsored placement is `FUTURE` and, when it ships, is a separately allocated, separately labelled section.
15. **Publication observability** — customers see only `Published` and `Stale` (with a freshness indicator) plus `RequestIntake`. **Customers must not be able to distinguish `Suspended` from `Deactivated`.** `Stale` stays discoverable and flagged, never hidden.
16. **Never publicly exposed:** precise provider base location; customer name or contact channel; event address (the highest-harm field in V1); event date; guest count; budget; request free text; conversation text; offer amount and terms; `Suspended` as a state; response-rate or response-time badges; `ResponsivenessObservation` and `FreshnessObservation`; report contents or reporter identity.

## 5. The P04 decisions

### 5.1 Request intake model — progressive, deterministic, category-governed

- **Traditional guided intake** — a fixed sequential question set for everyone. `REJECTED` as the primary model.
- **Progressive intake** — **SELECTED.** Ask only the next question the selected `Category`'s `CategoryArchetype` requires. The question set is data derived from `CategoryAttributeDefinition`, not a hand-built form per category. Adding a Category adds no screen.
- **Assisted intake** — **`FUTURE`, not V1.** See §5.7.

Progress must be honest: a named-stage indicator, not a false "step N of M" when M is data-dependent.

**On the friction target.** The canon's original brief set a target of 6–9 answers for a mobile performer request. **Adversarial review found that figure was computed net of identity while the competitor comparator was gross of it**, and `docs/04-ux/request-intake.md` §3 withdrew it. The corrected figures are **5 composer answers on the discovery path, up to 8 on direct arrival, and 10–11 total blocking items to submit** with identity counted. This paragraph records the correction rather than quietly restating the number, because the original target is cited in the phase's own reasoning. Every question must justify itself against §5.3; a question that improves no decision and gates nothing is deleted.

### 5.2 `RequestDraft` — user-visible model

A draft begins at the first answered composer question, **local and anonymous**, because `RequestDraft` is `Account`-scoped and no `Account` exists yet under `WA-05`. Server persistence begins when an `Account` exists. **Account creation is never required to browse, search, view a profile, or begin a draft.** A draft is resumable on the same device before an `Account` exists and across devices after. It has no recipient and is never visible to any provider; reuse requires deliberate selection and fresh confirmation per recipient. A category change retains universal `EventContext` answers and drops the previous category's answers, with a warning naming what will be lost and a session-scoped undo. An event-type change drops nothing. On submit the request enters `PendingVerification` — durable but invisible to the provider — so the customer's work is never lost.

### 5.3 Universal versus category-specific — the five classes

- `DISCOVERY` — needed to produce results at all.
- `PRE-SUBMIT` — needed before a `ServiceRequest` may be delivered.
- `QUALITY` — optional; improves the `ProviderResponse` but blocks nothing.
- `CATEGORY` — required, optional, or absent depending on the `CategoryArchetype`.
- `NOT-V1` — not V1. **Deliberately not named `FUTURE`**, because `FUTURE` is a governed evidence label and a class value must not collide with one.

The master classification table is in `docs/04-ux/request-intake.md` §6.

#### 5.3.1 Transportation is modelled and recommended out of the launch cohort

**Transportation's route-corridor semantics do not reduce to a containment predicate**, which is P02's own argument for excluding it from the launch archetype set; P04 models it and recommends it out of the launch cohort. The UX must accept a new Category with no application-shell redesign.

### 5.4 Budget

Optional, expressed as a band, never a required numeric pair. It is `QUALITY`: `EligibilityDecision` does not consider it, so requiring it would block a submission on a field that changes no outcome. Where supplied it reaches the provider inside the authenticated request surface only — never in a notification body.

### 5.5 Event versus service — `EventType != ServiceCategory`

One event may need many independent services. **The V1 request is single-service.** The V1 path for another service is a new `RequestDraft` pre-filled from the previous request's `EventContext`, carrying the customer-declared `eventGroupingHint`. No multi-service RFQ, no fan-out, no automatic routing. The customer-facing grouping is a light presentational grouping — **not** an `Event` aggregate, and no `Event` box is drawn.

### 5.6 Discovery, results, and zero results

Primary flow: need → governed `Category` plus governed `Place` → deterministic eligibility and ordering → ranked results with a per-slot ranking explanation. Free text is secondary matching, never the key; unresolvable input is rejected and shown, never approximated. **The UX must never imply AI is the marketplace source of truth** — in V1 there is no AI on the critical path at all.

Zero results: distinguish and separately record *no supply in this category* from *no supply in this area*; explain coverage honestly; offer progressive relaxation **with the relaxed constraint named**; capture unmet demand; **never convert a query into a broadcast**; make no notification promise, because durable `DemandWatch` is `Q-031` and unratified.

**Facet counts (`Q-035`) — recommendation: none live in V1.** `Q-035`'s owner is P04 plus David; P04 recommends and David decides.

### 5.7 AI-assisted intake — `FUTURE`, not V1

The experiment is UX-worthy in principle and not worth running in V1: the guided composer that would be its baseline does not exist yet; the progressive model removes most of the friction it would target; and the remaining friction sits in free text, which is where extraction is least reliable and which `Q-033` gates. `ai-evaluation.md` §8.1 states that if P04 finds no interaction problem worth testing, the experiment does not happen.

Four conditions, all required, would make it worth running: the guided composer ships and composition abandonment is measured per step (`R-022`); abandonment concentrates in the free-text stage rather than in verification; `Q-033` resolves with a lawful basis and a verified zero-retention term; and an application-enforced hard spend ceiling with a kill switch to the structured form exists. If it ever ships: **the model proposes; the form disposes.**

### 5.8 Rendering — `ADR-020` Option A

Per-surface classification vocabulary:

- `DOC` — mostly document and navigation. Server rendering is sufficient. No island is required **by the class**; an island may still be attached, and must then be named.
- `LOCAL` — bounded local interactive state that does not normally require a rich client rendering model. Navigation-persistent local state is permitted where explicitly bounded by a named island and a domain-owned persistence contract (`UX-07`'s anonymous pre-`Account` window; see `docs/04-ux/surface-inventory.md` §1.1 and `docs/04-ux/rendering-evidence.md` §1.2).
- `RICH` — client state that must survive navigation, held by the client. Would justify Option B for that surface.

**Finding: no V1 surface is `RICH`.** The one candidate, the multi-step composer, has state that survives navigation — but it is held by `RequestDraft`, a domain aggregate `ADR-003` already owns, plus browser-local storage before an `Account` exists. Three named islands: `I-1` governed type-ahead picker, `I-2` composer step controller and local draft persistence, `I-3` media upload manager. Each must degrade to a working server-rendered path, and each goes through the same application layer and the same single machine-access enforcement point. **No JSON API is built speculatively.**

Cost is not the deciding factor and must not be presented as one — rendering moves no infrastructure line.

### 5.9 Map — `ADR-019` Level 3: no rendered map in V1

Decided on user value, not vendor capability and **not on privacy**. The privacy invariant is retained absolutely and is explicitly not the reason: a city centroid or declared coverage emits nothing private. The reason is that no V1 journey needs one; the strongest candidate, Fixed venue, is answered by a coarse `Place` label, the published venue address where the provider chose to publish it, and a link-out to the customer's own map application, which is not a rendered map. A pin drawn from a coarse centroid asserts what the data does not support, and `undetermined` has no honest pin at all. Deferred with three named triggers, not rejected.

### 5.10 Identity and verification — `WA-05` guest-then-verify

Five timings were compared: before search, before profile, pre-submit account, guest-then-verify, and only-when-needed. **Guest-then-verify is recommended.** Anonymous discovery, profile viewing, and composition; at submit the customer supplies a contact channel and the request is stored as `PendingVerification`; delivery occurs only after control of that channel is proven (`DB-12`). The honest cost is that verification abandonment (`R-022`) concentrates here and must be instrumented separately from composer abandonment, because the fixes differ. **No government identity verification.** P04 does not resolve `Q-026`'s disclosure limb or contact-disclosure policy; the `ADR-010` seam makes the UX identical under all three owner answers.

### 5.11 Accessibility baseline

No compliance certification claim anywhere. Keyboard operability for every interactive surface including the three islands; the type-ahead is an ARIA combobox with a plain-select fallback. Labels programmatically associated; errors associated and announced, never color alone. **Dynamic question reveal is the hardest accessibility problem in P04** — the new region is announced politely, focus moves to the region heading rather than the first input, and the reveal never reorders answered questions. Progress is a named stage, honestly. Status is never color-only. Reduced motion respected. Spanish strings run materially longer than English, so no fixed-width control may depend on English string length; `contentLocale` is explicit and machine-translated content is marked. Alternative text is a property of the `MediaReference` placement.

### 5.12 Responsive

Every primary journey evaluated at phone width; no native-mobile-specific patterns. The composer is one question-group per screen with a persistent primary action; results are a single column; the profile leads with identity, service, coarse location and the request CTA. **The provider request-response surface is the highest-risk mobile surface in the product**, because providers are mobile-first in practice — performers working from phones (`DB-06`). Operator queues are the one surface class allowed to be desktop-first, stated rather than assumed.

### 5.13 Provider onboarding

Separate **required to create an account** — a contact channel and proven control of it, nothing else — from **required to appear in discovery**, which is `publicationGateMet`, archetype-aware. A live requirements checklist is visible from the first screen, each item stating why it is required and what it unlocks; progress is saved at every step; preview is available before every requirement is satisfied; **an incomplete profile must never silently become public.** Raising a publication requirement must not retroactively unpublish. `Stale` → `reconfirm` is a one-action path from a notification link and is the highest-frequency provider maintenance action in the product.

### 5.14 Provider workspace

Requests inbox, request detail, respond, conversation, offerings, profile, media, publication state, `RequestIntake` control, notification settings, and minimal outcome visibility. **Not a CRM.** No pipeline stages, custom fields, tags, bulk actions, report builder, or calendar. If a surface cannot name the marketplace decision it supports, it does not ship.

### 5.15 Dual role

One `Account`, one session, no mode toggle that changes global state. Two persistent contexts side by side — **Hiring** and **My business**, the latter present only when a `BusinessMembership` exists. Becoming a provider is additive inside the same account. Every surface where capacity is ambiguous states it. A provider hiring another provider uses the ordinary customer path. **No authorization decision may depend on which context is displayed.**

### 5.16 Response, conversation, outcome

Response kinds are user-visibly distinct: `clarification`, `decline` (optional provider-authored reason, non-punitive, immutable), `offer` (optional `PriceStatement`). **Not responding is a fourth user-visible outcome and is a system-observed state (`NoResponse`), not a provider action** — observable, non-punitive, non-terminal, window `POLICY PENDING`. Never present silence as a judgement (`Q-032`). **A quote is not a booking**: no accept button that creates an obligation, no transaction-protection implication. Structured fields, revision and expiry are archetype-gated; where revision is not enabled, "current offer" is the most recent by recency, and `Superseded` responses stay visible to both parties.

Conversation is asynchronous text attached to the request, rendered as one chronological thread with the request and each `ProviderResponse` as anchored, visually distinct entries — a presentation choice P02 explicitly permits. Author role always shown; `ThreadEvent` markers never counted as messages. Unread is computed server-side and rendered on navigation; **no global polling.** Notification failure is visible to its own party and is never presented as counterparty non-response. A blocked party sees a policy-level reason only.

Outcome: `ReportedOutcome` = `proceeding` / `not_proceeding` / `unknown`, customer-only, `self_declared`, on a governed trigger. `unknown` is an explicit answer; silence is `Unreported`. The richer reason list is an **optional secondary reason alongside the governed triple**, recorded as a `PROPOSED` P04 extension to `ADR-003` (`Q-037`) and never as a replacement enum.

### 5.17 Operator surfaces

Minimum viable, exception-only. **Eight domain queues plus one cross-queue index, on nine surfaces** (`UX-26`–`UX-34`); `UX-29` carries two case kinds. Each records reason, state, priority where justified, actor or owner, and an auditable action. Every private-content access is audited and reason-tagged. **No generic admin suite, no entitlement engine, no sponsored-campaign system, no dashboard inflation.** Per-queue operator time is unmeasured and must not be fabricated.

### 5.18 What P04 explicitly does not decide

`G-06` (`Q-007`) · contact-disclosure policy (`Q-008`, `A-010`, `OR-011`) · `Q-026`'s disclosure limb · `Q-031` · the `NoResponse` window · the `RequestIntake` decay window · `Q-020` beyond a recommendation · `Q-040` · sponsored mechanics · subscription pricing or packaging · legacy migration design · payment and booking design · any vendor · any color, font, or brand.

## 6. Surface inventory — canonical IDs

The authoritative per-surface record is `docs/04-ux/surface-inventory.md`. This is the ID register only.

| ID | Surface | Primary user | Auth | Rendering | V1 |
|---|---|---|---|---|---|
| `UX-01` | Home / need entry | Customer | Public | `DOC` + `I-1` | V1 |
| `UX-02` | Category / archetype browse | Customer | Public | `DOC` | V1 |
| `UX-03` | Market page (Category × Place) | Customer | Public | `DOC` | V1 |
| `UX-04` | Search results | Customer | Public | `DOC` + `I-1` | V1 |
| `UX-05` | Provider profile (public) | Customer | Public | `DOC` | V1 |
| `UX-06` | Informational / trust pages | Anyone | Public | `DOC` | V1 |
| `UX-07` | Request composer (progressive intake) | Customer | Public | `LOCAL` + `I-1`, `I-2` | V1 |
| `UX-08` | Request review and send | Customer | Public | `DOC` | V1 |
| `UX-09` | Identity and channel verification | Customer | Transitional | `DOC` | V1 |
| `UX-10` | Request submitted confirmation | Customer | Auth | `DOC` | V1 |
| `UX-11` | My requests (customer) | Customer | Auth | `DOC` | V1 |
| `UX-12` | Request detail: response and conversation (customer) | Customer | Auth | `LOCAL` | V1 |
| `UX-13` | My drafts | Customer | Auth | `DOC` | V1 |
| `UX-14` | Outcome capture | Customer | Auth | `LOCAL` | V1 |
| `UX-15` | Account and settings | Any | Auth | `DOC` | V1 |
| `UX-16` | Sign in / sign up | Any | Public | `DOC` | V1 |
| `UX-17` | Provider onboarding (progressive) | Provider | Auth | `LOCAL` + `I-1`, `I-3` | V1 |
| `UX-18` | Provider workspace home / request inbox | Provider | Auth | `DOC` | V1 |
| `UX-19` | Provider request detail and respond | Provider | Auth | `LOCAL` | V1 |
| `UX-20` | Provider conversation | Provider | Auth | `LOCAL` | V1 |
| `UX-21` | Profile editor | Provider | Auth | `LOCAL` | V1 |
| `UX-22` | Offering editor (category attributes, service area, `RequestIntake`) | Provider | Auth | `LOCAL` + `I-1` | V1 |
| `UX-23` | Media manager | Provider | Auth | `LOCAL` + `I-3` | V1 |
| `UX-24` | Publication state and requirements checklist | Provider | Auth | `DOC` | V1 |
| `UX-25` | Provider settings and notifications | Provider | Auth | `DOC` | V1 |
| `UX-26` | Operator case queue index | Operator | Auth | `DOC` | V1 |
| `UX-27` | Report / moderation case detail | Operator | Auth | `LOCAL` | V1 |
| `UX-28` | Publication review queue | Operator | Auth | `DOC` | V1 |
| `UX-29` | Duplicate suspicion and ownership claims | Operator | Auth | `DOC` | V1 (claims empty unless a cohort is approved) |
| `UX-30` | `CategoryProposal` review | Operator | Auth | `DOC` | V1 |
| `UX-31` | Geocode / `undetermined` / eligibility exceptions | Operator | Auth | `DOC` | V1 |
| `UX-32` | Delivery-attempt failure queue | Operator | Auth | `DOC` | V1 |
| `UX-33` | Staleness and `RequestIntake` decay / reactivation | Operator | Auth | `DOC` | V1 |
| `UX-34` | Audited private-content access | Operator | Auth | `DOC` | V1 |
| `UX-35` | Notification message (email body, allowlist-bound) | Any | Channel | n/a | V1 |
| `UX-36` | Legacy profile claim | Provider | Auth | `DOC` | `FUTURE` / conditional |
| `UX-37` | Sponsored placement surfaces | Operator / Provider | Auth | — | `FUTURE` |
| `UX-38` | Booking / payment / payout surfaces | — | — | — | `FUTURE` |
| `UX-39` | Report intake and block confirmation | Customer / Provider | **Auth** | `LOCAL` | V1 |

**`UX-39` was added during adversarial review**, which found report and block drawn as actions on anonymous public surfaces with no surface record behind them. `domain-model.md` requires a `Report` to come from an authenticated participant, so the public affordance routes through `UX-16` and the report is composed on `UX-39`. That is a deliberate authentication requirement on a reporting action — **not** a login wall on discovery, and it bounds `UX-27`'s queue, which anonymous intake would leave unbounded by design.

## 7. House style

1. H1 with a scope suffix. 2. Blockquote status block immediately after the H1. 3. A framing paragraph stating what the document is **not**. 4. H2 sections, H3 sub-sections; tables as the primary content form. 5. Register tables lead with a stable `| ID |` column; IDs are never reused or renumbered. 6. Fenced ```text blocks for journey chains with `→` arrows and caps terminal markers. 7. Every enum value, ID, entity, state, and evidence label in backticks; **bold** for the load-bearing prohibition inside a cell. 8. Documents end with a constraint, gate, or pointer — never a recap. 9. English only for internal documents; bilingual only for owner-facing artifacts. 10. No volatile metadata near the top; dates in an italic footer after a `---` rule. 11. No emoji; sentence case in table headers; cross-file references as backticked relative paths. 12. **Do not fabricate ratings, reviews, prices, response times, traffic, or usability findings.** Where a number is wanted, state the measurement that would produce it.

---

*Record dates — canon authored 2026-08-12 at the start of P04 and persisted at the end of it, amended in place for `UX-39` and for the wording corrections raised by the two adversarial reviews. It records the brief, not the outcome; where a specialist document differs, that document is the later word.*
