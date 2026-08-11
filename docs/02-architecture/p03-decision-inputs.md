# P03 Decision Inputs — Technology Problems P02 Exposed

> **Status:** `PROPOSED`. Provenance: `TECHNICAL_DISCOVERY`.
>
> **Standing qualifier:** `DAVID WORKING ASSUMPTION — OWNER VALIDATION PENDING` (`SRC-013`). **Scenario stamp: S-1.**
>
> **This document asks questions. It does not answer them with products.** No language, framework, cloud, store, search engine, broker, cache, media platform, mapping source, model provider, or payment vendor is named as a recommendation. `docs/03-technology/technology-radar.md` records candidates and David's own experience as **evaluation input only**; `R-007` warns that familiarity must not become selection.
>
> **P03 is NOT STARTED.** This file is an input register, not authorization.

## How to use this file

Each decision problem states: what P02 established that creates it, the requirement in Superola's vocabulary, the evaluation criteria that matter here specifically, and the P02 artifact that constrains the answer. `AGENTS.md` additionally requires every material technology decision to record alternatives, tradeoffs, risks, costs, lock-in, exit path, and a reconsideration trigger, with access dates on primary sources.

**Two standing rules for every problem below:**

1. **No number without a source and a volume scenario.** The reported ~43,000 legacy registrations are owner-reported and unaudited (`A-001`, `R-001`) and **may not size any store, index, or compute**. `SRC-006` (analytics and traffic) is NOT RECEIVED, so there is no traffic evidence at all.
2. **Every capability is reached through an interface expressed in Superola's vocabulary.** No vendor identifier, error, or data shape may appear in domain state or public output.

---

## D-01 — Marketplace record store

| | |
|---|---|
| **What creates it** | `ADR-001` (module-owned data, identifier-only cross-module references, no cross-module joins or referential constraints) and `ADR-006` (Discovery queries source truth, so there is one source of truth in V1) |
| **Requirement** | Strong transactional consistency for the atomic units in `system-architecture.md` §4; per-module data ownership; read-your-writes for the acting party; ordered append for messages per conversation; append-only audit that is **not deletable by the domain-deletion path**; per-record retention and deletion; and the ability to enumerate and reverse an import batch |
| **Evaluation criteria** | Transactional guarantees against §4; support for the geographic predicates in `D-02`; ability to express module ownership without cross-module constraints; migration and schema-evolution ergonomics; backup, restore, and point-in-time recovery; bulk export and **deletion proof** (`mvp-scope.md` lock-in row); operability by a small team; fixed and variable cost; exit path |
| **Constrained by** | `search-architecture-requirements.md` §11 — the eligibility-and-ranking query runs here in V1, so its cost is a first-class criterion, not an afterthought |

## D-02 — Geographic predicate capability

| | |
|---|---|
| **What creates it** | `domain-model.md` §4.2 — five archetypes with different service-area semantics, and a **three-valued** `LocationEligibility` |
| **Requirement** | Evaluate: radius-from-base containment with an explicit unit; point-in-named-place containment; delivery-zone containment expressed as radius, place list, **or postal-prefix list**; and proximity for sorting. **A coordinate without sufficient precision must not drive distance eligibility.** `undetermined` must be representable, not coerced |
| **Evaluation criteria** | Predicate expressiveness against the five archetypes; index and query cost at unknown volume; precision handling; whether it lives in `D-01` or needs separate capability; unit fidelity — "50 miles" and "80 km" are different provider promises and rounding changes eligibility |
| **Open finding P03 must weigh** | **Route-corridor semantics for the transportation archetype do not reduce to a containment predicate.** `search-architecture-requirements.md` §11 treats that as an argument for excluding transportation from the launch archetype set rather than for buying heavier machinery. P03 should confirm or challenge that |

## D-03 — Whether a separate search read store is ever needed

| | |
|---|---|
| **What creates it** | `ADR-006` — V1 has no separate store, and `data-architecture.md`'s staged hypothesis is retained but sharpened |
| **Requirement** | Not "choose a search engine". **Establish the measured threshold and instrument for it.** The three triggers and their required instrumentation are in `search-architecture-requirements.md` §11 |
| **Evaluation criteria** | Only after a trigger fires. Then: the full cost table in §11 — including that **every taxonomy change is a full reindex** (`R-010` guarantees taxonomy change), that divergence creates a permanent support queue, and that a derived store **creates a new product state**, published but not yet discoverable |
| **Cheaper alternatives to exhaust first** | A governed **synonym and alias table** on the taxonomy; query and index tuning on `D-01`; a labelled bilingual query set, which must exist before any relevance claim can be measured at all |

## D-04 — Durable deferred work

| | |
|---|---|
| **What creates it** | `system-architecture.md` §5 |
| **Requirement, verbatim** | *V1 requires durable deferred work with at-least-once execution, retry with backoff, idempotent handlers, and a dead-letter state visible in an operator queue. It does not require publish-subscribe fan-out, topic-based routing, partitioned ordered streams, or event sourcing.* |
| **Evaluation criteria** | **P03 must compare alternatives including one that adds no new always-on operational component.** That clause is the cost lever. Then: durability, retry and backoff semantics, idempotency support, dead-letter visibility, scheduling for sweeps, operability, fixed cost, exit |
| **Why it matters** | Every deferred action in V1 has **exactly one known consumer and one known effect**: notification dispatch, media derivatives, measurement forwarding, freshness and intake sweeps, reminders, import. These are **jobs, not events.** A broker is a second failure domain with queue-depth, poison-message, replay, and ordering incidents |
| **Trigger to reopen** | A second *independent* consumer of the same domain occurrence with a real owner; or measured job-store contention degrading transactional latency |

## D-05 — Notification delivery channel

| | |
|---|---|
| **What creates it** | `domain-map.md` (Notification owns delivery only) and `ADR-010` (payload is a reference; the body allowlist) |
| **Requirement** | One approved channel. Delivery attempt and outcome per attempt; **hard-failure signals ingested** so suppression is derivable; delivery state visible to operator and provider and **never conflated with provider non-response**; render only after a disclosure check; retry that cannot duplicate a marketplace effect |
| **Evaluation criteria** | Deliverability apparatus — domain authentication, bounce and complaint ingestion, suppression, sender reputation — because owner evidence already reports substantial platform email, so this is **required, not optional**; per-unit cost; failure-signal fidelity; two-jurisdiction implications under `S-2`; exit and record portability |
| **Explicitly deferred** | Additional channels. Each is a **second copy of the whole reliability apparatus**, plus per-jurisdiction consent and registration. Push additionally requires a channel decision `A-005` has not made. Trigger: non-response share where delivery was **confirmed** |
| **Free cost lever P03 should preserve** | Make in-app request state authoritative and complete. Then notification failure degrades **speed, not correctness**, and delivery reliability stops being a launch blocker |

## D-06 — Media storage, processing, and delivery

| | |
|---|---|
| **What creates it** | `domain-model.md` §1.3; `docs/01-product/feature-inventory.md` makes category-relevant media the evaluation substrate |
| **Requirement** | Object storage with a rights acknowledgement per asset; a small fixed derivative set; a processing state so no half-processed media is publicly visible; deletion that reaches derivatives and caches; upload malware and abuse scanning; hard per-provider count and per-file size caps |
| **Evaluation criteria** | **Served bytes per anonymous public view is the dominant variable line — not stored bytes.** It is the one cost that grows with **acquisition success** rather than with revenue-bearing requests, and public pages are designed to attract anonymous traffic. Then: processing cost per upload, where **video transcode is the most expensive per-unit operation in the product**; delivery-network and egress terms; deletion propagation; exit and bulk export |
| **Alternative P03 must evaluate explicitly, because the repository never named it** | **For performers, where audio and video are category-central, link out to provider-hosted media rather than hosting it.** This preserves the *product* capability — the customer can evaluate the act — at near-zero storage, egress, transcode, and byte-moderation cost. It trades away control, availability, and consistent presentation, and adds dead links plus a third-party terms question. It is **not a decision here**, but it converts the highest variable-cost line in the product into a near-zero one, and `docs/01-product/feature-inventory.md` conflates *media is category-central* with *Superola hosts media* |
| **Trigger for hosting** | Measured link-rot rate on external media above an approved share; or egress per profile view within an approved ceiling with hosting justified |
| **Note** | Human moderation of audio and video costs far more per asset than images, because a person must watch or listen in real time. **That is an operator cost masquerading as a storage cost** |

## D-07 — Place resolution and geocoding source

| | |
|---|---|
| **What creates it** | `domain-model.md` §1.4 — governed `Place` identity with internal identifiers, and mandatory `GeoPoint` precision and provenance |
| **Requirement** | Resolve provider-typed and customer-typed place text to governed `Place` identity; supply representative points and precision; **place identity is internal and never a vendor's identifier** |
| **Evaluation criteria** | Coverage and quality **per country**, since subdivision vocabulary and address shape differ; precision metadata availability; terms of use, caching and storage restrictions, and attribution requirements — these frequently conflict with owning place identity internally; per-request and per-stored-record cost; exit path if the source is replaced |
| **Constrained by** | The reported legacy defect that providers appear in incorrect locations makes precision and provenance a **correctness** requirement, not a nicety |

## D-08 — Authentication mechanism

| | |
|---|---|
| **What creates it** | `domain-model.md` §1.1 and `security-privacy-architecture.md` §2 |
| **Requirement** | Prove control of a credential or channel at a point in time. **The domain must not assume** that authentication proves identity, that a session carries role or permission, that a contact channel is unique or singular, or that credential type is fixed. **Authorization is decided in the domain** (`ADR-011`), so the mechanism must not be asked to carry it |
| **Evaluation criteria** | Verified-channel support for both provider publication gating and customer just-in-time gating from **one** mechanism (`A-021`); abandonment characteristics, since verification abandonment is a measured cost (`R-022`); operability; cost per active account; exit and credential portability |
| **Explicitly not required** | Government-ID or registry proofing, liveness, device fingerprinting, enterprise federation, mandatory second factors for customers, per-request step-up. See `security-privacy-architecture.md` §10 for the triggers |

## D-09 — Public web rendering and public distribution

| | |
|---|---|
| **What creates it** | `ADR-012`; `internationalization-architecture.md` §7; `docs/07-research/ai-discoverability.md` |
| **Requirement** | Serve useful human-rendered public pages with stable canonical identity, retained redirect history on slug change, locale-distinct URLs when more than one public locale ships, freshness, and a correction path. **Machine access must be servable-to-humans-while-denied-to-machines**, with one enforcement point and one owner |
| **Evaluation criteria** | Rendering approach against page usefulness and cost; whether canonical identity and locale routing can be governed as data; whether machine-access policy has a single enforcement point that bot mitigation cannot silently contradict; caching of anonymous public reads, which is also the cheap answer to perceived latency without regionalizing anything |
| **Gated, not assumed** | Indexability and each crawler class remain unapproved (`Q-015`), **and `Q-027` must be settled first**. A public-page programme additionally needs a named acquisition hypothesis and maintenance economics (`R-011`) |

## D-10 — Deployment, operations, and boundary enforcement

| | |
|---|---|
| **What creates it** | `ADR-001` |
| **Requirement** | One deployable artifact; out-of-band execution as a separate process from the same artifact; per-endpoint rate limits, request timeouts, upload caps, and per-workload resource limits; kill switches on risky and optional paths; a fast pipeline so rollback is cheap; **and mechanical module-boundary enforcement in the build** |
| **Evaluation criteria** | For enforcement specifically: can a forbidden cross-module import **fail the build**? Without that, `ADR-001` is claimed rather than implemented, and the architecture pays monolith exit costs later while having advertised modularity. This is the one place P03 should be willing to spend |
| **Explicitly rejected until triggered** | Multiple deployable units; container orchestration; multi-region topology. `internationalization-architecture.md` §5 is blunt: market scope is not topology |

## D-11 — Observability, audit storage, and measurement

| | |
|---|---|
| **What creates it** | `system-architecture.md` §8; `domain-model.md` §8 |
| **Requirement** | The funnel as **durable domain state**, not inferred log lines, segmented by `market`, `locale`, and `archetype` **in the record shape from day one**. Append-only audit with restricted read, a stated retention period, and non-deletability by the domain-deletion path. Operator case records with owner, class, and age. Basic technical health: error rate, latency on the search and request paths, background-job failure and dead-letter visibility, delivery-failure visibility |
| **Evaluation criteria** | Whether measurement queries can run against `D-01` without degrading production latency; audit retention and restricted-read support; the smallest sufficient technical monitoring tier |
| **The trap to name** | **Buying an observability product to answer *product* questions.** Funnel questions are answered from the transactional store with queries. A per-ingested-volume or per-host product is a recurring cost that grows with logging verbosity, and it is usually adopted **because product measurement was never built into the domain model.** Build the funnel into the domain; buy technical monitoring small |
| **Explicitly rejected** | Distributed tracing (there are no services), log aggregation with long retention, session replay, a warehouse and reporting stack, real-time analytics, an experimentation platform. Triggers in `system-architecture.md` §8 |

## D-12 — Label and localization storage

| | |
|---|---|
| **What creates it** | `internationalization-architecture.md` §3 |
| **Requirement** | Localized labels and synonyms as **data** over stable language-neutral identifiers, with a versionable label set; locale resolved once per request; no concatenated user-facing strings; no locale baked into an identifier or a URL-generation rule that cannot take another value |
| **Evaluation criteria** | Whether labels live in `D-01` or elsewhere; how a label set is versioned and reviewed; formatting for numbers, dates, and currency at the presentation edge; cost of adding a locale **structurally** versus **operationally** — the second is the expensive one and is a scope decision, not a technology one |
| **Note P03 must carry** | **Locale does not follow country.** The launch production locale may be Spanish even if the launch geography is the United States (`internationalization-architecture.md` §2) |

## D-13 — Optional natural-language intent experiment

| | |
|---|---|
| **What creates it** | `ADR-006`; `search-architecture-requirements.md` §2; `decision-branches.md` `DB-08` |
| **Requirement, if and only if separately approved** | The mapper sits **outside and in front of** the domain, emits the **same structured constraint set** the deterministic form emits and nothing it cannot, **persists no authoritative value**, has a kill switch with the product fully functional when disabled, has its own cost ceiling and separately attributable measurement, and **no module imports it** |
| **Evaluation criteria** | Per-query variable cost against traffic — **not against revenue-bearing requests**, because search is the highest-volume, lowest-intent action and public acquisition is the stated hypothesis, which inverts the unit-economics curve; latency and timeout policy; the **labelled evaluation set covering both languages, incomplete intent, unsupported constraints, and adversarial input**, which is real work with no user-visible output; prompt and version management as deployable artifacts; **rate limiting as a launch blocker**, because a paid endpoint reachable anonymously is a cost-amplification attack surface; privacy gate per `docs/07-research/ai-discoverability.md`; provider portability |
| **Prerequisite that is not a technology question** | **A recorded corpus of real zero-result and abandoned queries whose failure is demonstrably *interpretive* rather than *coverage*-driven.** If failures are coverage failures, the money belongs in supply acquisition. Nothing in the repository currently makes this distinction, and it is the whole question. Cheaper alternatives to exhaust first: the synonym table in `D-03`, and a guided structured entry flow |

## D-14 — Legacy pipeline environment

| | |
|---|---|
| **What creates it** | `ADR-009`; conditional on an approved cohort |
| **Requirement** | An out-of-band, resumable, idempotent, re-runnable batch capability in a controlled environment, writing **only** legacy records through governed commands; batch enumeration and reversal; a suppression check **before** record creation; least-privilege access; **no production data or row-level examples in this repository** |
| **Evaluation criteria** | Only after `G-09` and its legal questions are answered. Then: rehearsal and reconciliation support; rollback fidelity; access controls; data-handling location, retention, and deletion; per-record review throughput |
| **Coupled decision** | Coexistence requires URL-identity ownership, redirect authority, and a cutover trigger to be settled (`Q-019`, `A-019`) before any cutover mechanism is chosen |

## D-15 — Transaction Extension boundary — named, not evaluated

> `FUTURE`, separately owner-approved. `A-004` remains OPEN. **P03 must not evaluate payment technology while `G-02` is unresolved.**

Recorded only so the boundary is not forgotten: an external financial event would be received into its own transaction as an immutable, idempotently-keyed record; any domain change it triggers is a separate replayable step; the marketplace never treats an external callback as source of truth for commitment state without reconciliation; and **payout identity is a separate verification subject from marketplace identity**. Prerequisites are in `security-privacy-architecture.md` §9.

---

## Cross-cutting evaluation criteria P03 must apply to every problem above

| Criterion | Why it is specific to Superola |
|---|---|
| **Total cost of ownership, not the lowest bill** | `docs/03-technology/cost-model.md`; `R-021`. Fixed spend, variable unit cost **with the unit named**, developer time, and operator burden |
| **Operator workload created** | Plausibly the largest recurring cost at this stage, and it had no owner in any pre-P02 architecture document. `system-architecture.md` P21 makes it a principle |
| **Lock-in and exit** | Canonical data ownership, bulk export, **deletion proof**, usage and egress charges, replacement effort, dual-run and cutover, acceptable downtime (`mvp-scope.md` lock-in row) |
| **Small-team operability** | One person, plus justified specialists. A capability that needs a specialist to operate is a hiring decision, not a technology decision |
| **Degradation behaviour** | What the marketplace does when the capability is unavailable. `D-05` in particular must degrade speed, not correctness |
| **Reconsideration trigger** | Required by `AGENTS.md` for every material decision, and it must be **measurable** — a condition someone could observe, with the V1 instrumentation that makes it observable named alongside it |
| **Two-jurisdiction implications under `S-2`** | Consent, registration, tax, and per-unit price frequently differ per country. Under `S-1` most of this is deferred; the scenario must be stated in the evaluation |

## What P03 must not do

- Select anything before the decision problem is stated and its criteria agreed.
- Treat a candidate in `docs/03-technology/technology-radar.md` as adopted. Familiarity is an input to delivery-risk and cost analysis, never a selection (`R-007`).
- Produce a numeric non-functional-requirements table without sources and volume scenarios. `system-architecture.md` explains which specific commitments those numbers silently buy.
- Size anything to the unaudited legacy count.
- Evaluate payment, booking, availability, entitlement, sponsored-inventory, or protocol technology while their gates are unresolved.
- Answer `D-13` before the zero-result corpus exists.

## Sources

`AGENTS.md` · `docs/02-architecture/domain-map.md`, `domain-model.md`, `system-architecture.md`, `search-architecture-requirements.md`, `internationalization-architecture.md`, `security-privacy-architecture.md`, `decision-branches.md`, `adr/` · `docs/02-architecture/data-architecture.md`, `integration-architecture.md` · `docs/03-technology/technology-radar.md`, `cost-model.md`, `infrastructure-options.md`, `build-vs-buy.md` · `docs/05-roadmap/mvp-scope.md`, `risks.md`, `dependencies.md` · `docs/06-migration/legacy-data-strategy.md` · `docs/07-research/*` · `SRC-013`.
