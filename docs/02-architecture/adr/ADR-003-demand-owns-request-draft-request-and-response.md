# ADR-003 — Demand owns RequestDraft, ServiceRequest, and ProviderResponse; the offer is an entity inside the request aggregate

- Status: PROPOSED
- Date: 2026-08-11
- Decision owner: David
- Related evidence/requirements: `docs/02-architecture/domain-model.md` §1.6, §2.4, §5.2, §5.3, §9; `docs/05-roadmap/mvp-scope.md` B0 envelope; `docs/01-product/user-journeys.md`; `A-011`, `A-014`, `R-016`, `R-022`, `OR-006`, `Q-013`, `Q-017`
- Supersedes / superseded by: N/A

## Problem / context

The V1 loop runs from a customer's private draft to a delivered one-recipient request, a provider response, and a customer-reported outcome. P02 must decide the aggregate boundaries, and specifically whether the provider's offer is its own aggregate or module.

`PROPOSED` (`TECHNICAL_DISCOVERY`): the one-recipient boundary and the response vocabulary. `ASSUMPTION`: that direct request creates enough value without broadcasting (`A-011`), and that just-in-time verification is acceptable (`A-014`). `OPEN`: `Q-013`, `Q-017`, `G-03`.

## Constraints

Exactly one recipient per request, immutable after delivery. A reused draft never becomes automatic matching. The universal response is clarify, decline, or a minimum text offer; structured fields, revision, and expiry are category-gated. `NoResponse` must be observable, because it is the marketplace-health signal the whole loop depends on (`R-016`). Verification must precede provider delivery without losing the customer's work (`R-022`).

## Options considered

### Option A — Demand owns draft, request, and response; the offer is an entity inside the request aggregate (recommended)

### Option B — A separate Quote module or aggregate

Rejection factors, in order of weight. **Transactional:** the request's transition to responded and the response's creation must be atomic, or a request reads "no response" alongside an orphan offer — which corrupts the `R-016` measurement directly. **Invariant:** every stated response rule in `user-journeys.md` is expressed in terms of request state. **Concurrency:** one customer, one provider, a bounded response count — no contention justifies a separate root. **Scope:** the V1 minimum response is text, so there is barely a structure to own.

### Option C — No draft concept

Rejection factors: `mvp-scope.md` requires a reusable private draft, so it must exist anyway; and without it, `DB-01` multi-provider matching becomes a structural redesign rather than a new policy.

### Option D — Pre-generalize the recipient as a collection of size one

Rejection factors: multi-provider matching is **not reachable by relaxing the constraint** — it needs a separate routing concept with consent, provenance, caps, windows, closure, deduplication, and reroute. Pre-generalizing hides that cost inside a design that looks ready, and violates the anti-inflation rule.

## Decision

Recommend Option A. `RequestDraft` is a first-class aggregate. The recipient is a **single reference**, deliberately not a collection. `ProviderResponse` is an entity inside the request aggregate, with kinds clarification, decline, and offer, an **optional decline reason**, and a **stable identifier with immutable versions**.

**Approval still required:** David for the aggregate boundaries; **the owner for the one-recipient semantic (`G-03`), which the gate says silence does not settle.**

## Rationale

The atomicity requirement decides the offer's placement. The draft decides `DB-01`'s reversal cost: because the draft is required for V1's stated reuse behaviour anyway, matching later becomes a new policy over an existing aggregate — **the cheapest high-value seam in the design, at zero carry cost.**

The recorded decline reason is a second near-free instrument: it makes the availability question (`DB-10`, `G-06`) answerable from Phase-1 data at no cost.

## Consequences

Positive: response creation is atomic; response rate and latency are measurable from Demand alone; `NoResponse` is representable; `DB-01` is cheap; the offer's stable version makes a future commitment referenceable without renumbering.

Negative: the offer and the original request detail live in Demand while clarifying chat lives in Conversation (`ADR-010` context). If P04 renders them as one thread, that is presentation, not a boundary change.

Operational: `NoResponse` is `POLICY PENDING` — observable, non-punitive, non-terminal, with a governed window; a late response still moves the request forward.

## Cost implications

Build: LOW-MEDIUM; the category-gated response machinery is the only variable part. Operator: request-lifecycle exceptions. Variable: notification volume roughly one per request under the one-recipient invariant.

## Lock-in and exit implications

None vendor-side. Promoting the offer to its own aggregate later is the exit path, and it stays available because the offer already has a stable identifier and versions.

## Security and privacy implications

Request free text is customer-private and must be assumed to contain contact data. The verified-contact **snapshot** at delivery means later channel changes never rewrite what the provider was told. Submission must be idempotent so a retry cannot create a second request (`R-013`). A message must never be able to change request state.

## Reconsideration trigger

Promote the offer to its own aggregate when an offer must be referenced by a commitment that outlives request closure (`DB-02`), or when category-structured offers acquire independent revision or approval workflows. Reconsider the recipient model on an owner decision reversing `G-03`.

## Validation

Verify: no path creates a response without the request's state transition in the same transaction; a repeat submission creates no second request; a message cannot move a request to responded; and a draft cannot become a delivered request without deliberate recipient selection and fresh confirmation.
