# Architecture Decision Records

Use ADRs for material, durable decisions with meaningful alternatives, cost, lock-in, security/privacy, migration, or reversal consequences. Do not create an ADR merely to make a preliminary idea look decided.

## File naming

`ADR-NNN-short-kebab-title.md`, numbered monotonically. Never reuse an ID. Link superseding ADRs in both directions.

## Statuses

- `PROPOSED`: ready for review but not approved.
- `ACCEPTED`: approved by the appropriate human decision owner. **Authorization to run a phase is not approval of that phase's output** — the only ADR ever accepted required an explicit `DAVID_DIRECTIVE` naming it.
- `SUPERSEDED`: replaced by a newer ADR.
- `REJECTED`: intentionally not selected; preserve rationale when useful.

A `PROPOSED` ADR may additionally carry a **reconciliation disposition** in its header, recorded by a reconciliation phase: `RECOMMEND ACCEPT`, `KEEP PROPOSED`, `HOLD`, `SPLIT`, or `MOVE PRODUCT/UX PART TO P04`. **A disposition is a recommendation, never a status change and never an approval.**

## Template

```markdown
# ADR-NNN — Title

- Status: PROPOSED | ACCEPTED | SUPERSEDED | REJECTED
- Reconciliation disposition: (optional, set only by a reconciliation phase)
- Decision owner: name/role
- Scope: (optional, when the ADR deliberately excludes an adjacent decision)
- Related evidence/requirements: paths, IDs, source IDs
- Supersedes / superseded by: ADR IDs or N/A

## Problem / context

What decision is needed, why now, and which claims are CONFIRMED, OBSERVED, PROPOSED, or ASSUMPTION?

## Constraints

Product, delivery, cost, operations, compliance, security/privacy, migration, and team constraints.

## Options considered

### Option A — Name
Capabilities, benefits, costs, risks, and rejection/selection factors.

### Option B — Name
Capabilities, benefits, costs, risks, and rejection/selection factors.

## Decision

The chosen option and exact scope. For PROPOSED ADRs, state the recommendation and approval still required.

## Rationale

Why this option best satisfies the evidence and constraints.

## Consequences

Positive, negative, operational, migration, and delivery consequences.

## Cost implications

Initial development, fixed and variable spend, developer time, operations, migration, and total cost of ownership.

## Lock-in and exit implications

Portability, proprietary dependencies, data egress/export, switching effort, and exit path.

## Security and privacy implications

Threats, sensitive data, access, retention, compliance, and required controls; use N/A only with rationale.

## Reconsideration trigger

A measurable product, scale, cost, reliability, legal, vendor, or team condition that reopens this decision.

## Validation

Evidence, experiments, review, and acceptance checks.

---

*Record dates — ADR authored YYYY-MM-DD. Evidence access dates. Any later status or scope change, with its date.*
```

**Dates belong in the footer, not the header** (`AGENTS.md`, *Stable document prefix policy*): an ADR's authoring date and its evidence access dates are semantic, but they are not what a reader needs before the status. **ADRs written before this template change still carry a header `Date:` line; that is grandfathered and must not be swept — apply the new shape to new ADRs and to ADRs being substantively modified.**

No ADR was created in P00 or P01 because no production architecture or technology decision had been made.

## Index

All P02 ADRs were `PROPOSED`. **`ADR-004` moved to `ACCEPTED` on 2026-08-11 by an explicit `DAVID_DIRECTIVE` — the first accepted ADR in the repository.** Every other ADR remains `PROPOSED`. All design against the Working Decision Envelope (`SRC-013`), which is `DAVID WORKING ASSUMPTION — OWNER VALIDATION PENDING`.

### P02 — domain and system architecture

| ID | Title | Status | Decision owner beyond David |
|---|---|---|---|
| [ADR-001](ADR-001-single-deployable-modular-application.md) | Single deployable modular application with module-owned data | PROPOSED | P03 validation |
| [ADR-002](ADR-002-business-and-provider-profile-aggregates.md) | Business and ProviderProfile are distinct aggregates within one Provider context | PROPOSED | — |
| [ADR-003](ADR-003-demand-owns-request-draft-request-and-response.md) | Demand owns RequestDraft, ServiceRequest, and ProviderResponse; the offer is an entity inside the request aggregate | PROPOSED | Owner for `G-03` |
| [ADR-004](ADR-004-account-not-typed-by-marketplace-role.md) | An Account is not typed by marketplace role; customer and provider are roles | **ACCEPTED 2026-08-11** | — (`Q-028` **RESOLVED** by `DAVID_DIRECTIVE`) |
| [ADR-005](ADR-005-no-availability-model-in-v1.md) | V1 has no availability model; RequestIntake is an intake state | PROPOSED | **Owner for `G-06` / `Q-007`** |
| [ADR-006](ADR-006-discovery-owns-eligibility-ordering-and-placement.md) | Discovery owns eligibility, organic ordering, and placement provenance; no separate search store in V1 | PROPOSED | P03 for the mechanism |
| [ADR-007](ADR-007-shared-core-plus-governed-category-archetypes.md) | Shared core plus typed category archetypes, with governed metadata confined to attributes | PROPOSED | Owner for `G-04` cohort |
| [ADR-008](ADR-008-monetization-is-a-reserved-policy-seam.md) | Monetization is a reserved policy seam, not a V1 module | PROPOSED | Owner for `G-08` packaging |
| [ADR-009](ADR-009-legacy-records-are-a-distinct-non-discoverable-boundary.md) | Legacy records are a distinct non-discoverable boundary; no runtime coupling to the legacy platform | PROPOSED | **Owner plus counsel for `G-09`**; P05 for strategy |
| [ADR-010](ADR-010-contact-disclosure-decision-seam.md) | Contact data is resolved through a disclosure decision, never stored on a request, message, or notification | PROPOSED | Owner for `OR-011` policy |
| [ADR-011](ADR-011-single-application-layer-as-the-write-path.md) | One application layer is the single write path, and authorization is decided in the domain | PROPOSED | — |
| [ADR-012](ADR-012-allowlist-public-projection-and-deny-by-default-machine-access.md) | The public projection is allowlist-derived and versioned; machine access defaults to deny | PROPOSED | Owner for `Q-015`, after `Q-027` |

### P03 — technology and cost evaluation, as reconciled by P03.1

All `PROPOSED`. **Nothing here is approved.** Reasoning lives in `docs/03-technology/technology-evaluation.md`; vendor movement in `technology-radar.md`; **per-ADR dispositions and the approval packet in `docs/03-technology/p03-decision-reconciliation.md`.**

| ID | Title | Status | P03.1 disposition | Reversal difficulty |
|---|---|---|---|---|
| [ADR-013](ADR-013-application-platform-and-module-boundary-enforcement.md) | Application platform: Kotlin on Spring Boot, one deployable, with build-failing module-boundary enforcement | PROPOSED | **RECOMMEND ACCEPT** | **VERY HIGH** — carries the most scrutiny at approval |
| [ADR-014](ADR-014-postgresql-single-store-with-geospatial-confined-to-one-predicate.md) | One PostgreSQL store with schema-per-module, and geospatial capability confined to a single predicate | PROPOSED | RECOMMEND ACCEPT | MEDIUM, rising with data volume |
| [ADR-015](ADR-015-durable-deferred-work-as-a-transactional-outbox-in-the-same-store.md) | Durable deferred work is a transactional outbox in the same store, not a broker | PROPOSED | RECOMMEND ACCEPT | LOW |
| [ADR-016](ADR-016-managed-platform-single-region-deployment.md) | Managed platform, single region, no orchestration and no general-purpose infrastructure-as-code | PROPOSED | RECOMMEND ACCEPT — region still deferred | LOW today; the window closes with data volume |
| [ADR-017](ADR-017-application-owned-authentication.md) | Authentication is application-owned, with credentials in the marketplace store | PROPOSED | **HOLD** — its own reconsideration trigger fired | BUILD→BUY easy; **BUY→BUILD is vendor-specific and now verified**, not uniformly hard |
| [ADR-018](ADR-018-media-zero-egress-storage-fixed-derivatives-and-link-out-for-audio-and-video.md) | Media: zero-egress object storage, pre-generated fixed derivatives, and link-out for audio and video | PROPOSED | RECOMMEND ACCEPT | LOW–MEDIUM |
| [ADR-019](ADR-019-governed-place-list-with-geocoding-for-provider-addresses-only.md) | A governed internal Place list, with geocoding for provider base addresses only | PROPOSED | **SPLIT** — L1 architecture RECOMMEND ACCEPT · L2 vendor HOLD (legal) · **L3 map ANSWERED BY P04: no rendered map in V1, deferred with three triggers** | LOW — ~USD $75, a few days |
| [ADR-020](ADR-020-web-rendering-strategy-provisional-until-p04.md) | Web rendering strategy: server rendering with progressive enhancement and three named client islands | **PROPOSED — P04 EVIDENCE DELIVERED; RECOMMENDS OPTION A; DAVID APPROVAL REQUIRED** | `HOLD` **discharged by P04** | LOW–MEDIUM, and per-surface |

*The `ADR-020` filename retains its original slug. It was not renamed when the title changed, because committed records — including the P03.1 plan and the ADR index itself — cite the path, and rewriting history to tidy a filename is a worse trade than a stale slug.*

#### Added by P03.1

**`ADR-020` splits the rendering portion out of `ADR-013`.** P03 packaged a VERY HIGH reversal decision (language, framework, runtime, boundary enforcement, one deployable) with a LOW–MEDIUM one (server rendering, htmx, client islands) that depends on interaction requirements P04 has not produced. **One signature would have settled both.** `ADR-013` is now platform-only; `ADR-020` holds the rendering baseline as explicitly provisional and permits P04 to conclude that a richer client is justified for named surfaces.

**`ADR-019` is split into three decision levels** rather than superseded: the architecture (governed `Place` identity, customer-side resolution against it, provider-only geocoding, precision and provenance), the **vendor selection** — on `HOLD` because two legal readings are unresolved and a legal interpretation must not be recorded as a technical fact — and the **rendered map**, moved to P04. **The privacy invariant is unchanged and absolute: precise provider-private location is never publicly exposed. That invariant does not prohibit a privacy-preserving geographic surface, and P03 was wrong to write the two as one sentence.**

**`ADR-017` moved to `HOLD`.** Its own trigger — *"if password-hash exportability is verified for a specific vendor, the exit asymmetry that decides this ADR weakens"* — fired. Verification falsified P03's blanket claim for two of four vendors. Evidence: `docs/07-research/authentication-vendor-verification.md`.

**`ADR-015` additionally corrects `system-architecture.md` §4.** The rule that the notification delivery intent is created *after* the business transaction commits is correct for a broker and wrong for a same-store queue, where it opens a silent lost-notification window producing **neither** a delivery-failure state nor a non-response state (`R-044`). The prohibition is re-scoped from the intent to the external call. **Note the corrected framing:** an outbox is also how a broker avoids the dual-write problem, so the argument for the same-store option is that the outbox table is required either way and the broker is therefore **strictly additive** — not that it is the only mechanism.

#### Added by P04

`ADR-020`'s deferral is **partially discharged**: over thirty-nine named surfaces P04 delivered **three of the four** per-surface items the ADR required — the fourth, what a progressive-enhancement implementation costs, is unavailable until code exists — and found **no V1 surface requiring client state that survives navigation**. The strongest counter-case, the multi-step request composer, resolves to `RequestDraft`, an aggregate `ADR-003` already owns. It recommends **Option A** with a proposed budget of **three** client islands against the ADR's own ceiling of five. `ADR-019` Level 3 is answered in the same phase and on independent grounds: **no rendered map in V1**, deferred with three named triggers, with the privacy invariant retained and explicitly **not** used as the reason. **Neither is approved. Both are recommendations requiring David's individual approval, and P04 satisfied no owner gate — `G-06` remains unsatisfied and `WA-01` is a working assumption, not an answer.**

### Deliberately not written as ADRs

- **"No event bus in V1."** The absence of a mechanism is not a decision worth an ADR. The *requirement* — durable deferred work with at-least-once execution, retry, idempotent handlers, and an operator-visible dead-letter state, explicitly **not** publish-subscribe fan-out — is recorded in `docs/02-architecture/system-architecture.md` §5 and carried to P03. It becomes an ADR when a broker is proposed.
- **"Media is a capability inside Provider, not a module."** A recorded design decision, too small for an ADR. Trigger: conversation attachments, or a second consumer.
- **"Never name a fact the platform cannot prove."** A principle (P14) plus a product gate (`Q-014`), not an architecture decision.
- **Anything naming a language, framework, cloud, store, search engine, broker, cache, or vendor.** Those were P03 decisions. **P03 has now made them: `ADR-013`–`ADR-019` above, with movement recorded in `docs/03-technology/technology-radar.md`. Nothing is adopted; every selection is `PROPOSED — DAVID APPROVAL REQUIRED`.**

#### Added by P03

- **The transactional email provider.** A material choice, but reversal is LOW in code and the real cost is a sender-reputation warm-up, not an architecture change. Recorded in `technology-evaluation.md` §4.5. **One consequence is structural and impossible to retrofit after reputation damage, and is recorded as a requirement rather than an ADR: separate provider configuration sets and separate sending subdomains per consent purpose class, because a vendor's suppression list is global per address and Superola's purpose-class model has no reach inside it** (`R-041`).
- **The observability tier.** Free-tier tooling with a LOW reversal; the *decision that matters* is `D-11`'s existing principle — build the funnel into the domain, buy technical monitoring small. Recorded in `technology-evaluation.md` §4.11.
- **The audit immutability mechanism** — ownership plus revoked destructive grants, no foreign key from audit to any domain table, and a separate retention role. This **implements** `security-privacy-architecture.md` §9 rather than deciding something new, so it is recorded as a validation requirement with build-failing tests, not as an ADR.
- **"Do not spend on a natural-language intent mapper."** A recommendation not to act, on a gate that is closed and unanswerable in P03. `docs/03-technology/ai-evaluation.md` holds the full record. **It becomes an ADR if and when an experiment is proposed.** *(P03.1 note: `ai-evaluation.md` §8.1 records a bounded, optional, capped **AI-assisted request intake** experiment for P04 to accept or reject. It is a candidate experiment, not a decision, and it does not reverse the recommendation above — which concerns the **search** path.)*
