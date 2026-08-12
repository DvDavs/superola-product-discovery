# ADR-006 — Discovery owns eligibility, organic ordering, and placement provenance; no separate search store in V1

- Status: PROPOSED
- Date: 2026-08-11
- Decision owner: David
- Related evidence/requirements: `docs/02-architecture/search-architecture-requirements.md`; `domain-map.md`; `docs/02-architecture/data-architecture.md` staged hypothesis; `docs/05-roadmap/mvp-scope.md`; `R-004`, `R-010`, `R-011`, `R-018`; `docs/07-research/search.md`
- Supersedes / superseded by: N/A

## Problem / context

Two questions are usually answered together and should not be: **who owns eligibility and ordering**, and **whether the search read path is a separate store**.

`PROPOSED`: the deterministic search model. `CONFIRMED` (`OWNER_INTERVIEW`): legacy location behaviour is unreliable and provider-created categories degraded taxonomy. **Absent:** any traffic or scale evidence — `SRC-006` is NOT RECEIVED.

## Constraints

Ranking must be explainable, because once sponsorship exists an unauditable ordering cannot prove organic and paid separation (`R-004`). Governed data must not know about relevance. Ranking quality at launch is bounded by **data**, not engine capability — there are zero clicks, conversions, reviews, or session history. `R-010` guarantees taxonomy will change.

## Options considered

### Option A — Discovery owns eligibility, ordering, and placement provenance; the read path is a query over the single source of truth (recommended)

Discovery owns `EligibilityDecision`, `LocationEligibility`, the organic ordering policy, zero-result and unmet-demand capture, and `placementBasis`. It reads Provider, Catalog, and Geography — **it never owns their data**. In V1 the read path is a query executed directly against source records, **with the publication allowlist applied as a predicate inside that query**, so no field can reach a result that the publication policy does not permit.

Capabilities: category and archetype filtering, three-valued geographic eligibility, deterministic ordering, zero-result and relaxation handling, placement provenance. Benefits: one source of truth; freshness is read-your-writes and free; ranking policy has one auditable home; no reindex obligation. Costs: the eligibility-and-ranking query cost sits on the primary store and must be measured and tuned. Risks: query cost growth is the failure mode, and it is the trigger in the reconsideration section. Selection factor: it is the only option that gives ranking an owner without also buying a second source of truth.

### Option B — Inline queries with no owning module

Rejection factors: forces Catalog, Geography, and Provider to agree on relevance — a cycle and a governance vacuum. `R-004`, `R-011`, and `R-018` all require ranking policy auditable in **one** place.

### Option C — A dedicated search store or search infrastructure in V1

Rejection factors. Decomposing the working query shows that category filtering, geographic eligibility, deterministic ordering, bilingual labels, and zero-result handling all reduce to standard predicates over governed data. The only genuine candidate is free-text relevance with per-language analyzers and typo tolerance — **and `mvp-scope.md` scopes V1 to governed category plus geography search, which is structured search.** Further, **a dedicated engine's differentiators have nothing to eat at launch**, because relevance tuning, learning-to-rank, and vector recall all feed on behavioural signal that does not exist yet. Costs: HIGH new fixed line; **every taxonomy change is a full reindex**; a permanent "I updated my profile and cannot find myself" support queue; two sources of truth so every investigation begins with which one is wrong; and it **creates a new product state** — published but not yet discoverable — that must be shown honestly or hidden dishonestly.

### Option D — Defer placement provenance until sponsorship ships

Rejection factors: retrofitting placement destroys historical organic comparability, which is exactly the `R-004` and `R-018` failure mode. It also removes the only data that could later teach Superola how to **price** sponsorship, since position-to-request attribution is obtainable only from organic data.

## Decision

Recommend Option A. Discovery owns `EligibilityDecision`, `LocationEligibility`, the organic ordering policy, zero-result and unmet-demand capture, and `placementBasis`. In V1 the read path is a query over source truth — **no separate store, no dedicated search infrastructure.** `placementBasis` ships from day one with `organic` as its only value.

"Projection" here is a **contract** — a named, owned, rebuildable read model — not infrastructure. Whether it is ever a derived store is a P03 decision gated by measurement, consistent with `data-architecture.md`'s staged hypothesis.

**Approval still required:** David for the ownership boundary; P03 for the store and query mechanism against `D-01`, `D-02`, and `D-03`.

## Rationale

Ownership and storage are separate questions. Ownership must be Discovery, or ranking policy has no auditable home. Storage must be the source of truth in V1, because the expensive option's differentiators cannot be exercised and its operational costs are immediate and permanent.

Two positive consequences worth naming: search freshness is `read-your-writes` and therefore **free**, and **the "published but not yet discoverable" product state does not exist in V1.**

## Consequences

Positive: one source of truth; free freshness; explainable ordering; placement provenance ships before it is needed; no reindex obligation on taxonomy change.

Negative: query cost sits on the primary store, so the eligibility-and-ranking query must be measured and tuned. Free-text relevance is genuinely weaker than a dedicated engine would provide — accepted, because it is out of V1 scope.

Operational: a governed **synonym and alias table** on the taxonomy is the cheap alternative that must be tried before any model or engine is funded; it absorbs a large share of surface-form variance across languages because filtering happens on identifiers.

## Cost implications

Option A: LOW in V1; the driver is query cost over source truth. Option C, stated so the later decision is made against full cost: MEDIUM build, **HIGH recurring fixed — likely the largest single new fixed line at this scale**, MEDIUM variable growing with providers times locales times fields, HIGH operator, HIGH cognitive, MEDIUM exit — the one good property, since a derived store is rebuildable.

## Lock-in and exit implications

None vendor-side in V1, which is the point. The staged hypothesis preserves the option: a derived store can be added because the read model is already a named owned contract that is rebuildable from source.

## Security and privacy implications

**No field may reach a search result that the publication policy does not permit.** In V1 the query reads source records directly, so the allowlist must be enforced **inside** that query as a predicate rather than by the existence of a separate store (`ADR-012`). That distinction matters: a derived store would enforce the boundary by construction, while a direct query enforces it by discipline, so the discipline must be explicit and testable. A search result is never authorization; durable actions re-check source truth.

## Reconsideration trigger

Any one, thresholds set by a named owner at approval: p95 latency of the eligibility-and-ranking query exceeds the approved budget on the approved instance class **after** indexing and query tuning, with candidate-set size and query mix recorded; **or** bilingual free-text relevance with typo tolerance shows measured gains over the deterministic baseline **on a labelled query set that must exist first**; **or** filtered facet-count cardinality makes counts infeasible at the approved page shape.

The reported ~43,000 legacy registrations are owner-reported and unaudited and **may not size any index, store, or compute**.

## Validation

Required V1 instrumentation, without which the triggers are decoration: query latency recorded with candidate-set size and filter shape; zero-result and abandoned-query logs with the constraint set attempted; and supply counts per `Market` so an interpretation failure can be told apart from a coverage failure. Verify no ranking input is demand-derived except via a governed published provider quality attribute, and that every result and every measurement event carries `placementBasis`.
