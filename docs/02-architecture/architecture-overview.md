# Architecture Overview — Index and Principle Record

> **Status:** P02 produced one `PROPOSED` logical architecture. **No architecture is accepted, and no technology is selected.** This file is an index and a principle record; it must not silently become an approval record.
>
> **Standing qualifier for everything P02 produced:** `DAVID WORKING ASSUMPTION — OWNER VALIDATION PENDING` (`SRC-013`). The P01.1 release gate was **not** satisfied at P02 start — `G-06` and `G-10` unsatisfied, `G-01`/`G-04`/`G-05`/`G-07`/`G-09` partial. **Nothing in P02 authorizes P04.**

## P02 artifacts

| Artifact | What it decides |
|---|---|
| [domain-map.md](domain-map.md) | Ten marketplace modules, one application layer, one cross-cutting audit facility, one conditional legacy boundary. What was rejected and why. Dependency direction and forbidden dependencies. Adjudicated boundary disputes. |
| [domain-model.md](domain-model.md) | Conceptual entities, twenty-three required distinctions, the category-extensibility decision, the geography model, and six lifecycle state machines. No physical schema. |
| [system-architecture.md](system-architecture.md) | Logical layers, deployment shape (`PROPOSED — TO BE VALIDATED IN P03`), the interaction and consistency map, transactional boundaries, the asynchrony requirement, extension points, and twenty-one refined principles. |
| [search-architecture-requirements.md](search-architecture-requirements.md) | Search inputs, filters, geographic eligibility, ranking inputs, sponsored separation, zero-result behaviour, bilingual handling, freshness, and the **measured threshold** for dedicated search infrastructure. No search technology. |
| [internationalization-architecture.md](internationalization-architecture.md) | The capability-envelope versus launch-scope split, scenario definitions `S-1` and `S-2`, conceptual handling of country, locale, timezone, currency, address, and units, and why multi-country data is not multi-region infrastructure. |
| [security-privacy-architecture.md](security-privacy-architecture.md) | Identity, ownership, authorization placement, profile claim, contact disclosure, public-versus-private fields, legacy provenance, auditability, moderation, future agent access, the future payment boundary, multi-jurisdiction, data classification, threats, and a proportionality check. |
| [decision-branches.md](decision-branches.md) | The Working Decision Envelope, its reconciliation against the P01 record, release-gate status, and fourteen decision branches with reversal cost. |
| [p03-decision-inputs.md](p03-decision-inputs.md) | Fifteen technology decision problems, stated as questions with evaluation criteria. No products. |
| [adr/](adr/) | `ADR-001`–`ADR-012`, all `PROPOSED`. |
| [data-architecture.md](data-architecture.md) · [integration-architecture.md](integration-architecture.md) | Advanced by P02: what is now specified, and what remains for P03 and P05. |
| [../../presentation/architecture-preview-v0.1.md](../../presentation/architecture-preview-v0.1.md) | Bilingual owner-facing preview, including "what changes if…". |

## Principles

P00 recorded twelve principles as `DAVID_DIRECTIVE` guardrails and `TECHNICAL_DISCOVERY` positions. **P02 evaluated and refined them into twenty-one checkable principles in [system-architecture.md](system-architecture.md) §7.** That section is now the canonical principle record.

Summary of what changed, so the revision is traceable rather than silent:

| P00 principle | P02 disposition |
|---|---|
| Build for a two-sided marketplace, not only a directory | Retained; it is a product framing rather than an architecture principle, and it is already canonical in `AGENTS.md`. |
| Prefer clear domain boundaries and incremental delivery | Retained and made checkable as P1: one deployable, module-owned data, identifier-only references, **no cross-module joins or referential constraints** — and mechanically enforced, without which the principle is a claim. |
| Strongly evaluate a modular monolith before microservices | Executed. `ADR-001` recommends one deployable with module-owned data, compared against coarse distributed and per-module services **on actual evidence**, with what it makes worse stated honestly and seven measurable flip triggers. |
| Avoid premature Kubernetes, microservice, and event-stream complexity | Executed and sharpened. `system-architecture.md` §5 states the real requirement — durable deferred work, explicitly **not** publish-subscribe — because every deferred V1 action has exactly one known consumer. |
| Separate business capabilities from external vendors/providers | Retained and made checkable as P10: no vendor identifier, error, or data shape in domain state or public output. The glossary also retires "vendor" for the supply side, which was a genuine collision with this principle. |
| Model monetization and entitlements explicitly | Resolved against `mvp-scope.md`'s prohibition: `ADR-008` locates **one seam** rather than building a system, and locks the eligibility ordering invariant. |
| Treat internationalization, SEO, geography, privacy/security, and cost as structural | Executed across three dedicated documents, plus per-module cost annotation and a cost field on every accepted boundary. |
| Keep messaging distinct from notification delivery and organic ranking distinct from paid placement | Retained, and given the mechanisms that make each real: the disclosure decision (`ADR-010`) and `placementBasis` (`ADR-006`, `ADR-008`). |
| Let future mobile needs influence stable API/domain boundaries without forcing premature mobile implementation | Executed as `ADR-011`: one application layer as the single write path, and **no versioned public contract in V1**, because no external consumer is approved. |
| Use AI to augment deterministic marketplace mechanics | Made structural: the intent experiment sits outside and in front of the domain, emits the same structured constraint set the form emits, persists no authoritative value, and **no module imports it**. |
| Scale infrastructure with evidence and optimize for a small initial engineering team | Retained as P12, and operationalized: every structural addition names the measurement that justified it **and** the reconsideration trigger that would remove it. |
| Give every material decision a reconsideration trigger | Retained, and strengthened: a trigger is only real if V1 instruments its metric, so **the triggers determine V1's instrumentation requirements**. |
| — | **Added by P02:** P13 nothing becomes public or privileged as a side effect · P14 never name a fact the platform cannot prove · P15 repeated actions must not create duplicate marketplace effects · P16 delivery failure and human non-response never collapse · P17 category variation is governed data, not a code branch · P18 sensitive content stays inside its owning boundary · P19 imported data is provenance-tagged and passes the same gates · P20 read models may be stale, safety actions may not · **P21 architecture decisions are evaluated for the manual queue volume they create** — operator workload is plausibly the largest recurring cost at this stage and had no owner in any pre-P02 architecture document. |

## Architecture questions still open after P02

| Question | Owner |
|---|---|
| The V1 stopping boundary, and therefore whether the Transaction Extension is in scope | Owner (`G-02`, `A-004`) — **the highest-leverage question outstanding** |
| What "available" promises a customer | Owner (`G-06`, `OR-010`, `Q-007`) — **unsatisfied and unaddressed by the working envelope; blocks P04** |
| Lawful basis for contacting or migrating legacy records | Owner plus qualified counsel (`G-09`, `Q-025`, `Q-029`) |
| Launch geography, locale, and category cohort | Owner (`G-04`, `G-05`, `OR-002`–`OR-004`) |
| Contact disclosure policy | Owner (`OR-011`, `Q-008`) — **does not block, because of `ADR-010`** |
| Indexability and crawler classes | Owner (`Q-015`), **after `Q-027`** |
| Coexistence identity and URL ownership, and the cutover trigger | Owner plus P05 (`Q-019`, `A-019`) |
| **Is a durable "notify me when supply appears" watch in or out of the launch slice?** | Owner plus David (`Q-031`). **Stated explicitly because P02 shipped the non-durable `SearchIntent` model unratified**: `domain-model.md` §11 says to decide before accepting it, and the decision has not been made. The trade is real — the non-durable model cleanly enforces "a search is never a broadcast", but a durable-never-delivered watch entity enforces that just as well while preserving arguably the strongest cold-start mechanism available to a marketplace with unknown local liquidity (`A-013`). |
| Whether `unconfirmed` intake may ever be a ranking input | David plus product, after measurement (`search-architecture-requirements.md` §6) |
| Every technology choice | P03, against `p03-decision-inputs.md` |
| Legacy migration strategy | P05 |
| Committed scope, roadmap, and delivery ranges | P06 |
