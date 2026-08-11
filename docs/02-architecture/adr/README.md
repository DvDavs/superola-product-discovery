# Architecture Decision Records

Use ADRs for material, durable decisions with meaningful alternatives, cost, lock-in, security/privacy, migration, or reversal consequences. Do not create an ADR merely to make a preliminary idea look decided.

## File naming

`ADR-NNN-short-kebab-title.md`, numbered monotonically. Never reuse an ID. Link superseding ADRs in both directions.

## Statuses

- `PROPOSED`: ready for review but not approved.
- `ACCEPTED`: approved by the appropriate human decision owner.
- `SUPERSEDED`: replaced by a newer ADR.
- `REJECTED`: intentionally not selected; preserve rationale when useful.

## Template

```markdown
# ADR-NNN — Title

- Status: PROPOSED | ACCEPTED | SUPERSEDED | REJECTED
- Date: YYYY-MM-DD
- Decision owner: name/role
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
```

No ADR was created in P00 or P01 because no production architecture or technology decision had been made.

## Index

All P02 ADRs are `PROPOSED`. None is approved. Every one designs against the Working Decision Envelope (`SRC-013`), which is `DAVID WORKING ASSUMPTION — OWNER VALIDATION PENDING`.

| ID | Title | Status | Decision owner beyond David |
|---|---|---|---|
| [ADR-001](ADR-001-single-deployable-modular-application.md) | Single deployable modular application with module-owned data | PROPOSED | P03 validation |
| [ADR-002](ADR-002-business-and-provider-profile-aggregates.md) | Business and ProviderProfile are distinct aggregates within one Provider context | PROPOSED | — |
| [ADR-003](ADR-003-demand-owns-request-draft-request-and-response.md) | Demand owns RequestDraft, ServiceRequest, and ProviderResponse; the offer is an entity inside the request aggregate | PROPOSED | Owner for `G-03` |
| [ADR-004](ADR-004-account-not-typed-by-marketplace-role.md) | An Account is not typed by marketplace role; customer and provider are roles | PROPOSED | — (`Q-028`, David can ratify) |
| [ADR-005](ADR-005-no-availability-model-in-v1.md) | V1 has no availability model; RequestIntake is an intake state | PROPOSED | **Owner for `G-06` / `Q-007`** |
| [ADR-006](ADR-006-discovery-owns-eligibility-ordering-and-placement.md) | Discovery owns eligibility, organic ordering, and placement provenance; no separate search store in V1 | PROPOSED | P03 for the mechanism |
| [ADR-007](ADR-007-shared-core-plus-governed-category-archetypes.md) | Shared core plus typed category archetypes, with governed metadata confined to attributes | PROPOSED | Owner for `G-04` cohort |
| [ADR-008](ADR-008-monetization-is-a-reserved-policy-seam.md) | Monetization is a reserved policy seam, not a V1 module | PROPOSED | Owner for `G-08` packaging |
| [ADR-009](ADR-009-legacy-records-are-a-distinct-non-discoverable-boundary.md) | Legacy records are a distinct non-discoverable boundary; no runtime coupling to the legacy platform | PROPOSED | **Owner plus counsel for `G-09`**; P05 for strategy |
| [ADR-010](ADR-010-contact-disclosure-decision-seam.md) | Contact data is resolved through a disclosure decision, never stored on a request, message, or notification | PROPOSED | Owner for `OR-011` policy |
| [ADR-011](ADR-011-single-application-layer-as-the-write-path.md) | One application layer is the single write path, and authorization is decided in the domain | PROPOSED | — |
| [ADR-012](ADR-012-allowlist-public-projection-and-deny-by-default-machine-access.md) | The public projection is allowlist-derived and versioned; machine access defaults to deny | PROPOSED | Owner for `Q-015`, after `Q-027` |

### Deliberately not written as ADRs

- **"No event bus in V1."** The absence of a mechanism is not a decision worth an ADR. The *requirement* — durable deferred work with at-least-once execution, retry, idempotent handlers, and an operator-visible dead-letter state, explicitly **not** publish-subscribe fan-out — is recorded in `docs/02-architecture/system-architecture.md` §5 and carried to P03. It becomes an ADR when a broker is proposed.
- **"Media is a capability inside Provider, not a module."** A recorded design decision, too small for an ADR. Trigger: conversation attachments, or a second consumer.
- **"Never name a fact the platform cannot prove."** A principle (P14) plus a product gate (`Q-014`), not an architecture decision.
- **Anything naming a language, framework, cloud, store, search engine, broker, cache, or vendor.** Those are P03 decisions, and `docs/03-technology/technology-radar.md` correctly holds every candidate unadopted.
