# ADR-011 — One application layer is the single write path, and authorization is decided in the domain

- Status: PROPOSED
- Date: 2026-08-11
- Decision owner: David
- Related evidence/requirements: `docs/02-architecture/system-architecture.md` §1 and §7 (P2, P3); `docs/02-architecture/architecture-overview.md` principle 9 (future mobile influences stable boundaries without forcing mobile implementation); `docs/07-research/ai-discoverability.md` §C; `A-005`; `R-013`; `docs/00-context/product-context.md` (channel and future-mobile direction)
- Supersedes / superseded by: N/A

## Problem / context

Three future channels are named in the repository — native mobile, an optional natural-language intent path, and external agent read access — none approved and none evidenced. P02 must keep them cheap without building for them, and must decide where authorization is enforced.

`PROPOSED`: web as the initial channel with a preserved future mobile path (`DAVID_DIRECTIVE`). `ASSUMPTION`: `A-005`, that mobile is not required for launch. `FUTURE`: agent read access.

## Constraints

`mvp-scope.md` mandates operator exception paths, which are a second caller by design. `docs/07-research/ai-discoverability.md` and `R-013` anticipate a future authenticated read path. Permissions here are **state-dependent** — whether a provider may read a request depends on delivery and request state. The legacy platform's effective architecture was per-channel controllers with embedded rules.

## Options considered

### Option A — One application layer as the single write path; authorization decided in the domain (recommended)

No business rule or state transition lives in a delivery adapter. Every client reaches domain state through the same use-case layer. Authorization answers one question — *may this Account, acting for this Business, perform this action on this resource, in this resource state?* — inside the domain.

### Option B — Per-channel controllers with embedded rules

Rejection factors: this is what the legacy platform effectively did. A rule living in a web route is **silently absent in the second channel** — the textbook authorization bypass — and operator exception handling is precisely where authorization gets skipped.

### Option C — Contract-first with a versioned public interface in V1

Rejection factors: builds and maintains an external contract with no approved external consumer. `A-005` is OPEN and no agent channel is approved. The anti-inflation rule forbids a deliverable whose only consumer is a future channel.

### Option D — A dedicated adapter per channel in front of the domain

Rejection factors: premature. One channel exists. Adapters multiply the surface where a rule can be duplicated inconsistently.

### Option E — Authorization at the delivery edge, with the domain trusting the caller

Rejection factors: the three reasons in the constraints — a second caller by design, an anticipated second read path, and state-dependent permissions that a channel guard cannot see.

## Decision

Recommend Option A. The application layer commits the domain transaction and then creates post-commit side effects; it holds **no invariant**. Delivery channels enforce authentication and pass the actor through. **Every deny is loggable.**

Sophistication is deliberately low — two roles, one product, a small team. **What matters is placement, not sophistication:** no policy language, no external authorization service, no fine-grained permission matrix.

**Approval still required:** David only.

## Rationale

This is the seam that makes three separate future extensions nearly free at once: a mobile client becomes an additional client rather than a rewrite; the natural-language path becomes something that sits *in front of* the layer and emits the same structured query object the form emits; and a future authenticated agent read routes through the same policy point. **That last one is the concrete payoff for not putting authorization in the channel.**

It is also the discipline you want with one channel anyway, so it passes the admissibility test in `system-architecture.md` §6 on Phase-1 grounds alone.

One further requirement, free now and invasive later: **delegated authority requires the acting Account to be distinguishable from the acting channel.** If V1 records only that a Business did something, a future delegated action is unattributable.

## Consequences

Positive: no second write path; a channel cannot acquire a private rule set; operator exception paths are subject to the same authorization as user paths; adding a client is additive.

Negative: an indirection that looks like ceremony with one channel. Accepted — the alternative is the failure mode the legacy platform demonstrated.

Operational: operator capabilities must sit behind **audited entry points**, because a database-console action has no actor, no reason, and no audit record. The interface may be crude.

## Cost implications

Build: LOW — essentially the structure a single-deployable modular application wants regardless. Operator: lower, because exception paths are attributable. Exit: not applicable; this is not an external dependency.

## Lock-in and exit implications

None. It reduces lock-in to any particular delivery technology, because the write path is independent of it.

## Security and privacy implications

**This is the control that prevents the second-channel authorization bypass.** Combined with `ADR-012`, it also ensures any future external read path consumes the same public projection humans get, with no privileged fields and no separate query path into source records. No **external or agent** write path exists in V1; the only structural preparation is that actions have identity so a retry cannot create a second request (`R-013`), and provider opt-out is a Business-level fact rather than a channel setting.

## Reconsideration trigger

A channel demonstrably cannot be served without channel-specific domain behaviour. For the versioned external contract: an approved external consumer with measurable economics.

## Validation

Verify no state transition is reachable except through the use-case layer; that no authorization decision lives in a route, template, or client; that operator actions are attributed and audited; and that every action records both the acting Account and the Business acted for.
