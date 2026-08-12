# Integration Architecture — Advanced by P02

> **Status:** integration **boundaries** are now specified by P02. **No external provider, vendor, product, or protocol is selected.** Selection is P03, against [p03-decision-inputs.md](p03-decision-inputs.md).
>
> **Standing qualifier:** `DAVID WORKING ASSUMPTION — OWNER VALIDATION PENDING` (`SRC-013`). **Scenario stamp: S-1.**

## The boundary rule

**Every external capability is reached through an interface expressed in Superola's vocabulary. No vendor identifier, vendor error, or vendor data shape appears in domain state or public output.** If a boundary requires emulating vendor semantics inside the domain, the boundary is in the wrong place.

This is `system-architecture.md` principle P10, and it is stated so it can be checked: search domain state and public output for vendor artifacts.

## Integration categories V1 Phase 1 actually needs

| Category | Owning boundary | Requirement | P03 problem |
|---|---|---|---|
| Notification delivery | Notification | One approved channel. Attempt and outcome per attempt; hard-failure signals ingested so suppression is derivable; delivery state visible and **never conflated with provider non-response**; render only after a disclosure check; retry that cannot duplicate a marketplace effect. | `D-05` |
| Media object storage, processing, delivery | Provider (media capability) | Rights acknowledgement per asset; a small fixed derivative set; a processing state so no half-processed media is publicly visible; deletion that reaches derivatives and caches; upload scanning; hard count and size caps. **Served bytes per anonymous public view is the dominant variable line, not stored bytes** — and it is the one cost that grows with acquisition success rather than with revenue-bearing requests. | `D-06` |
| Place resolution and geocoding | Geography | Resolve typed place text to governed `Place` identity; supply representative points **and precision**. **Place identity is internal and never a vendor's identifier.** Terms of use, caching restrictions, and attribution frequently conflict with owning identity internally, so that conflict is an evaluation criterion, not a surprise. | `D-07` |
| Authentication | Identity & Access | Prove control of a credential or channel at a point in time. **The mechanism must not be asked to carry authorization**, which is decided in the domain. | `D-08` |
| Durable deferred work | Application layer | At-least-once execution, retry with backoff, idempotent handlers, and an operator-visible dead-letter state. **Explicitly not** publish-subscribe fan-out, topic routing, partitioned ordered streams, or event sourcing. P03 must compare an alternative that adds **no new always-on component**. | `D-04` |
| Anonymous public read caching | Delivery edge | Reduce origin load for public pages, which is also the cheap answer to perceived latency **without regionalizing anything**. | `D-09` |

## Integration categories deliberately absent from V1

Named so their absence is a decision rather than an oversight.

| Absent | Why | Trigger |
|---|---|---|
| A message broker or event stream | Every deferred V1 action has exactly one known consumer and one known effect. | A second *independent* consumer with a real owner; or measured job-store contention degrading transactional latency. |
| Realtime transport | No V1 surface requires server-initiated push. The only near-realtime requirement is safety-suppression propagation. | Measured provider response latency showing sub-minute awareness would change outcomes. |
| A dedicated search service | `ADR-006`, and the measured thresholds in `search-architecture-requirements.md` §11. | Any one of the three thresholds, **after** tuning. |
| Additional notification channels | Each is a **second copy of the whole reliability apparatus**, plus per-jurisdiction consent and registration. Push additionally requires a channel decision `A-005` has not made. | Non-response share where delivery was **confirmed**, plus consent and registration cleared per jurisdiction. |
| Payment, payout, tax, invoicing | Transaction Extension; `A-004` OPEN. **And nothing payment-adjacent may be stored in V1 — including via legacy import**, so classifying and excluding it is an explicit P05 rule. | `G-02` resolution. `D-15` records the boundary only. |
| Billing and subscription | `ADR-008`; the seam is one decision point, not a system. | `monetization.md`'s V1.x gate conditions observed as data. |
| Language-model providers | `DB-08`; the intent experiment sits outside and in front of the domain, and no module imports it. | The zero-result corpus showing **interpretive** rather than coverage failure. `D-13`. |
| MCP, A2A, A2UI, UCP, ACP, `llms.txt`, any protocol adapter | **No V1 deliverable may exist whose only consumer is an emerging protocol.** `R-012`. | A named approved channel or partner with measurable economics and target-market access. |
| A public versioned contract or data feed | No approved external consumer exists. `ADR-011`. | An approved consumer with measurable economics. |
| Any runtime coupling to the legacy platform | It would make confirmed-broken legacy data a permanent liability of the new system. `ADR-009`. | A decision to keep legacy authoritative for some entity beyond cutover. |
| An observability platform, warehouse, or tracing | The funnel is durable domain state queried directly. `D-11` names the trap: buying an observability product to answer *product* questions, usually because product measurement was never built into the domain. | Measurement queries degrading production latency; counted unresolvable incidents; a compliance reporting gap. |

## What every material integration evaluation must still cover

Unchanged from the P00 requirement, and now with owning boundaries named above:

- The business capability and its owning module boundary.
- The synchronous or asynchronous contract, and **degradation behaviour** — what the marketplace does when the capability is unavailable. `D-05` in particular must degrade **speed, not correctness**, which means in-app request state must be authoritative and complete.
- Retries, idempotency, ordering, rate limits, reconciliation, and observability. **Repeated actions must not create duplicate marketplace effects** (`R-013`).
- Data classification, residency, retention, consent, and provider access — against the classification table in `security-privacy-architecture.md` §12.
- Development and operational effort, fixed and variable cost **with the unit named**, operator workload, lock-in, and exit path including bulk export and **deletion proof**.
- The evidence threshold that would justify adding complexity, and a **measurable** reconsideration trigger with the V1 instrumentation that makes it observable.

**Messaging content and notification transport require separate ownership even if one vendor product spans both** — and the disclosure decision sits between them and belongs to neither (`ADR-010`).
