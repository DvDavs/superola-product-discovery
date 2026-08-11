# ADR-009 — Legacy records are a distinct non-discoverable boundary; no runtime coupling to the legacy platform

- Status: PROPOSED
- Date: 2026-08-11
- Decision owner: David for the architecture; **the owner plus qualified counsel decide lawful basis (`G-09`, `OR-009`)**; P05 decides the migration strategy
- Related evidence/requirements: `docs/05-roadmap/mvp-scope.md` ("Imported records must remain traceable to source and separable until approved"); `OR-008`, `OR-009`, `G-09` **PARTIAL**; `A-001`, `A-007`, `A-009`, `A-019`, `A-022`, `A-025`; `R-001`, `R-006`, `R-010`, `R-016`; `docs/06-migration/legacy-data-strategy.md` (NOT STARTED); `docs/02-architecture/decision-branches.md` `DB-03`, `DB-11`
- Supersedes / superseded by: N/A

## Problem / context

The Working Decision Envelope adds legacy and new-platform **coexistence during transition**, which no prior P01 position covers — `docs/02-architecture/architecture-overview.md` lists it as a deferred architecture question. P02 must define a boundary without deciding the migration strategy, which is P05's.

`CONFIRMED` (owner-reported, unaudited): approximately 43,000 registrations; unreliable location behaviour; provider-created categories degraded taxonomy; data saturation. `ASSUMPTION`: that a meaningful subset is real, reachable, and claimable (`A-001`); that migration is lawful and technically possible (`A-007`). `PARTIAL` gate: `G-09`'s **permission** component is an owner-plus-legal fact David cannot supply.

## Constraints

Imported records must remain traceable to source and separable until approved, and cannot receive requests before lawful-use, reachability, control, and publication gates pass. Legacy exports never enter this repository. `mvp-scope.md` requires "no migration / fresh onboarding" to stay a live alternative. Repeated staged import passes are anticipated.

## Options considered

### Option A — A distinct boundary whose records are structurally not provider profiles (recommended)

`LegacyProviderRecord`, `ImportBatch`, `ClaimInvitation`, and a `SuppressionRecord` live in a conditional boundary. Import writes **only** legacy records. Claim **binds ownership and does not publish** — it seeds a *new* `ProviderProfile` in `Draft` through the same governed commands a fresh provider uses. The pipeline itself is out of band and disposable.

### Option B — Imported records as provider profiles in a draft or unpublished state, with default-deny flags

Rejection factors, and this reverses an earlier position in P02's own drafting. An imported record is **Superola asserting a business may exist**; a profile is **a business asserting itself, under its own control**. Collapsing them breaks lawful-use gating and the `OR-009` invariant, destroys source traceability and separability, and — decisively — **creates a live accidental-publication path from unverified imported data straight into the public marketplace.** A default-deny flag is one bug away from failing; a record that is not a profile cannot appear in discovery at all.

### Option C — A runtime read-through, proxy, or dual-write to the legacy platform

Rejection factors: it makes confirmed-broken legacy data — unreliable location, degraded taxonomy, saturation — a **permanent liability of the new system**.

### Option D — A parallel legacy provider model inside the running system

Rejection factors: a second provider model is the expensive mistake hiding inside "coexistence". Imported records must pass **the same publication state machine** fresh providers pass.

## Decision

Recommend Option A. Additionally:

- **Provenance per record and per field** — source system, source record identifier, batch, extracted-at, transformation, lawful-basis reference.
- **Lawful-basis marker per record, default absent meaning unusable. Never inferred from presence in the export.**
- **Separate gates** — publication, contact, claim, and **request eligibility last**.
- **Batch separability and reversibility**: batch identity plus the ability to enumerate everything a batch created or modified. *Unimport this batch* is not a backup restore; a restore also reverts legitimate post-import provider edits, which is a second incident.
- **A suppression record checked before record creation.**
- **Idempotent, resumable, re-runnable per source record**, because it will run more than once.
- **No runtime coupling in either direction.** Coexistence is edge-level routing and URL-identity ownership.
- **The audit output is also derived from personal data:** findings returning to this repository must be aggregates and counts with definitions, **not row-level examples**; synthetic examples must be labelled synthetic.

**If no cohort is approved, this boundary is empty and the claim queue is zero.**

**Approval still required:** David for the boundary; **the owner plus qualified counsel for lawful basis (`G-09`, `Q-025`, `Q-029`) — a fact David cannot supply; and P05 for the migration strategy.**

## Rationale

Making the legacy record a different kind of thing turns a policy control into a structural impossibility. That is stronger than any flag, and it is what actually satisfies `mvp-scope.md`'s separability requirement.

Suppression-before-creation addresses what is judged the single most likely privacy incident in the whole plan: **deletion without suppression is undone by the next import pass**, and staged repeated passes are anticipated.

Per-field provenance matters because a migration mixes imported values with provider-corrected values in one record; after that, *"is this data ours or theirs?"* is unanswerable — which is the difference between a deletion request you can honour and one you cannot.

## Consequences

Positive: imported data cannot reach the public marketplace by accident; batches are reversible; deletions stay deleted; the legacy platform's data quality never becomes the new system's liability; and fresh onboarding remains a genuinely live alternative.

Negative: two representations of provider-adjacent data exist while a cohort is active, and the claim path must bridge them. Accepted, because the bridge is a deliberate governed act.

Operational: claim adjudication, duplicate resolution, and reachability verification are manual exception queues. Broad migration would make the operator queue the dominant cost.

## Cost implications

**Zero if no cohort is approved.** If approved: the dominant driver is manual review time divided by an **unknown yield**, and that denominator is the whole risk. Non-linear drivers commonly forgotten: taxonomy mapping is per **distinct legacy string**, not per record — and free-form entry pushes the distinct-string count toward the record count in the tail; media rights review is per **asset**, not per provider — and performers have many. **The strongest argument is not cost: migration risk is measurement risk. A bad import can make a working product look broken (`R-016`).**

## Lock-in and exit implications

No runtime coupling means no dependency to exit. Batch reversibility is the exit path for the data itself. Cutover requires URL-identity ownership and redirect authority to be settled first (`Q-019`, `A-019`).

## Security and privacy implications

Claim is the highest-risk capability adjacent to V1 — see `security-privacy-architecture.md` §4 for abuse cases, the tiered evidence model, the enumeration-oracle risk, and what must be recorded. Imported contact data frequently belongs to a natural person rather than a business (`A-022`), so it is treated as sensitive until proven business-owned. Jurisdiction is frequently unknown for legacy records, and `unknown` maps to the **strictest** handling among the working markets (`A-025`). Lawful basis per market is a **legal question with a named owner** — P02 does not opine.

## Reconsideration trigger

Broaden migration only when **all** hold: measured cost per publishable, reachable, request-eligible imported provider is **below** measured cost per freshly acquired provider of equivalent quality; lawful use and consent are cleared for the broader set; and operator review capacity exists without breaching the backlog-age stop rule. Both cost figures come from **measurement, not estimation**.

Reconsider Option C only if a decision is taken to keep legacy authoritative for some entity beyond cutover.

## Validation

Verify no path allows a legacy record to appear in discovery or receive a request; that publication is never a side effect of import; that a batch can be enumerated and reversed; that a suppressed record cannot be re-created by a later pass; and that no runtime call reaches the legacy platform.
