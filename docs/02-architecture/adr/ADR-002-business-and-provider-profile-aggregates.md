# ADR-002 — Business and ProviderProfile are distinct aggregates within one Provider context

- Status: PROPOSED
- Date: 2026-08-11
- Decision owner: David
- Related evidence/requirements: `docs/02-architecture/domain-map.md`, `domain-model.md` §1.3 and §2.1; `docs/01-product/feature-inventory.md`; `docs/01-product/user-journeys.md` provider lifecycle safeguards; `docs/00-context/glossary.md`; `Q-006`, `OR-008`
- Supersedes / superseded by: N/A

## Problem / context

P01 requires that `Business` and `ProviderProfile` not be conflated. That is a modelling requirement; it does not by itself say whether they are two modules, two aggregates in one module, or one aggregate.

`CONFIRMED` (`OWNER_INTERVIEW`): the owner described profiles with gallery, geography, media, and profile information. `PROPOSED`: the business, membership, and publication model. `OPEN`: whether a provider may operate multiple teams, locations, or services (`Q-006`, `docs/01-product/feature-inventory.md`).

## Constraints

Publication must validate Business standing, profile completeness against Catalog's publication requirements, service-area validity against Geography, and media readiness **together and rejectably**. Deactivating a Business must un-publish its profiles. Suspension must attach to the accountable party, not to a page a bad actor can recreate. Incomplete profiles must never silently become public.

## Options considered

### Option A — Two aggregates in one Provider context (recommended)

`Business` owns identity, standing, membership, and provenance. `ProviderProfile` owns presentation, offerings, publication state, and freshness. Publication is a state machine on the profile, gated by Business standing, in one transaction.

### Option B — Two separate modules

Rejection factors: the publication invariant becomes a distributed invariant with no compensating benefit at this scale, and Business deactivation to profile un-publication becomes eventually consistent — a **safety regression**.

### Option C — One merged aggregate

Rejection factors: breaks a Business with two legitimate profiles into two unrelated parties; makes suspension equivalent to deleting a recreatable page; leaves a future subscription with no party to attach to; leaves an ownership dispute with no subject; and makes closing a profile imply destroying commercial records.

### Option D — Publication authority in a separate public-distribution context

Rejection factors: **publication authority must sit with the owner of the data being published, or "what is public" has two sources of truth.**

## Decision

Recommend Option A. Distinct aggregates, distinct identifiers, distinct public URL identity — which satisfies P01's non-conflation requirement — inside one module.

**Approval still required:** David. No owner input is needed for the boundary itself; the multi-profile question is coupled to the owner's launch-cohort answer (`G-04`).

## Rationale

The transactional argument is decisive: four conditions must be evaluated together and the operation must be rejectable. Splitting modules to express a modelling distinction buys nothing and costs a distributed invariant plus a safety regression. Extraction later is mechanical because the aggregate boundary already exists.

## Consequences

Positive: publication is atomic and auditable; suspension attaches to the accountable party; multiple profiles per business are expressible; V1 can ship one profile per business without foreclosing more.

Negative: the module is the largest in the design; a reviewer may reasonably charge it with doing too much.

Operational: the profile lifecycle carries the operator suspension and reinstatement path, and customers must not be able to distinguish suspended from deactivated.

## Cost implications

Build: LOW relative to the alternatives — one transaction rather than a coordination protocol. Operator: the publication-exception queue lives here. Exit: extraction along the existing aggregate boundary.

## Lock-in and exit implications

None vendor-side. Internally, the exit path is extracting `ProviderProfile` into its own module, which stays cheap while the aggregate boundary and identifier-only references hold.

## Security and privacy implications

Because publication authority sits with the data owner, the per-field publication policy and its version live here (`ADR-012`). Suspension state must never be published as a punitive label before policy validation.

## Reconsideration trigger

A launch or expansion category requires **multiple independently published profiles per business with distinct staff permissions or independent moderation handling** — venues with multiple rooms alongside performers with multiple acts is the likely first case (`OR-002`, `G-04`).

## Validation

Verify that no path can publish a profile without evaluating all four conditions in one transaction, and that Business deactivation synchronously un-publishes every profile it owns.
