# ADR-005 — V1 has no availability model; RequestIntake is an intake state

- Status: PROPOSED
- Date: 2026-08-11
- Decision owner: David for the architecture; **the owner decides the product promise (`G-06`, `OR-010`, `Q-007`)**
- Related evidence/requirements: `A-006` `SUPERSEDED`; `OR-010`; `G-06` **UNSATISFIED**; `Q-007`; `docs/05-roadmap/mvp-scope.md`; `docs/01-product/feature-inventory.md` category-variability table; `R-015`, `R-016`; `docs/02-architecture/decision-branches.md` `DB-10`
- Supersedes / superseded by: N/A

## Problem / context

`A-006` — that provider availability can be represented by one universal model across categories — is `SUPERSEDED` and rejected. But **the repository never states the positive replacement**, and a rejection without a replacement is an invitation to design one. Meanwhile `G-06` is a BLOCKING gate that the Working Decision Envelope is **entirely silent on**.

`SUPERSEDED`: `A-006`. `PROPOSED`: that "accepting requests" is not a date promise. `OPEN`: what "available" should promise a customer (`Q-007`, `G-06`).

## Constraints

The five archetypes have five different resource units — team, room, person, production slot, vehicle — and materially different time semantics. `mvp-scope.md` defers universal date and resource availability, holds, realtime availability, and calendar synchronization. P02 must not fill an unresolved **owner** gate with a design decision.

## Options considered

### Option A — No availability model; a provider-controlled intake state (recommended)

`RequestIntake` with states accepting, paused, and unconfirmed; a paused-until horizon; a last-confirmed timestamp; an optional provider-authored reason. A stale `accepting` **decays to unconfirmed**. The customer's desired date is request context only. Date feasibility is resolved by the provider's response.

### Option B — Advisory provider-declared blackout ranges

Rejection factors for V1: LOW build but **MEDIUM operator** — truth decays silently, a stale blackout suppresses demand invisibly, the provider then complains about having no leads, and the cause is undiagnosable from data.

### Option C — Date-filtered search

Rejection factors: this is `R-015` verbatim. Every stale record silently deletes supply, and it pushes the query toward dedicated search infrastructure.

### Option D — Per-resource inventory, holds, or calendar synchronization

Rejection factors: HIGH build and cognitive cost; holds are booking's machinery arriving before booking; synchronization failures are silent and produce **confidently wrong** availability.

### Option E — A universal availability boolean

`REJECTED` already by `A-006`.

## Decision

Recommend Option A, and **state the replacement positively in every artifact: `V1 has no availability model.`**

`RequestIntake` is deliberately **not** named availability, because a field named `available` will be read as availability regardless of documentation. The archetype **declares** its resource concept as descriptive metadata only, which is what makes a universal boolean structurally impossible.

Additionally: **the decline path records an optional reason** (`ADR-003`).

**Approval still required:** David for the architecture; **the owner for what "available" promises a customer (`G-06`, `OR-010`, `Q-007`) — currently UNSATISFIED and unaddressed by the working envelope. This ADR is the deliberate absence of a design, pending that answer.**

## Rationale

`G-06` is an owner decision P02 must not pre-empt, and every option above Option A encodes a promise the owner has not made. Option A makes no claim the platform cannot evidence, costs almost nothing, and leaves a clean insertion point: eligibility is already a composable predicate set, so a future availability context plugs into both discovery eligibility and request-creation eligibility as one more predicate.

The decline reason is the decisive addition. It converts *"do we need availability?"* from a debate into a measurement, answerable from Phase-1 data at no cost. Nothing in the repository previously proposed it.

## Consequences

Positive: no false availability; no calendar operations; the seam is near-free; the question becomes empirically answerable.

Negative: customers cannot filter by date, so some requests will reach providers who cannot serve the date. That waste is **visible** in the decline reason rather than hidden.

Operational: intake decay requires a scheduled sweep and a provider reconfirmation path. Without decay, an unrefreshed positive signal turns the marketplace into a graveyard of stale contacts (`R-016`).

## Cost implications

Build: NEAR-ZERO. Recurring: none. Operator: none in V1; each rung up the alternative ladder adds an operator queue whose cost exceeds its build cost.

## Lock-in and exit implications

None. The composable eligibility predicate set is the seam, and it is required for other reasons anyway.

## Security and privacy implications

No calendar data means no exposure of a provider's schedule — which `docs/07-research/ai-discoverability.md` names as a sensitive-data risk for any future external read path. Any later availability model must expose coarse state, never private schedule detail.

## Reconsideration trigger

**Measured share of provider declines attributed to date or resource unavailability, from the recorded decline reason, within the launch cohort** — threshold set by a named owner at approval. Or the owner's answer to `G-06` makes "available" a reservable commitment, in which case contention and holds appear and the consistency map changes materially, coupling to `DB-02`.

## Validation

Verify no date, calendar, hold, concurrency, or resource-availability field exists anywhere; that the non-claim is stated verbatim on every profile and search surface; that a stale `accepting` decays; and that the decline reason is captured.
