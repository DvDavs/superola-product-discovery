# Decision Branch Register — P02

> **Status:** `PROPOSED`. Provenance: `TECHNICAL_DISCOVERY` over a `DAVID_DIRECTIVE` design envelope.
>
> **Standing qualifier:** `DAVID WORKING ASSUMPTION — OWNER VALIDATION PENDING` (`SRC-013`).

## Purpose

P02 produced **one** recommended architecture, not several. This register records the owner alternatives that would change it, so David can answer the only question that matters before the owner has decided:

> *"If the owner chooses X instead, these are the parts that change — and these are the parts that do not."*

**This is not a set of parallel architectures.** Each branch states the primary working assumption P02 designed against, the live alternative, and the reversal cost. A branch is not a plan to build both.

## The Working Decision Envelope

Recorded verbatim in substance as `DAVID WORKING ASSUMPTION — OWNER VALIDATION PENDING`; provenance `DAVID_DIRECTIVE`; source `SRC-013`. It is **not** owner-confirmed and does **not** satisfy the P01.1 release gate as an owner decision. It satisfies that gate's alternative clause — an explicitly documented bounded scenario set David accepts as the design envelope — and this file is the "consequences recorded" half of that clause.

Envelope items, labelled `E1`–`E10` for reference below:

| ID | Envelope item |
|---|---|
| E1 | Preserve the essential marketplace function: customers discover, evaluate, and contact providers; providers respond; both sides can progress toward a real commercial relationship |
| E2 | Working launch geography United States plus Mexico, English plus Spanish; architecture must permit more countries and locales without structural redesign; extensibility is separate from launch operational scope |
| E3 | Broad event-service category growth, not musicians only; five representative archetypes; shared invariants plus category-specific extensibility; do not pre-model every category |
| E4 | Customer expresses or searches a need; Superola returns relevant providers; the customer deliberately chooses whom to contact; natural-language interpretation may later translate intent into structured constraints; the model is never the source of truth for eligibility or ranking |
| E5 | One customer-selected provider per request in V1; no automatic broadcast or fan-out; multi-provider matching is a future branch |
| E6 | Phase 1 is Marketplace Leads; a transaction extension is potential; P02 designs Phase 1 and lets the extension influence boundaries only where avoiding future structural redesign is reasonable |
| E7 | Legacy and new platform may coexist during transition; audit, then usable cohort, then staged migration or reactivation, then claim where lawful, then redirect and cutover; not all records usable; full migration is not a V1 prerequisite; P05 decides |
| E8 | Growth first: free provider onboarding, subscription once attributable value is observable, sponsored placement later; architecture must not prevent them and must not carry their operational complexity |
| E9 | Web first, native mobile later; do not make future mobile structurally difficult; no mobile-specific architecture without evidence |
| E10 | Internal AI-assisted discovery optional; external agent actions future; preserve machine-legible domain semantics and clean application boundaries without protocol-specific services |

## Reconciliation against the P01 canonical record

The envelope is not fully consistent with the repository it designs against. These are the contradictions P02 found, and the resolution each artifact adopts.

| Envelope item | P01 canonical position | Severity | Resolution P02 adopted |
|---|---|---|---|
| E2 — two countries at launch | `OR-003` and `docs/05-roadmap/mvp-scope.md`: **one** owner-approved geography for the costing and learning baseline; United States and Mexico priority is an unvalidated assumption derived from historical reach | HIGH | Split into two separately labelled constructs — architecture capability envelope versus launch operational scope — with two named scenarios (`S-1`, `S-2`) and consequences recorded (`DB-04`). **Every P02 artifact stamps the scenario it assumes.** The capability requirement is derived from `docs/00-context/product-context.md` and `AGENTS.md`, **not** from the launch claim, so an owner reversal on geography does not touch the model |
| E2 — two locales at launch | `OR-004`: **one** production locale at launch; additional locales are an explicit gated expansion | HIGH | Same split — plus the finding that locale count is **not purely operational**, because provider-authored bilingual content is a content-model question (`A-015`, `Q-020`). New assumptions `A-015`, `A-016` |
| E3 — "broad event-service growth" at launch | `OR-002`: start with the **smallest coherent** category archetype | MEDIUM | "Broad" is an open range, not a bounded set, so it cannot satisfy the gate clause. The five archetypes become the **design pressure set** — the corpus that validates the model — which `docs/01-product/feature-inventory.md` already sanctions. The launch cohort stays `OR-002` OPEN with two bounded scenarios (`DB-05`). New assumption `A-017` |
| E3 — "shared invariants plus category-specific extensibility" | `docs/01-product/feature-inventory.md` explicitly reserves a **three-way comparison** for P02: narrow launch-category model, shared core with extensions, governed metadata or configuration | MEDIUM-HIGH | **The sharpest methodological contradiction: the envelope pre-selects one of the three alternatives P01 chartered P02 to compare.** P02 ran the comparison anyway (`domain-model.md` §3) and recorded the outcome as `ADR-007` with a reconsideration trigger. The envelope's phrasing is registered as `A-018`, a design preference, not a given |
| E6 — "V1 Phase 2" | `OR-005`, `OR-014`, `docs/01-product/user-journeys.md`: the transaction cluster is a **separate later slice**, explicitly deferred and owner-gated, not a scheduled phase of V1. `A-004` is OPEN | HIGH | Naming, not substance — the contents match `user-journeys.md` almost verbatim. But calling it "V1 Phase 2" converts an unapproved owner-gated scope decision into a scheduled one, which silently resolves `A-004`. **Renamed throughout to `Transaction Extension` — `FUTURE`, separately owner-approved. `A-004` remains OPEN. No P02 artifact uses the phrase "V1 Phase 2" to name the scope — it appears only in quoted references to the brief's own wording while explaining this rename** |
| E6 — "influence boundaries where avoiding future redesign is reasonable" | The anti-inflation rule: a foundation belongs in V1 only if it already serves V1; future concepts need no V1 mechanism | MEDIUM | **Admissibility test adopted** (`system-architecture.md` §6): a future-motivated boundary enters P02 only if independently justified by a Phase-1 need, with that justification written next to it. Otherwise it is a named future concern with no V1 mechanism. Registered as `A-020`, `Q-022` |
| E1 — "progress toward a real commercial relationship" | `docs/01-product/product-vision.md` and `mvp-scope.md`: preserve intent, response, and outcome **without claiming a transaction Superola did not execute** | MEDIUM | Not a substantive contradiction — *progress toward* is not *complete*. It is a live terminology hazard, because Superola has **no evidence source** for a commercial relationship. **No domain concept named relationship, deal, engagement, or conversion exists.** The sole V1 outcome type is `ReportedOutcome` with reporter, time, and self-declared basis |
| E1 — "contact/request" as the customer action | `mvp-scope.md`: alternative **A** is the discovery-and-contact directory trap; **B0** has exactly one initiation path, the request. Contact disclosure is unresolved (`OR-011`, `A-010`) | MEDIUM | Read as two capabilities, P02 would design a contact-reveal path P01 deliberately left open. **Exactly one initiation path is modelled: the request.** "Contact" is not a separate V1 capability; contact-detail disclosure stays open policy behind the §5 disclosure seam. New question `Q-023` |
| E7 — the audit-to-cutover pipeline as decided | `mvp-scope.md` approval gate 3 requires **no migration / fresh onboarding** to remain a live alternative; claim capability "is not assumed" | MEDIUM | The pipeline silently drops the fresh-only branch. **Fresh onboarding is the sole V1 baseline** (`DB-03`); import provenance is a separable attribute; claim is an extension point with no V1 mechanism unless a cohort is approved |
| E7 — "may coexist during transition" | **No P01 position exists.** `docs/02-architecture/architecture-overview.md` lists coexistence as a deferred architecture question | `EXTENDS` — new, not contradictory | Genuinely new and structural: dual-run forces decisions on provider identity ownership, canonical URL ownership, redirect authority, and cutover trigger. New assumption `A-019`, new question `Q-019`. **Do not let coexistence become an implicit requirement** |
| E8 — "must not prevent" versus "must not carry complexity" | `mvp-scope.md` forbids an entitlement engine; `architecture-overview.md` principle 6 says model monetization and entitlements explicitly | LOW | Resolved by locating **one seam**, not building a system: publication and request eligibility is the decision point that can later read an entitlement. Ordering locked as an invariant: trust and quality gate, then eligibility, then a future entitlement — **never entitlement before publication** |
| Envelope **silence** on availability | `G-06` is BLOCKING; `A-006` is `SUPERSEDED`; availability must not be reduced to an unconditional boolean | MEDIUM (omission) | The envelope does not cover `G-06` and P02 **must not fill the gap.** Only `RequestIntake` is modelled, with the non-claim stated verbatim. `V1 has no availability model.` `Q-007` remains the P04 blocker |
| E10 — "machine-legible semantics" | Human-public publication does not authorize crawling; structured-data markup is conditional; no protocol-only deliverable | LOW | Machine-legible is **not** a public contract, feed, markup, or approved indexability. P02 preserves clean identifiers and stable public meanings only; indexability stays an unapproved policy gate (`Q-015`) |

## Release gate status at P02 start

Recorded so no artifact implies the gate was met. Assessment method: each gate is tested for a named alternative set of at most two, **and** recorded consequences.

| Gate | Status | Residual risk P02 carries |
|---|---|---|
| `G-01` Primary outcome | **PARTIAL**, and it is in the matrix's explicit "do not start" set. `E1` restates a `PROPOSED` position and adds no owner authority. The clause's two-alternative requirement is supplied below rather than by the envelope | The owner may define success as bookings or revenue, which moves the boundary and flips `A-004`. The observable set is unchanged, so the model holds |
| `G-02` V1 stopping boundary | **SATISFIED in substance** | The "V1 Phase 2" framing contaminated `A-004`; renamed. "Avoid future redesign" can smuggle transaction machinery into Phase 1; the admissibility test blocks it |
| `G-03` Request boundary | **SATISFIED** under the clause — two named alternatives with consequences | `A-011` unvalidated; `R-014`. Owner-required fan-out brings routing, consent, caps, windows, closure, deduplication, and reroute |
| `G-04` Category archetypes | **PARTIAL.** Archetype set yes; launch cohort no — "broad" is an open range | Designing for five archetypes at once is the `R-015` universal-schema trap; launch cost and capacity claims become unbounded |
| `G-05` Geography and language | **PARTIAL**, and cheapest to fix. One scenario supplied; the second and the consequences were missing until this file supplied them | Bilingual provider-authored content is a domain question, not an operations scale-up. `R-003`; `A-002` OPEN |
| `G-06` Availability claim | **UNSATISFIED. The envelope is entirely silent** | HIGH if P02 invents an availability model. The gate's own fallback: must be resolved before P04 designs profile and request surfaces |
| `G-07` Identity boundary | **PARTIAL.** `E7` touches claim; nothing addresses the customer identity boundary | If unidentified guests may send requests, request ownership, reachability, consent, and abuse controls all change. Timing stays a P04 comparison |
| `G-08` Monetization direction | **SATISFIED** | "Must not prevent" drifting into "build the seam now". Bounded by the seam definition |
| `G-09` Legacy expectation | **PARTIAL**, and in the "do not start" set. The gate's **permission** component — whether permission to contact or migrate exists — is an owner-plus-legal fact **David cannot supply** | If broad migration becomes a launch precondition, provider identity, claim and dispute, deduplication, and taxonomy normalization become V1-core, and P05 becomes a P02 dependency rather than a parallel track |
| `G-10` Material status | **UNSATISFIED.** `SRC-003` and `SRC-004` remain NOT RECEIVED; only the owner can classify commitments | `R-009`. A domain model is the artifact most robust to a late feature document, so P02 proceeds; **P04 does not** |

**Summary: satisfied — `G-02`, `G-03`, `G-08`. Partial — `G-01`, `G-04`, `G-05`, `G-07`, `G-09`. Unsatisfied — `G-06`, `G-10`.**

### `G-01`'s alternative set, supplied here because the envelope did not

The gate clause requires at most two named alternatives with consequences recorded. For `G-01` the envelope supplied only one position, so P02 supplies the second:

| Alternative | Consequence for the architecture |
|---|---|
| **A — Success is a useful provider response and a confident customer decision** *(the position P02 designed against)* | The only measurable outcome is `ReportedOutcome`, self-declared, with reporter and time. Demand's terminal states are reported states. No transaction context exists. This is the current design. |
| **B — Success is a completed booking, or revenue** | `A-004` flips. `ReportedOutcome` stops being the terminal signal and becomes an intermediate one. A booking event with a defined meaning becomes required (`Q-014`), a Transaction context appears, "review" and "verified" acquire new meanings, and the consistency and audit regime changes. This is `DB-02`, and P02's boundary set would be **wrong rather than merely incomplete**. |

### Honest limits of this authorization

Two things the clause does **not** cover, stated so no reader concludes otherwise:

- **`G-01` and `G-09` are both in the matrix's "do not start" set.** The "readiness test, not an authorization" sentence means the gate does not itself authorize — David must. It does **not** license overriding the do-not-start clause. P02 proceeded on David's explicit authorization, which is a decision recorded here, not a gate satisfaction.
- **`G-09`'s permission component cannot be satisfied by any scenario set.** Whether permission exists to contact or migrate legacy records is an owner-plus-counsel fact. P02's mitigation is to make that answer's absence structurally safe: fresh onboarding is the sole baseline, and if the answer is no, the legacy boundary collapses to a suppression list.

**This authorization does not carry P04.**

## Load-bearing ranking

Ranked by domain-model blast radius on reversal, not by importance. P02 spent its design risk budget on Tier 1 and deliberately spent none on Tier 3.

| Tier | Branches | Meaning |
|---|---|---|
| **Tier 1 — reversal rewrites aggregates or invariants** | `DB-01`, `DB-02`, `DB-05` (the *model* half), `DB-03` + `DB-11` | These reshape aggregates, add contexts, or make conditional capabilities core |
| **Tier 2 — reversal changes a bounded area** | `DB-09`, `DB-10`, `DB-12`, `DB-13`, `DB-14`, `DB-04` (the *locale* half), `DB-07`, `DB-08` | Bounded, and the seams already absorb most of the change |
| **Tier 3 — launch scope and delivery only, near-zero domain radius** | `DB-04` (the *country* half), `DB-05` (the *cohort* half), `DB-06` | Cost, capacity, compliance, and staffing — the model holds either way |

---

## DB-01 — Direct request versus multi-provider matching

| Field | Content |
|---|---|
| **Decision** | Does a customer request reach exactly one deliberately chosen provider, or may one expressed need be distributed to several eligible providers? |
| **Primary working assumption** | Exactly one customer-selected recipient per request, with an independent lifecycle. Contacting another provider is another deliberate request from a reusable private draft, with fresh confirmation. No broadcast, fan-out, recipient set, shared response window, auto-closure of siblings, or reroute. |
| **Alternative** | Multi-provider matching: one customer brief distributed to N eligible providers, with routing policy, recipient provenance, fan-out caps, shared response windows, closure, and deduplication. |
| **Why primary was selected** | `A-011` and `mvp-scope.md`: owner evidence supports provider discovery and communication but does not establish routing consent, eligibility, fan-out, deduplication, rerouting, or the operating capacity matching requires. It also lets Superola learn provider responsiveness per deliberate selection without confounding it with routing quality. `E5` confirms. |
| **Product impact** | The customer does comparison work deliberately. The provider receives fewer, self-selected requests. Response rate is attributable to the provider, not to routing quality. |
| **Domain impact** | The recipient is a **single reference**, part of request identity — **deliberately not a collection of size one** (`domain-model.md` §9). No routing policy entity, no provider request-preference entity, no fan-out counters. Conversation is one per request. Outcome is per request. |
| **UX impact** | Discovery to profile to request is one deliberate path. Multiple contacts are repeated deliberate actions from a draft. No "get quotes from five vendors" surface. **Empty results must never silently become a broadcast.** |
| **Infrastructure impact** | No routing or fan-out execution, no notification burst control, no deduplication service. Notification volume is roughly one per request. |
| **Cost / complexity direction** | **Materially lower.** The alternative adds routing operations, abuse limits, notification pressure, provider preference management, and local-liquidity operations. |
| **What would need to change** | The draft becomes the brief; a **separate routing concept** appears with its own consent model, recipient provenance, caps, windows, closure, deduplication, and reroute; provider request preferences and limits become entities; conversation becomes per recipient; outcome attribution spans recipients; measurement re-keys from per-request to per-brief. |
| **What would remain unchanged** | Identity & Access, Provider, Catalog, Geography, Discovery, offer content semantics, conversation content model, notification delivery model, Operations, Audit, Analytics event families (re-keyed), and the monetization seam. |
| **Trigger for reconsideration** | Measured near-zero provider response rate on direct requests in a launch cohort; customers repeatedly creating multiple deliberate requests for one need; provider demand for inbound flow they need not be found for; or an owner decision. |

## DB-02 — Phase 1 only versus transaction required immediately

| Field | Content |
|---|---|
| **Decision** | Does the first release stop at a customer-reported outcome, or must it include booking commitment and money movement? |
| **Primary working assumption** | Phase 1 only. The **Transaction Extension** — booking, deposit and payment, payout, cancellation, refund, dispute, completion, transaction-derived review — is `FUTURE` and separately owner-approved. Boundaries are placed so it can attach without structural redesign; **no payment operations are designed.** |
| **Alternative** | A transactional marketplace at V1 — P01's option C. |
| **Why primary was selected** | P01 compared three slices and recommended B; `A-004` is OPEN; the transaction cluster is a **coupled compliance-and-operations cluster** that two jurisdictions multiply; owner evidence does not establish it; and V1's job is to learn whether the exchange happens at all. |
| **Product impact** | Superola records intent, not transactions. No verified booking. No transaction-derived review (`Q-014`). |
| **Domain impact** | The offer is terminal in V1 — an offer, not an accepted commitment. **V1 has no accept transition that creates an obligation.** No booking, payment, payout, refund, dispute, or review entity, field, relationship, or diagram box. The one carried seam is that the offer has a stable identifier and immutable versions, so a future commitment can reference a specific version. `EventContext` remains a value object. |
| **UX impact** | The journey ends at the outcome prompt. No checkout, deposit, payout onboarding, or dispute surfaces. Nothing may imply a reservation, and **no money-shaped language** may appear in public pages or notifications. |
| **Infrastructure impact** | No payment processor, no card-data scope, no payout identity, no ledger or reconciliation, no tax handling, no chargeback handling. |
| **Cost / complexity direction** | **Dramatically lower.** The alternative adds the largest single cost cluster in the programme plus per-market legal work. |
| **What would need to change** | A new Transaction context depending on Demand — and **Demand must never depend on it**; booking, payment, payout, refund and dispute, completion, and review entities; strong-consistency money boundaries and idempotent external callbacks; payout identity as a **separate verification subject** from marketplace identity; tax and invoicing per market; refund and dispute operator workload; the trust model changes because verified events become possible; monetization may shift from subscription to transaction fees; `Event` becomes a necessary aggregate; and availability may need to become reservable, coupling to `DB-10`. |
| **What would remain unchanged** | Identity & Access, Provider, Catalog, Geography, Demand's request and response lifecycle, Conversation, Notification, Operations intake, Audit, and Analytics families. |
| **Trigger for reconsideration** | An owner requirement; measured high proceeding rate with evidenced off-platform payment leakage; customer or provider demand for platform-mediated commitment; a validated fee model. |

**This is the single highest-leverage question outstanding.** Several P02 boundaries move together on this one answer, and David cannot decide it (`G-02`, `A-004`).

## DB-03 — Curated legacy cohort versus broad migration

| Field | Content |
|---|---|
| **Decision** | Does the platform launch on a curated audited legacy cohort plus fresh onboarding, on fresh onboarding only, or on broad migration of the reported registrations? |
| **Primary working assumption** | **Fresh onboarding is the sole V1 baseline.** A curated cohort is conditional on audit plus lawful basis. Coexistence during transition. Full migration is not a V1 prerequisite. P05 decides. |
| **Alternative** | (a) Broad migration at cutover. (b) Zero legacy import ever — which `mvp-scope.md` approval gate 3 requires stay live and the envelope silently dropped. |
| **Why primary was selected** | `A-001`, `A-007`, `A-009`, `R-001`, `R-010`. The count is owner-reported and unaudited. **The three fields that matter most for discovery — category, location, contact — are the three the owner reports as broken**, so a record with untrustworthy category and location has no discovery value. Cost per publishable provider is review cost divided by an **unknown yield**, and that denominator is the whole risk. |
| **Product impact** | Launch supply may be small and cold-start risk stays, but published supply is credible. Broad migration would fill result pages with unreachable stale providers, making requests a worse experience than a directory (`R-016`). |
| **Domain impact** | `LegacyProviderRecord` is a distinct entity, **structurally not a `ProviderProfile`**, therefore incapable of appearing in discovery or receiving a request. Provenance per record **and per field**, lawful-basis marker, cohort marker, and separate gates. Claim **binds ownership; it does not publish** — it seeds a new `Draft` profile through the same governed commands a fresh provider uses. Suppression checked **before** record creation. |
| **UX impact** | Claim exists only for an approved cohort. Unclaimed records never appear as participating. A provider re-onboarding path exists. |
| **Infrastructure impact** | Import, rehearsal, reconciliation, and rollback in a controlled environment, **outside** the application boundary. Coexistence needs URL and redirect strategy at cutover. **No runtime read-through, proxy, or dual-write to the legacy platform, in either direction.** |
| **Cost / complexity direction** | Curated: bounded and measurable per publishable provider. Broad: **HIGH** — manual review time dominates and scales with volume; taxonomy mapping is per distinct legacy string, not per record; media rights review is per asset, not per provider; plus privacy exposure. **The strongest argument is not cost: migration risk is measurement risk. A bad import can make a working product look broken.** |
| **What would need to change** | Migration becomes a programme with per-record review economics; taxonomy mapping needs automatic-plus-exception at scale; the operator queue grows greatly; a consent or re-consent campaign appears; moderation load rises; publication gates may need bulk workflows; cutover risk and rollback complexity rise; duplicate resolution may need a subsystem rather than manual exception handling; and **P05 becomes a P02 dependency rather than a parallel track.** |
| **What would remain unchanged** | The domain model of Business, Profile, Offering, Catalog, and Geography; the publication gate concept; the claim concept; and the provenance requirement — which becomes **more** important, not different. |
| **Trigger for reconsideration** | Measured cost per publishable, reachable, request-eligible imported provider **below** measured cost per freshly acquired provider of equivalent quality; **and** lawful basis and reachability cleared; **and** operator review capacity available without breaching the backlog-age stop rule. Both cost figures come from measurement, not estimation. |

## DB-04 — Two countries and two locales versus a narrower first launch

| Field | Content |
|---|---|
| **Decision** | Is the first operational launch two countries and two languages, or one bounded geography and locale with the architecture still multi-country capable? |
| **Primary working assumption** | **Two separately labelled constructs.** *Architecture capability envelope:* multi-country and multi-locale capable, justified by `docs/00-context/product-context.md` and `AGENTS.md` independent of any launch claim. *Launch operational scope:* **OPEN**, expressed as `S-1` (one geography, one locale — assumed by every P02 artifact) and `S-2` (two of each). |
| **Alternative** | (a) `S-2` as launch scope. (b) The stricter reading that the architecture need not carry country and locale abstraction at all in V1. |
| **Why primary was selected** | `OR-003` and `OR-004` specify one geography and one locale for the costing baseline. Country and locale abstraction is **near-zero when done as data and sometimes unrecoverable when retrofitted** — ambiguous historical timezone values cannot be reconstructed, and history never recorded segmented cannot be segmented. But operational two-locale support is **not** cheap, and that is a launch-scope decision. `R-003`. |
| **Product impact** | Which markets and languages appear at launch; support coverage; whether provider-generated content is multilingual. |
| **Domain impact** | **None under either answer — that is the point of the abstraction.** Country, locale, currency, timezone, and units are attributes and policy data, never code branches. Categories have language-neutral identity with localized labels. Addresses are country-shaped. No launch-country literal appears in any entity. |
| **UX impact** | Locale selection and content strategy; bilingual versus monolingual surfaces; formatting; address input per country; possible per-market taxonomy label divergence. |
| **Infrastructure impact** | **Multi-country data and policy is not multi-region infrastructure.** No residency or multi-region requirement is implied; drawing a second region on a diagram purchases infrastructure with a marketing statement. Place-resolution coverage per country becomes a P03 question. |
| **Cost / complexity direction** | Structural extensibility: **NEAR-ZERO**. Operational two-locale launch: **MEDIUM-HIGH and recurring** — permanent content maintenance, bilingual operator staffing as a multiplier on every queue, translation of notification, legal, and policy text, and doubled content cost in the phase most likely to pivot, where every pivot re-translates. |
| **What would need to change** | Narrowing to `S-1`: **nothing structural** — launch configuration narrows. Expanding to `S-2` or beyond: per-market legal and privacy work, support staffing, a taxonomy localization programme, possibly market-specific categories, per-locale public URL strategy, and the provider-content-language decision. |
| **What would remain unchanged** | Every domain entity and boundary. |
| **Trigger for reconsideration** | An owner decision on launch geography and locale; measured demand by country; support capacity; P05 evidence about where usable legacy supply actually sits. |

**Finding not previously in the repository: locale does not follow country.** The owner-reported legacy category mix — mariachi, norteño, banda — is direct evidence of a Spanish-speaking segment **inside the United States**. The launch locale may be Spanish even if the launch geography is the United States. Do not assume `country → locale` (`Q-020`, `A-015`).

## DB-05 — Representative archetype cohort versus all categories configured immediately

| Field | Content |
|---|---|
| **Decision** | Does V1 configure a bounded archetype cohort, or all event-service categories at once — and separately, which variability *model* does the architecture use? |
| **Primary working assumption** | Two separable halves. *Model:* the extensibility mechanism must handle all five archetypes — that is the design test — using shared core plus typed archetype extensions, with governed metadata confined to descriptive and filterable attributes (`ADR-007`). *Configured launch cohort:* an owner and operational decision, minimum one archetype, `OR-002` OPEN. |
| **Alternative** | (a) A narrow single-archetype model hard-coded. (b) All categories configured at launch with governed metadata defining behaviour. |
| **Why primary was selected** | `R-015`, `A-013`, and `docs/01-product/feature-inventory.md` prove the archetypes differ materially. Designing only for performers produces a model that breaks on venues (fixed location, room inventory) and transportation (route corridor). Configuring everything at launch multiplies taxonomy governance, content quality, and operator burden without liquidity evidence. **Adding a Category becomes a governance act; adding an Archetype becomes an engineering act** — that is the correct cost curve, and both alternatives get it wrong in opposite directions. |
| **Product impact** | Which categories customers can search at launch; whether the model absorbs a new category without a code change. |
| **Domain impact** | **This is the model decision, and it is Tier 1.** `CategoryArchetype` becomes first-class between Category and Offering. Categories carry language-neutral identity and governed lifecycle. Attribute definitions are typed, versioned, and never provider-writable. Service-area semantics vary by archetype. Filterable versus descriptive is explicit, per-`Market`, and revocable. Provider input can only ever be a `CategoryProposal`. |
| **UX impact** | Category-specific request and profile forms; category-appropriate offer surfaces; filters differ per category; search must degrade gracefully across mixed-category results. |
| **Infrastructure impact** | Filterable attributes drive query requirements; more configured categories means more distinct filter shapes and earlier pressure on search capability. Route-corridor semantics do not reduce to a containment predicate — which is an argument for excluding transportation from launch, not for buying search infrastructure. |
| **Cost / complexity direction** | The mechanism is **the largest carried cost in V1**, then low per category. Configuring all categories at launch: HIGH taxonomy, content, and operator cost with no liquidity proof. Taxonomy governance is the **highest-ranked operator cost driver** in the product. |
| **What would need to change** | All categories at launch: taxonomy governance becomes a programme; per-category definitions, labels per locale, and validation must all exist; the operator queue grows; the filter surface grows; onboarding forms multiply; the quality bar per category becomes harder to hold. A narrow model instead: the archetype layer collapses and each added category costs a release. |
| **What would remain unchanged** | The extension mechanism itself; Business, Profile, Geography; Demand; publication gates. |
| **Trigger for reconsideration** | Measured demand for an unconfigured category, from zero-result searches by category; an owner breadth requirement; supply availability in a category; operator capacity. **For the model:** a launch archetype appears whose eligibility semantics cannot be evaluated and tested within the approved engineering budget; two categories inside one archetype needing different *eligibility* semantics; operators blocked on engineering beyond an agreed rate; or a launch category expressible by no archetype and unable to justify a new one — in which case descope the category rather than bending an archetype. |

## DB-06 — Web-first versus native mobile at launch

| Field | Content |
|---|---|
| **Decision** | Web only at launch with mobile later, or a native or cross-platform mobile client at or near launch? |
| **Primary working assumption** | Web first. Domain semantics stay independent of the delivery channel, so a mobile client is an additional client rather than a rewrite. No mobile-specific architecture without evidence. |
| **Alternative** | Native or cross-platform mobile at or near launch (`A-005`). |
| **Why primary was selected** | `A-005` is OPEN with no user evidence. Web is also the public discovery surface that carries the acquisition hypothesis. Mobile adds channel scope, push consent and infrastructure, store release cycles, and duplicated interaction work. |
| **Product impact** | Acquisition via public web; provider management on web; the notification channel is email-class rather than push. |
| **Domain impact** | **Essentially none, provided the channel-independence principle holds** — the application layer must expose governed use cases, not page-shaped operations. That is the whole seam, and it is the discipline you want regardless. Push would add a device and consent concept to Notification. |
| **UX impact** | Responsive web must serve providers who are likely mobile-first in practice — performers working from phones. That is a P04 concern and a real risk even with no native app. |
| **Infrastructure impact** | No store pipelines, no push infrastructure, no device registry at V1. |
| **Cost / complexity direction** | Lower. The alternative adds a parallel client, a release process, and a push channel with its own consent and deliverability cost. |
| **What would need to change** | A device and push channel in Notification with consent, token lifecycle, and per-device delivery state; possible offline expectations; store compliance; a versioned external contract; mobile authentication flows. |
| **What would remain unchanged** | Every domain module — **and the application layer, if it was genuinely channel-independent. That is the test of whether the principle was honoured.** |
| **Trigger for reconsideration** | Measured provider and customer mobile behaviour; an owner requirement; push shown necessary for response latency; store distribution as an evidenced acquisition channel. |

## DB-07 — Free-growth phase versus subscription required immediately

| Field | Content |
|---|---|
| **Decision** | Free provider onboarding with monetization staged later, or subscription from launch? |
| **Primary working assumption** | Free onboarding. Subscription becomes relevant once providers can observe attributable value. V1 carries a monetization **seam**, not an entitlement system. Sponsored placement is later. |
| **Alternative** | Charge at registration or publication — which `docs/01-product/monetization.md` already records as `REJECTED` for the initial hypothesis — or a fixed trial-then-charge at launch. |
| **Why primary was selected** | `A-003`, `A-012`, `R-017`. `monetization.md` compared four approaches and staged. Charging before value confounds willingness-to-pay with cold-start and legacy quality. And the trial clock must not start until a provider is publishable **and** receiving relevant demand — meaning the clock depends on marketplace measurements that do not exist yet. |
| **Product impact** | No paywall. Provider eligibility is trust- and quality-gated, never payment-gated. Value measurement events exist so a later gate can be evaluated honestly. |
| **Domain impact** | The seam is exactly three facts, all required anyway: **one** `EligibilityDecision` point consulted by Provider and Discovery, with a constant entitlement input; `placementBasis` carried end to end; and per-provider value events. `Business` is the entitlement subject and already exists. **No plan, price, tier, trial, proration, or entitlement data — and no empty structures "for later".** |
| **UX impact** | No billing surfaces. Provider value and activity visibility becomes important instead — that *is* the eventual willingness-to-pay driver. Later, plan and trial surfaces. |
| **Infrastructure impact** | No billing provider, invoicing, tax handling, dunning, or proration at V1. |
| **Cost / complexity direction** | Much lower. The alternative adds billing, tax, refund, and support plus churn operations before there is anything to sell — and billing disputes are the most support-intensive case class in this product, arriving in two languages under `S-2`. |
| **What would need to change** | Subscription, plan, and entitlement entities; billing integration and its failure modes; tax and currency per market, coupling to `DB-04`; dunning, downgrade, cancellation, and reactivation policy; support load; entitlement inputs at the eligibility point; trial, conversion, and churn measurement. |
| **What would remain unchanged** | The eligibility decision **location**, if the seam was honoured; Discovery; organic ranking independence; Provider; Demand; Conversation; Operations. |
| **Trigger for reconsideration** | All of `monetization.md`'s V1.x gate conditions **observed as data**: cohort supply and demand health; providers able to observe *attributable* value rather than impressions; measurable trial activation, conversion, retention, and churn; plus owner validation of package intent. |

## DB-08 — Deterministic search versus AI-assisted search on the launch critical path

| Field | Content |
|---|---|
| **Decision** | Is discovery governed deterministic search with natural-language interpretation as an optional non-critical front end, or is AI-assisted search on the launch critical path? |
| **Primary working assumption** | Deterministic governed search is authoritative and on the critical path. Natural-language interpretation, if enabled, translates expressed intent into **structured constraints validated against governed data**, and may produce nothing the deterministic form cannot. The model is **never** the source of truth for eligibility or ranking. Any experiment is separately approved, budgeted, measurable, and stoppable with a deterministic fallback. |
| **Alternative** | AI-first or semantic search as the primary discovery mechanism at launch. |
| **Why primary was selected** | `R-019`, `R-012`, and `product-context.md`: natural-language intent may augment but must not replace a sound search foundation. Eligibility and ranking must be auditable and explainable — especially once sponsorship exists, because an unauditable ordering cannot prove organic and paid separation. Plus per-query variable cost, latency, unproven quality baseline, and unproven multi-locale quality. |
| **Product impact** | Search behaviour is explainable and reproducible; zero-result behaviour is honest; natural language becomes an entry-point convenience whose failure degrades to structured search rather than to nothing. |
| **Domain impact** | `SearchIntent` — a structured constraint set — is a first-class concept distinct from raw query text and from a request. Eligibility and ranking are governed decisions over authoritative data. The interpretation path produces a **candidate** constraint set that must be validated and is user-correctable; it never bypasses eligibility. **No module imports the experiment, and the dependency arrow points one way.** |
| **UX impact** | A structured search surface must exist and be sufficient alone. A natural-language entry is additive and must show the interpreted constraints so the customer can correct them. Interpretation failure must be visible, not silent. |
| **Infrastructure impact** | No model dependency on the critical path; no vector or semantic infrastructure at V1. If the experiment runs: a per-query cost ceiling and fallback-rate measurement. P03 question. |
| **Cost / complexity direction** | Deterministic: bounded and predictable. AI on the path: **the only candidate with unbounded marginal cost attached to a top-of-funnel action.** Search is the highest-volume action in a discovery product, cost scales with **traffic** rather than with revenue-bearing requests, and the stated acquisition priority is public traffic — exactly the low-intent, high-volume, high-abandon kind. That inverts the unit-economics curve. Plus an evaluation harness, prompt versioning, and a cost-amplification attack surface that makes rate limiting a launch blocker. |
| **What would need to change** | Search availability and latency become coupled to a model; eligibility and ranking auditability must be re-established some other way; cost per search becomes variable and demand-scaled; quality regressions become release-blocking with an evaluation harness; multilingual quality per locale must be proven; and a deterministic fallback still has to exist for outages — **meaning you build both anyway.** |
| **What would remain unchanged** | Governed taxonomy and geography; authoritative marketplace data; publication and eligibility rules; the organic and sponsored separation requirement; everything from the request onward. |
| **Trigger for reconsideration** | **A recorded corpus of real zero-result and abandoned queries whose failure is demonstrably *interpretive* rather than *coverage*-driven**, exceeding an approved share of search sessions. Nothing in the repository currently makes that distinction, and it is the entire question — if failures are coverage failures, the money belongs in supply acquisition, not in a model. Cheaper alternatives to try first: a governed synonym and alias table, and a guided structured entry flow. |

## DB-09 — Contact disclosure policy

| Field | Content |
|---|---|
| **Decision** | When, if ever, may customer and provider exchange direct contact details, and what must stay in-platform? |
| **Primary working assumption** | Interaction stays in-platform as asynchronous text attached to the request, with minimal disclosure. **The policy itself remains open** (`A-010`, `OR-011`, `Q-008`). |
| **Alternative** | (a) Never expose. (b) Expose after a defined request state. (c) Relay without exposing — the owner's own concept. |
| **Why primary was selected** | The in-platform preference is `DAVID_DIRECTIVE`; the relay concept is owner-reported; the combined policy is **not** owner-validated. P02 therefore does not pick — it makes the pick cheap. |
| **Product impact** | Whichever answer arrives, the customer's contact details are protected by default, and the marketplace keeps a thread it can moderate and measure. |
| **Domain impact** | **The seam: contact data is never an attribute of a request, a message, or a notification.** It is a party attribute resolved at delivery or render time through a **disclosure decision** evaluated per (recipient, channel, field, request state) and recorded. All three owner answers become the same structure with a different predicate. |
| **UX impact** | Notification bodies carry event type, public display name, and a link — never contact data, request text, event address or date, guest count, budget, or offer terms. **And Superola must not tell users that in-platform means contact-protected**, because free text defeats any policy. |
| **Infrastructure impact** | If relay is chosen, a relay identity per (party, request) becomes the resolved address of the same decision. No new subsystem. |
| **Cost / complexity direction** | The seam is **LOW**. Not having it is what is expensive: a contact field on the request read by the provider interface and the notification template means a policy change touches storage, indexes, templates, logs, **and every already-sent notification, which cannot be recalled.** |
| **What would need to change** | One predicate on the disclosure decision, plus a relay identity concept if relay is chosen. |
| **What would remain unchanged** | Everything else. |
| **Trigger for reconsideration** | An owner policy decision; measured off-platform leakage; a legal answer on notice or consent obligations for relay per market (`Q-030`). |

**Consequence worth stating: because of this seam, `OR-011` does not block P02 or P04** — conditional on P02 actually adopting it. Without the seam, `A-010` becomes a blocker retroactively.

## DB-10 — What "available" promises

| Field | Content |
|---|---|
| **Decision** | Does "available" or "accepting requests" mean a helpful indication, an invitation to ask, or a reservable commitment? |
| **Primary working assumption** | **`V1 has no availability model.`** The positive replacement is `RequestIntake`: a provider-controlled state of accepting, paused, or unconfirmed, where a stale `accepting` **decays to unconfirmed**. Date feasibility is resolved by the provider's response. The customer's desired date is request context only — never a filter, never a system claim. |
| **Alternative** | (a) Advisory provider-declared blackout ranges. (b) Date-filtered search. (c) Per-resource inventory per archetype. (d) Holds and reservations. (e) External calendar synchronization. |
| **Why primary was selected** | `A-006` is `SUPERSEDED` and the universal model `REJECTED`; `mvp-scope.md` defers date and resource availability, holds, realtime, and calendar sync; and **`G-06`/`OR-010` is an unresolved owner gate the envelope does not address.** A rejection without a stated positive replacement is an invitation to design one, so P02 states the replacement and stops. |
| **Product impact** | The platform makes no claim it cannot evidence. "Not accepting requests" is not date unavailability, and neither is worded as the other. |
| **Domain impact** | No date, calendar, hold, concurrency, or resource-availability field exists. The archetype **declares** its resource concept — team, room, person, production slot, vehicle — as descriptive metadata only, which is what makes a single universal boolean structurally impossible. |
| **UX impact** | Profile and request surfaces cannot promise dates. `Q-007` must be resolved before P04 designs those surfaces — the gate says so itself. |
| **Infrastructure impact** | None. Each step up the alternative ladder adds progressively: date-range storage and timezone semantics, then a discovery predicate, then per-archetype resource models with concurrency, then contention control and expiry, then per-vendor synchronization with token refresh and drift reconciliation. |
| **Cost / complexity direction** | NEAR-ZERO for the primary. The ladder runs LOW to HIGH, and the operator cost runs higher than the build cost at every rung — **stale availability suppresses demand invisibly, the provider then complains about having no leads, and the cause is undiagnosable from data.** Calendar synchronization failures are silent and produce confidently wrong availability. |
| **What would need to change** | An `Availability` context appears, resource-typed per archetype, plugging in as one more predicate in `EligibilityDecision` and in the request-creation eligibility policy. If it becomes reservable, contention and holds appear and the consistency map changes materially — coupling to `DB-02`. |
| **What would remain unchanged** | Catalog, Geography, Provider, Demand's request and response lifecycle, Conversation, Notification, Operations. The composable eligibility predicate set is the seam, at near-zero carry cost. |
| **Trigger for reconsideration** | **The recorded decline reason.** Measured share of provider declines attributed to date or resource unavailability, within the launch cohort. This is the cheapest high-value instrument in the product: it answers the availability question from Phase-1 data at no cost, and nothing in the repository currently proposed it. Or the owner's answer to `G-06` makes "available" a reservable commitment. |

## DB-11 — Profile claim of imported records

| Field | Content |
|---|---|
| **Decision** | Does V1 support claiming imported or preloaded provider records, or fresh onboarding only? |
| **Primary working assumption** | Fresh onboarding only in the V1 baseline. Claim is **conditional** on an audited lawful cohort and has **no V1 mechanism** unless one is approved. |
| **Alternative** | Claim as a launch capability, whether for a legacy cohort or for externally acquired records. |
| **Why primary was selected** | `OR-008` and `mvp-scope.md` already make it conditional. The structural reason goes further: **claim is an authorization grant over a record whose lawful basis for existing has not been established.** If the record should not be there, the grant is a second wrong layered on the first — and it is the one that becomes visible to a third party. External acquisition is separately gated by `R-006`. |
| **Product impact** | Slower cold start, but no risk of handing a stranger control of a third party's business data and its inbound demand. |
| **Domain impact** | Claim and evidence entities exist in Operations; the legacy record is a distinct entity that is structurally not a profile; claim **binds ownership and does not publish**, seeding a new `Draft` profile through the same governed commands. Competing claims never auto-resolve first-come. |
| **UX impact** | If enabled: a claim flow that **discloses nothing before the grant decision** — even masked contact hints are a leak across the imported set — plus a post-claim change window on material public fields, and a reversal path for the losing party. |
| **Infrastructure impact** | An operator adjudication queue. Failed-attempt counters per record and per actor, which is the only thing that detects claim farming or enumeration. |
| **Cost / complexity direction** | **Zero if no cohort is approved — the claim queue does not exist.** If approved: MEDIUM, and inherently adjudicative, because two parties assert ownership and there is no algorithm. |
| **What would need to change** | Claim, evidence, and adjudication become first-class; the operator queue grows; the tiered evidence model, challenge-without-disclosure, and change window must all be built; deletion-on-request and suppression must already work, because a claim dispute where the losing party demands erasure is the first hard case. |
| **What would remain unchanged** | Business, Profile, Offering, the publication gate, Demand, Conversation, Discovery. |
| **Trigger for reconsideration** | A cohort passes audit **and** counsel confirms lawful basis per market; plus an owner decision on who bears the residual risk of a wrong grant and what reversal is committed to the losing party (`Q-025`). |

## DB-12 — Customer identity boundary

| Field | Content |
|---|---|
| **Decision** | Must a delivered request come from an identified, reachable customer? |
| **Primary working assumption** | **Yes.** A request must have a reachable customer contact channel whose control has been verified before the request is delivered to a provider. Verification **timing** remains a P04 comparison. |
| **Alternative** | Unidentified guest requests delivered to providers. |
| **Why primary was selected** | `A-014` and the trust minimum. `G-07` blocks only on the boundary, not the timing, which `mvp-scope.md` charters P04 to compare across pre-submit, just-in-time, and guest-then-verify. The threat is concrete: an unreachable or spam request burns provider attention, and Superola is used to push messages at a third party's channel. |
| **Product impact** | Providers receive reachable requests. Some customers abandon at verification, and that abandonment is a measured cost (`R-022`). |
| **Domain impact** | `PendingVerification` exists so the request is **durable but invisible to the provider**, and **delivery is the verification-gated transition**. The customer's work is not lost on abandonment, and abandonment becomes measurable. A verified-contact **snapshot** is captured at delivery so later channel changes never rewrite what the provider was told. |
| **UX impact** | P04 owns the timing comparison. The identity boundary constrains the surface but not the sequence. |
| **Infrastructure impact** | One verification mechanism serving two consumers — the provider publication gate and the request delivery predicate (`A-021`). Not two subsystems. |
| **Cost / complexity direction** | LOW. Verification abandonment support is the dominant driver. |
| **What would need to change** | If guest requests were permitted: request ownership, reachability, consent, abuse controls, and the delivery predicate all change; spam exposure rises materially; and the provider-attention cost becomes the marketplace's problem. |
| **What would remain unchanged** | Everything from delivery onward. |
| **Trigger for reconsideration** | Measured verification abandonment above the approved bound; an owner decision; P04 evidence on the friction-integrity tradeoff (`Q-018`). |

## DB-13 — Public indexability and crawler policy

| Field | Content |
|---|---|
| **Decision** | Are public provider pages indexable, and by which crawler classes? |
| **Primary working assumption** | **Human-public browsing does not authorize crawling.** Machine access is an explicit per-surface, per-crawler-class switch that **defaults to deny**, governed as data with one enforcement point and one named owner. Indexability remains an unapproved policy gate (`Q-015`). |
| **Alternative** | Approve indexing at launch, whether for search crawlers, AI-search crawlers, or model-training crawlers — each a separate decision. |
| **Why primary was selected** | `docs/07-research/ai-discoverability.md` establishes that indexability and each crawler class need an approved acquisition, content, privacy, and legal policy, and that training access is decided independently. `R-011` warns that thin or duplicate public pages convert a discovery strategy into search-quality debt. |
| **Product impact** | Public acquisition is a deliberate decision with content and maintenance economics, not an emergent property of building pages. |
| **Domain impact** | The publishable field set is versioned and governed; the projection is allowlist-derived and rebuildable; **machine-readable claims never exceed visible human claims.** No design in which indexability is an emergent property of being publicly reachable. |
| **UX impact** | Public page content quality becomes a gating concern rather than a by-product. |
| **Infrastructure impact** | One enforcement point, so bot mitigation cannot silently contradict the approved policy. If approved: canonical identity, sitemap correctness, and referral attribution. |
| **Cost / complexity direction** | Keeping the gate closed: NEAR-ZERO. Opening it: a content inventory that must be differentiated and maintained per locale, plus the maintenance economics `R-011` warns about. |
| **What would need to change** | Per-crawler-class approval records; canonical and sitemap correctness; referral and lead-quality measurement; and — critically — **the deletion path for surfaces Superola does not control must exist first.** |
| **What would remain unchanged** | The domain model; the publication policy mechanism. |
| **Trigger for reconsideration** | An approved public-acquisition hypothesis with maintenance economics. **Ordering constraint: `Q-027` must be settled before `Q-015`**, because approving a crawler before the deletion path exists makes the first deletion request permanently unanswerable on third-party surfaces. |

## DB-14 — Business memberships at launch

| Field | Content |
|---|---|
| **Decision** | Does V1 support multiple users per business, or single-owner businesses only? |
| **Primary working assumption** | Single-owner businesses at launch — **but ownership is modelled as a revocable relation with exactly one role value, not as an owner reference.** Every action records the acting Account plus the Business acted for. |
| **Alternative** | Multi-user roles and invitations at launch (`actors.md` marks provider staff `PROPOSED`, possibly deferred). |
| **Why primary was selected** | Nothing in the request loop breaks without the capability, so deferring it is correct. But deferring the *relation* is not: a direct owner reference cannot express transferred, disputed, provisionally granted, or revoked ownership — all of which the claim path already implies — and it would require rewriting every authorization decision and migrating every business when staff arrive. |
| **Product impact** | One person manages a business at launch. |
| **Domain impact** | `BusinessMembership` exists as a relation with a one-valued role vocabulary. **Actor attribution on every action is mandatory and is the part that cannot be retro-fitted** — once messages have been sent under an ambiguous actor, "who could read this at time T?" is permanently unanswerable. |
| **UX impact** | No invitation, role, or team-management surfaces at launch. |
| **Infrastructure impact** | None. |
| **Cost / complexity direction** | The relation is NEAR-ZERO. The capability is LOW-MEDIUM. Skipping the relation is the expensive choice, paid later. |
| **What would need to change** | Additional role values, invitation and revocation flows, role-scoped permission predicates on each aggregate, and operator handling of ownership transfer. |
| **What would remain unchanged** | Everything — that is the point of shipping the relation now (`A-023`). |
| **Trigger for reconsideration** | A launch archetype whose providers routinely operate as teams — venues with staff are the likely first case; provider research; an owner requirement. |

---

## What is genuinely blocking, and what is not

**No unconditional blocker for P02 exists today.** The P01 record supplies a default for every unresolved item that is valid under both possible answers, which is why the model survives: legacy value is optional to the launch slice and never its precondition; availability is unmodelled by design; entitlements are a seam rather than a system; and multi-country and multi-locale capability is required regardless of launch scope.

**One conditional blocker, and it is genuine:** if the owner requires Superola to execute the transaction in the first release (`DB-02`, `G-02`, `A-004`), Phase 1's boundary set is **wrong, not merely incomplete** — money, payout identity, dispute, tax, and transaction-derived review retro-define outcome, review, "verified", and the consistency and audit regime. David cannot decide it. P02's mitigation is precisely to *not* model it and to require a written Phase-1 justification for any seam it motivates.

**Two partial invalidations — costly, not fatal:** owner-required fan-out at launch (`DB-01`) makes the request aggregate wrong and requires a routing concept, though discovery, profile, conversation, and outcome survive intact; and owner-required broad migration as a launch precondition (`DB-03`) moves provider identity, claim and dispute, deduplication, and taxonomy normalization from conditional to core, and makes P05 a P02 dependency.

**Explicit non-transfer: this authorization does not carry P04.** `G-06` is entirely unaddressed by the envelope and the gate itself requires it resolved before P04 designs profile and request surfaces; `SRC-004` is NOT RECEIVED and `OR-016` requires reconciliation to precede any P04 authorization.

## Sources

`AGENTS.md` · `docs/00-context/product-context.md` · `docs/00-context/interview-evidence.md` · `docs/00-context/assumptions.md` · `docs/01-product/feature-inventory.md` · `docs/01-product/user-journeys.md` · `docs/01-product/product-vision.md` · `docs/01-product/actors.md` · `docs/01-product/monetization.md` · `docs/01-product/open-questions.md` · `docs/01-product/owner-reconciliation-matrix.md` · `docs/05-roadmap/mvp-scope.md` · `docs/05-roadmap/risks.md` · `docs/06-migration/legacy-data-strategy.md` · `docs/07-research/ai-discoverability.md` · `SRC-013`.
