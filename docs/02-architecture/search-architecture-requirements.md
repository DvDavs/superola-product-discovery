# Search Architecture Requirements — P02

> **Status:** `PROPOSED`. Provenance: `TECHNICAL_DISCOVERY`.
>
> **Standing qualifier:** `DAVID WORKING ASSUMPTION — OWNER VALIDATION PENDING` (`SRC-013`). **Scenario stamp: S-1** (one launch geography, one production locale) for all operational statements.
>
> **Hard boundary:** no search technology is selected. Not Elasticsearch, not OpenSearch, not any relational, vector, or hosted search product. This document defines **requirements and a measured threshold**; P03 selects a mechanism against them.

## 1. The authoritative-versus-derived distinction

| | Authoritative marketplace data | Search read path |
|---|---|---|
| Owner | Provider (business, profile, offering, publication policy), Catalog (taxonomy, definitions), Geography (places, markets) | Discovery |
| Truth | Yes | **Never.** It is derived |
| Rebuildable | — | **Must be fully rebuildable from source truth** |
| Authorization | Durable actions re-check source truth | **A search result is never authorization** |
| Field exposure | — | **The publication allowlist is applied as a predicate in the query itself. No field may reach a search result that the publication policy does not permit.** This is the constraint that stops the search path becoming the private-data leak path. Note carefully: it does *not* mean the query avoids source records — in V1 it reads them directly. It means the allowlist is enforced **inside** that read, not by a separate store |

**V1 recommendation: no separate search store and no dedicated search infrastructure.** Discovery composes eligibility and ordering as a query over the single source of truth, **with the publication allowlist applied as a predicate**. Consequences that are worth stating positively:

- Search freshness is `read-your-writes` and therefore **free**.
- **The product state "published but not yet discoverable" does not exist in V1.** With a derived store it becomes real and must be either shown honestly or hidden dishonestly — neither is free, and both land in the operator queue.
- There is one source of truth, so no bug investigation begins with *"which one is wrong?"*

`PublishedSnapshot` (see `domain-model.md` §1.3) is the versioned, allowlist-derived public projection of a profile. It is created at publication and re-versioned on edit — synchronous, not a lagging pipeline. It exists for stable public URL rendering, freshness, correction, and moderation evidence, not for search performance.

## 2. Search inputs

| Input | Representation | Notes |
|---|---|---|
| Service need | A governed `Category` reference, or an `archetype` reference for broader intent | Never free text as the primary key. Provider-typed and customer-typed text are *resolution inputs* against governed labels and synonyms |
| Location constraint | A `Place` reference at a stated granularity, optionally with a `GeoPoint` and precision | See §4 |
| Category-scoped filters | Values bound to `CategoryAttributeDefinition`s marked filterable for the target `Market` | See §3 |
| Desired date or window | **Request context only. Never a filter and never a system claim** | `V1 has no availability model` (`A-006` `SUPERSEDED`, `OR-010`) |
| Free-text terms | Optional secondary matching over governed labels, synonyms, and provider narrative | See §9 for why this is the only candidate that could ever justify dedicated infrastructure, and why it is out of V1 scope |
| Locale | Resolved once per request; affects labels and formatting, **never identity or eligibility** | |
| Result window | Page position and size | Position is recorded on impression — see §7 |

**The natural-language boundary.** If the optional intent experiment is ever enabled, it converts text into **the same structured constraint set the deterministic form produces, and nothing the form cannot produce**. Unmapped constraints are **rejected, not approximated**. The interpreted constraints are shown to the customer so they can be corrected, and interpretation failure is visible rather than silent. This is what *structurally* guarantees the model is never the source of truth for eligibility or ranking: it can only emit constraints Discovery validates.

## 3. Category filters

Filterability is governed, per-`Market`, and **revocable**. The seven criteria are defined in `domain-model.md` §3.4. The three most often omitted, and the three that produced the reported legacy defects:

- **Explicit null semantics.** A definition must state whether missing means `no` or `unknown`, and a filter must never silently exclude `unknown` without telling the user. Legacy search died of saturation: treating missing as matching made everything match everything.
- **Coverage above a governed threshold in the target Market.** A filter answered by a small fraction of local supply produces a broken empty-result page. Filterability is therefore revocable per Market, not permanent.
- **Language-neutral values.** Filter values are stable identifiers with localized labels, never translated strings.

Free-text provider narrative is **never filterable and never mined into facets.**

## 4. Geography and service-area semantics

Eligibility is evaluated **per `ServiceOffering`, never per `Business`**, and returns three values.

`LocationEligibility → eligible | ineligible | undetermined`

**`undetermined` is load-bearing and must be representable and surfaced.** Coercing it to `eligible` reproduces legacy saturation; coercing it to `ineligible` hides real supply and produces false empty results. The per-archetype eligibility rules and the `undetermined` conditions are in `domain-model.md` §4.2.

Two rules that constrain the search mechanism without selecting one:

- **A `GeoPoint` without sufficient precision may not drive distance eligibility.** This is the direct structural fix for the reported legacy defect that providers appear in incorrect locations.
- **A named-market list is a first-class alternative to a radius for every archetype.** A radius that crosses a national border, a mountain range, or a metro boundary is a lie — which matters directly under a two-country working scenario.

Note also: filtering venues by *distance from me* and filtering performers by *will travel to me* are different questions behind the same interface affordance. For fixed-location archetypes, distance is an **informational and sort factor, not a hard eligibility bar**.

## 5. Eligibility

`EligibilityDecision` is one named concept, not scattered conditionals. Named inputs:

1. Publication state — `Published` or `Stale` only.
2. Category eligibility for the queried category or archetype.
3. `LocationEligibility` per §4.
4. `RequestIntake` state — surfaced, not silently excluded (§8).
5. Trust state — no blocking moderation state; not suspended.
6. **Entitlement — a constant input in V1**, always eligible on the free tier.

Two invariants:

- **`EligibilityDecision` must never gain a "paid" branch.** Paid placement is a separate pipeline, not a branch here.
- **Trust and quality gates decide first; a future entitlement may narrow eligibility but may never bypass them.** Payment must never buy publication.

A legacy record is not a `ProviderProfile`, so it is **structurally incapable** of being eligible — no flag is required to exclude it.

## 6. Organic ranking inputs

Ranking inputs are **organic attributes only**. Candidate inputs, all `PROPOSED` and all deterministic:

| Input | Note |
|---|---|
| Constraint match strength | How well the offering matches the requested category and filters |
| Geographic fit | Distance or market specificity, per archetype semantics |
| Profile completeness beyond the publication minimum | |
| Freshness | Last confirmation or update recency |
| `RequestIntake` state | **`unconfirmed` may NOT be a ranking input in V1.** Eligibility surfaces it; ranking must not demote it. See the note below — this was a defect found in critical review, not an original position |
| Internal responsiveness observation | **Only if introduced as a governed, published provider quality attribute on Provider** — never by a `Discovery → Demand` dependency, which would let demand signals become a relevance term with no governance (`R-004`) |

**Ranking must be explainable.** Every result slot carries a ranking explanation. This is not a nicety: once sponsorship exists, an unauditable ordering cannot prove organic and paid separation, and `R-004` becomes unfalsifiable.

**Ranking quality at launch is bounded by data, not by engine capability.** At launch there are zero clicks, zero conversions, zero reviews, and no session history. Relevance tuning, learning-to-rank, and vector recall all feed on behavioural signal that does not exist yet.

### Why `unconfirmed` may not demote a provider in V1

`RequestIntake` decays from `accepting` to `unconfirmed` after a governed freshness window (`ADR-005`). Letting that decay also lower a provider's rank would reproduce **exactly** the failure mode `DB-10` rejects date-filtered search for: a system clock silently suppresses supply for a reason the provider never learns, and the cause is undiagnosable from data. In a cold-start market that is a worse trade than a stale signal.

Therefore, in V1:

- **Eligibility** may surface `unconfirmed` — the customer sees the freshness state honestly.
- **Ranking** may not use it. `unconfirmed` becomes a permissible ranking input only after the share of impressions and requests lost to it is a measured V1 metric with an accepted bound.
- **The decay window length is an owner and product decision, not a system default.** P02 states that a stale positive signal must decay; it does not decide how fast.

This correction came from adversarial critical review of P02's own draft. It is recorded rather than silently applied so the reasoning is auditable.

## 7. Sponsored separation

| Requirement | Detail |
|---|---|
| `placementBasis` on every result slot | The only V1 value is `organic`. **Carried from day one, while sponsorship does not exist.** |
| The same basis on every measurement event | Impressions, clicks, and requests record placement basis **and position**. Without this, historical organic data becomes incomparable the moment sponsorship launches — the `R-004` and `R-018` failure mode |
| Disclosure marker per slot | Distinct in human **and** machine channels |
| Separate pipeline, not a scorer term | Sponsorship later becomes a separately allocated, separately labelled result section. Adding it means adding a basis value, not changing the result contract |
| Forbidden | **A `featured` or `sponsored` boolean on a profile or in a ranking input.** This is the named anti-pattern |

Carrying placement provenance in V1 costs one enumerated value and one discipline. Retrofitting it after sponsorship ships requires unpicking ranking, re-deriving historical organic metrics, and re-explaining trust to users who already saw a corrupted ordering. **The cheap path is also the one that teaches you how to price the thing**, because position-to-request attribution is only obtainable from organic data.

## 8. Zero-result and degraded-result behaviour

| Situation | Required behaviour |
|---|---|
| No eligible supply | **Never convert the query into a broadcast.** Explain coverage honestly, offer correction or relaxation, and record the unmet-demand signal |
| Distinguishing failure modes | *No supply in this category* and *no supply in this area* are different facts about the market and must be separately recorded. This distinction is the entire basis for later deciding whether an interpretation problem or a coverage problem exists |
| `undetermined` candidates present | Surfaced with their uncertainty, never silently dropped and never presented as confirmed matches |
| Provider not accepting requests | Shown as such. **"Not accepting requests" is not date unavailability** and must not be worded as either |
| Progressive relaxation | Permitted and explained; the customer must see which constraint was relaxed |
| Recording | Every zero-result search records the constraint shape, the result count, the `undetermined` count, and the `Market` |

## 9. Bilingual labels

- Filtering and eligibility operate on **language-neutral identifiers**, so they are language-independent by construction.
- Labels and synonyms are localized data over those identifiers.
- **A governed synonym and alias table on the taxonomy absorbs a large share of surface-form variance** — "mariachi" / "mariachis" / "banda" / "norteño" — at near-zero marginal cost, and it is the cheap alternative that must be tried before any model is funded.
- Provider narrative carries an explicit `contentLocale`; machine-translated content must be explicitly marked as such so no consumer presents a translation as the provider's own words.
- Cross-language discovery in a bilingual market is a **content-model question, not a search-engine question** (`Q-020`, `A-015`).

## 10. Freshness requirement

| Path | Requirement |
|---|---|
| Search results in V1 | `read-your-writes`. No staleness exists, because there is no derived store |
| Public profile projection | Re-versioned synchronously on publication or edit |
| **Suppression, deactivation, and moderation removal** | **Bounded short propagation to every public surface, independent of any rebuild.** This is the only near-realtime requirement in V1 |
| If a derived store is ever introduced | Bounded, **measured** staleness becomes a requirement; the new "published but not yet discoverable" state must be shown honestly; and reindex on taxonomy change becomes an operational obligation |

## 11. The measured threshold for dedicated search infrastructure

Decompose what the working scenario's query actually requires:

| Capability | Reduces to | Needs dedicated search infrastructure? |
|---|---|---|
| Governed category filter | Equality or containment over a controlled identifier set | No |
| Geography and service-area eligibility | Radius or zone containment, or point proximity — standard geographic predicates | No |
| Organic ranking | A deterministic ordered read over a filtered candidate set | No |
| Bilingual labels | Language-neutral identifiers plus localized label rows; filtering is on identifiers | No |
| Zero-result handling | Query composition plus recording | No — and a dedicated engine makes this **harder**, because opaque scoring fights the explainability requirement in §6 |
| **Free-text relevance over provider narrative, with analyzers, stemming, and typo tolerance per language** | Genuinely different | **The only real candidate — and it is not in V1 scope.** `docs/05-roadmap/mvp-scope.md` scopes V1 to governed service-or-category plus geography search, which is structured search |

The transportation archetype's origin-destination corridor is the one genuine outlier that does not reduce to a containment predicate. **That is an argument for excluding transportation from the launch archetype set, not for buying search infrastructure.**

### Cost of a separate read store, stated so the later decision is made against full cost

| Cost | Direction | Driver |
|---|---|---|
| Build | MEDIUM | Indexer, field mapping, backfill, reindex tooling |
| Recurring fixed | **HIGH — likely the largest single new fixed line at this scale** | A new always-on component |
| Variable | MEDIUM | Size grows with providers × locales × fields, and **every taxonomy change is a full reindex** — which `R-010` guarantees will happen |
| Operator | HIGH | Divergence detection; reindex windows; **"I updated my profile and I cannot find myself" becomes a permanent support queue** |
| Cognitive | HIGH | Two sources of truth; every investigation begins with which one is wrong |
| Product | — | Creates the "published but not yet discoverable" state |
| Exit | MEDIUM — the one good property | A derived store is rebuildable, which is precisely why staging it is correct |

### Triggers — any one, with thresholds set by a named owner at approval

1. **p95 latency of the eligibility-and-ranking query exceeds the approved budget on the approved instance class, *after* indexing and query tuning**, with candidate-set size and query mix recorded.
2. **An approved capability is provably absent** — bilingual free-text relevance with typo tolerance showing measured gains over the deterministic baseline **on a labelled query set that must exist first**.
3. **Filtered facet-count cardinality makes counts infeasible** at the approved page shape.

**Instrumentation this requires in V1**, without which the triggers are decoration: query latency recorded with candidate-set size and filter shape; zero-result and abandoned-query logs with the constraint set attempted; and supply counts per `Market` so an interpretation failure can be told apart from a coverage failure.

The reported ~43,000 legacy registrations are owner-reported and unaudited (`A-001`, `R-001`) and **may not size any index, store, or compute**.

## 12. What this document deliberately excludes

Semantic or vector retrieval · learning-to-rank · personalization · query-understanding models on the critical path · autocomplete infrastructure · faceted-count infrastructure beyond what the primary store provides · saved searches and search alerts (see the `DemandWatch` question, `Q-031`) · any recommendation system.

## Sources

`docs/05-roadmap/mvp-scope.md` · `docs/02-architecture/data-architecture.md` (the staged hypothesis this document sharpens) · `docs/01-product/feature-inventory.md` · `docs/01-product/user-journeys.md` · `docs/01-product/product-vision.md` · `docs/00-context/product-context.md` · `docs/00-context/assumptions.md` (`A-001`, `A-006` `SUPERSEDED`, `A-013`) · `docs/05-roadmap/risks.md` (`R-001`, `R-004`, `R-010`, `R-011`, `R-015`, `R-018`, `R-019`) · `docs/07-research/ai-discoverability.md` · `docs/07-research/search.md` · `SRC-013`.
