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

No ADR is created in P00 because no production architecture or technology decision has been made.
