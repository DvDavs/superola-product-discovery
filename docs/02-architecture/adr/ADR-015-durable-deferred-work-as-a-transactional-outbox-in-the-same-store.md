# ADR-015 — Durable deferred work is a transactional outbox in the same store, not a broker

- Status: PROPOSED
- Date: 2026-08-11
- Decision owner: David (technical decision owner for this repository)
- Related evidence/requirements: `docs/03-technology/technology-evaluation.md` §4.4; `p03-decision-inputs.md` `D-04`; `system-architecture.md` §3, §4 (corrected), §5; `domain-map.md`; `ADR-001`, `ADR-014`; `R-013`, `R-016`, `R-044`; `SRC-013`
- Supersedes / superseded by: N/A

## Problem / context

`system-architecture.md` §5 states the requirement verbatim: *"V1 requires durable deferred work with at-least-once execution, retry with backoff, idempotent handlers, and a dead-letter state visible in an operator queue. It does not require publish-subscribe fan-out, topic-based routing, partitioned ordered streams, or event sourcing."* And: *"P03 must compare alternatives including one that adds no new always-on operational component."*

Every deferred action in V1 has **exactly one known consumer and one known effect**: notification dispatch, media derivatives, measurement forwarding, freshness and intake sweeps, reminders, and import. These are **jobs, not events.**

P03 additionally found a defect in §4 that this decision resolves — see the Decision section.

## Constraints

One deployable plus one worker process from the same artifact. One part-time operator. A dead-letter state must be visible in an operator queue and actionable **without a database console**, because *"a console action has no actor, no reason, and no audit record."* Retries must not create duplicate marketplace effects (`R-013`). Delivery failure and human non-response must never collapse into one state (`R-016`, principle P16).

## Options considered

### Option A — A job table in the same store as domain data (recommended)

Claim rows with skip-locked selection; retry with backoff; a failed state that is a row you query; recurring tasks for sweeps. **Incremental always-on infrastructure: none.**

### Option B — A managed cloud queue or broker

**Stated correctly, because the naive version of this comparison is wrong:** a broker *can* be used safely, and the way you do it **is the transactional outbox** — the intent commits to a database table with the business transaction, and a separate relay process publishes it onward. So the honest comparison is not *outbox versus broker*. It is **outbox alone versus outbox plus a broker plus a relay.**

Rejection factors follow from that framing: **the broker is strictly additive at V1 volumes.** You still need the outbox table, so it buys no durability the recommended option lacks; it adds a second always-on component, a second failure domain, a second vendor, and a relay process to operate. The dead-letter view must still be built, now spanning a vendor interface and **partly outside the audit boundary**, so a retry action has no actor and no reason unless that is rebuilt too. One evaluated push-model variant additionally requires a publicly reachable job endpoint — a new anonymous attack surface on a product whose edge is deny-by-default (`ADR-012`).

**What a broker genuinely buys is fan-out to unknown consumers** — and `system-architecture.md` §5 establishes V1 has exactly one known consumer and one known effect per deferred action.

### Option C — An in-memory-store-backed queue

Rejection factors: this is precisely the *"new always-on operational component"* the requirement asks to avoid. Beyond cost, **its default durability model is a poor match for the word "durable"** — periodic snapshotting loses everything since the last snapshot; the usual append-only setting loses up to a second. For a delivery intent backing a marketplace request that is a silent correctness loss, not a performance tradeoff. Not transactional with the primary store, so the dual-write problem returns.

### Option D — A managed task-queue service

Rejection factors: same structural objections as Option B, plus lock-in on a category that costs nothing to own.

## Decision

**Option A.** A job table in the same store, with recurring-task support for scheduled sweeps.

### The correction this decision forces in `system-architecture.md` §4

§4 previously required the notification delivery intent to be created **after** the business transaction commits, and forbade it from sharing that transaction *"Ever."*

**That rule is correct for a broker and wrong for a same-store queue.** It is correct when the queue is a separate system, because you cannot atomically commit to two systems, so you defer and accept a gap. Once the job store is a table in the same database, the rule creates the exact failure it was written to prevent:

> A crash between the business commit and the intent insert leaves the request durable, the provider never notified, **and no dead-letter entry — because no entry was ever created.** `R-016` and P16 require that delivery failure and human non-response never collapse. **This failure mode produces neither state. It produces silence** — the one outcome the whole notification design exists to make impossible.

§3 already states the requirement correctly: *"best-effort delivery over a **durable intent**."* **An intent that can be lost is not durable.**

**The prohibition is therefore re-scoped from the intent to the external call.** The durable intent commits **with** the business transaction. The side effect — the actual send — executes afterwards, in the worker process, in its own transaction per attempt, with retry and an operator-visible dead-letter state. **This is the transactional outbox pattern.** Recorded as `R-044`; §4 and the two matching lines in `domain-map.md` are corrected.

**Approval still required:** David.

## Rationale

Every clause of the verbatim requirement is satisfied at **$0 incremental always-on cost**.

**The deciding argument, stated precisely.** The outbox table is required under *every* option that satisfies §4's durability requirement — including the broker options, because the outbox is exactly how a broker avoids the dual-write problem. **Since the table is common to all of them, the broker is strictly additive: a second always-on component and a relay process, bought at 0.096 jobs per second, that buy no durability the table does not already provide.**

*(An earlier draft of this ADR claimed Option A was "the only mechanism under which P02's durability requirement holds literally." That was wrong, and adversarial review caught it: a broker fronted by an outbox holds the requirement equally well. The decision is unchanged, because the correct argument — additive cost for no additive guarantee — is stronger than the one it replaces.)*

The load confirms it is not a compromise. At the Growth scenario: 250,000 emails ÷ 2,592,000 seconds = **0.096 jobs per second average**, roughly 2 per second at peak. Mature libraries document lock-and-fetch at 1,000+ executions per second. **Roughly 100× headroom at the largest modelled volume** — every alternative is priced against a load two orders of magnitude below where any becomes necessary.

Idempotency is *easier* here, not harder, because the dedupe key and the business row are in one transaction.

## Consequences

Positive: no new component, no new failure domain, no new vendor; the dead-letter state is a row the operator surface already knows how to render; job records are covered by the same backup and point-in-time recovery as business data; and §4's durability requirement becomes literally true.

Negative: job-table churn creates dead tuples and vacuum load on the primary store — monitored by trigger 2 below. Scheduled work requires leader election if the worker ever runs more than one instance.

Operational: the operator retry command must record actor, reason and time, like every other governed command.

## Cost implications

**$0 incremental always-on infrastructure at every volume scenario.** The worker process is mandated by `ADR-001` regardless; the store exists regardless. Build cost is a library plus handlers. Operator cost is one dead-letter queue page, which the operator surface must have anyway.

## Lock-in and exit implications

None vendor-side. The job table is ordinary schema, exported by the same bulk export as everything else. Moving to a broker later means writing a producer and a consumer against handlers that already exist and are already idempotent — **the idempotency discipline is the transferable asset, and it is required either way.**

## Security and privacy implications

Job payloads carry **references, never customer-private content** — consistent with `ADR-010`, where the notification payload is a pointer and rendering happens after a disclosure check. A failed job visible in an operator queue must not leak the content it concerns; the operator opens the referenced record through the audited read path (`Q-026` directive). No new network surface, no new credential, no new anonymous endpoint.

## Reconsideration trigger

Move the **job store** on any one: measured contention where per-statement analysis attributes ≥20% of a request-path latency regression to job-table statements; sustained dead-tuple ratio above 30% for 24 hours with autovacuum running; or sustained enqueue above 200 jobs per second — **noting this is roughly 2,000× the Growth average and will not fire.**

Add a **broker** only on `system-architecture.md` §5's own trigger: a second *independent* consumer of the same domain occurrence, with a real named owner, that the existing handler cannot serve. **These are different decisions and must not be conflated.**

## Validation

Verified 2026-08-11: the store's documentation states the skip-locked clause *"can be used to avoid lock contention with multiple consumers accessing a queue-like table"* — the pattern is a documented feature, not a community workaround; and the recommended framework's current release ships a persistent event-publication registry with failed-state tracking, completion-attempt counting, restart republication and resubmission, over the same store.

Required instrumentation: enqueue rate per job type per minute; dead-letter depth; per-statement latency attribution on the job table; and dead-tuple ratio recorded daily per table. Acceptance: replay every deferred action and confirm no duplicate marketplace effect (P15); and kill the process between the business commit and dispatch, then confirm the intent survives and the job runs.
