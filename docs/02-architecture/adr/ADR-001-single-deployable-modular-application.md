# ADR-001 — Single deployable modular application with module-owned data

- Status: PROPOSED
- Date: 2026-08-11
- Decision owner: David (technical decision owner for this repository)
- Related evidence/requirements: `docs/02-architecture/system-architecture.md` §2, `domain-map.md`; `AGENTS.md` architecture guardrails; `docs/02-architecture/architecture-overview.md` principle 4; `docs/05-roadmap/mvp-scope.md` operating envelope; `A-011`, `A-013`, `R-014`, `R-021`; `SRC-013`
- Supersedes / superseded by: N/A

## Problem / context

P02 must recommend a deployment shape for V1 Phase 1. `AGENTS.md` requires evaluating a modular monolith before distributed systems without assuming either outcome, and states that a capability is not automatically a service or deployment unit. `docs/02-architecture/architecture-overview.md` principle 4 additionally warns against premature orchestration, microservice, and event-stream complexity.

Evidence status: the marketplace loop and its invariants are `PROPOSED` (`TECHNICAL_DISCOVERY`). Traffic, scale, and operational evidence is **absent** — `SRC-006` (analytics/traffic) is NOT RECEIVED. Team size is `CONFIRMED` as small (`OWNER_INTERVIEW`: specialist hiring only when justified). Demand is unvalidated (`A-011`, `A-013`).

## Constraints

Small team with no evidence of a team split. No measured resource saturation of any kind. No data-residency or compliance isolation requirement in any input. Explicit owner cost discipline. A non-contractual six-month planning hypothesis. Exception-only manual operations capped by the operating envelope. The Phase-1 loop is **consistency-coupled by design**: a request may not be delivered to a non-publishable provider; publication gating spans Provider, Catalog, and Geography; block enforcement is read on the hot path by two modules.

## Options considered

### Option A — Single deployable modular application with module-owned data (recommended)

One deployable artifact. Modules own their persisted data; cross-module references by identifier only; **no cross-module query joins and no cross-module referential constraints**. Out-of-band execution — media processing, notification retry, import — runs as a **separate process from the same artifact**. Boundaries are **mechanically enforced in the build**.

Benefits: cross-module invariants remain simple synchronous checks; one fixed-cost floor; one investigation surface; the modules remain the durable decision while the deployment shape stays reversible. Costs: boundary erosion is silent without enforcement; coupled deploys; dissimilar workloads scale as one unit; deadline pressure attacks boundaries first.

### Option B — Coarse distributed services (three to four)

Rejection factors: buys nothing measurable against current evidence, and specifically fragments the synchronous invariants around publication gating, request eligibility, and block enforcement. Duplicated authentication and observability. Higher fixed spend against explicit cost discipline.

### Option C — Microservices per module

Rejection factors: actively harmful. Forces eventual consistency onto invariants that are currently simple checks; multiplies the manual-operations burden the operating envelope caps; and makes the unvalidated-demand risk far more expensive to abandon.

### Option D — Single deployable with no module ownership

Rejection factors: this is effectively what the legacy platform was. It pays monolith exit costs later while claiming modularity now.

## Decision

Recommend Option A for V1 Phase 1, **with mechanical boundary enforcement as an inseparable part of the decision**. Out-of-band workers are not distribution of the domain.

**Approval still required:** David, and validation in P03 against the selected runtime and store (`D-01`, `D-10`).

### Scope note — what this ADR does *not* decide

This ADR decides the **deployment shape**, not the **module count**. The ten marketplace boundaries in `domain-map.md` were derived from domain analysis — invariants, data ownership, and retention or privacy divergence — and are treated as fixed input here rather than as a cost variable.

That deserves stating plainly, because this ADR argues the modules are the durable decision worth the design budget: **ten boundaries is roughly ten times the enforcement surface of three or four.** Three of the ten are thin in V1 — Notification owns two entities, Marketplace Analytics owns a vocabulary and is write-only, Marketplace Operations is a set of manual queues. The decomposition is defended on the grounds that a module here is a *conceptual ownership boundary* rather than a deployment unit, so its marginal cost is naming discipline plus one enforcement rule, not infrastructure. If P03 finds that enforcement cost scales with boundary count in practice, the boundary set — not the deployment shape — is what should be revisited first.

## Rationale

The decisive argument is not simplicity. It is that **the modules are the durable decision and the deployment shape is the reversible one**, so under unvalidated demand the design budget belongs on the irreversible thing.

The second argument is asymmetry. **The first additional deployable unit is by far the most expensive one** — going from one to two introduces partial failure, contract versioning, and cross-unit tracing as new permanent categories, while five to six introduces almost nothing. And **re-merging services is harder than splitting a monolith**, so the direction of regret is asymmetric.

Option A is only cheap **if boundaries are enforced**. Unenforced, it degrades into a monolith with folders, the option to split is lost, and the entire benefit disappears. Enforcement is therefore not a nice-to-have — it is the cost case for the decision.

## Consequences

Positive: synchronous invariants stay simple; a single fixed floor; rollback is simpler than coordinated multi-service rollback; horizontal replicas are cheap because unused code costs nothing at rest.

Negative: a bad migration or hot loop degrades everything simultaneously, including public pages, which is a compounding acquisition loss; noisy-neighbour effects between import batches and live search; a team-scaling ceiling that is irrelevant now.

Operational: out-of-band workers as separate processes provide the cheap majority of blast-radius isolation. Per-endpoint rate limits, request timeouts, upload caps, and per-workload resource limits are required.

Migration: extraction later is mechanical **only if** identifier-only references and no cross-module joins held.

## Cost implications

Fixed spend: lowest of the options — one floor rather than N. Variable: no added network hops per user action. Operator: lowest; no "which unit?" step before every investigation. Cognitive: lowest; no eventual consistency imposed on transactional invariants. Exit: **low only under enforcement**; high without it. No figures are given — `docs/03-technology/cost-model.md` is `INPUTS NOT VALIDATED` and `mvp-scope.md` forbids fabricated ranges.

## Lock-in and exit implications

The lock-in risk is internal, not vendor-side: cross-module joins and referential constraints are what make extraction expensive. The exit path is module extraction along an existing aggregate boundary, which stays available only while enforcement holds.

## Security and privacy implications

One process means one blast radius, mitigated by per-workload limits and out-of-band isolation. Authorization must be decided in the domain rather than at the delivery edge (`ADR-011`, `security-privacy-architecture.md` §3) — a single deployable makes a channel-level guard *look* sufficient, which is precisely the trap. Module-owned data plus identifier-only references also limits accidental cross-boundary data exposure.

## Reconsideration trigger

Any one, with thresholds set by a named owner at approval: two named modules show **measured** divergent resource saturation on one instance class, unrelieved by vertical scaling or a read replica; read-model maintenance measurably degrades write-path latency; notification retry backlog affects request-path availability; a legal ruling requires physical data separation; a counted number of releases of one module blocked by another module's deploy risk; two or more **teams** blocked on one release train; or Transaction Extension approval brings payment-compliance isolation requirements.

## Validation

P03 validates against the selected runtime and store, and must specify the **mechanical boundary-enforcement mechanism** — without it this ADR is not implemented, only claimed. Required instrumentation: per-module resource attribution, and a release-block log.
