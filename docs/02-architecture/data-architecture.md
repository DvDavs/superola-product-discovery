# Data Architecture — Advanced by P02

> **Status:** conceptual data requirements are now specified by P02. **No database product, search product, or persistence technology is selected.** Physical schema design remains out of scope for P02 and is a P03 input.
>
> **Standing qualifier:** `DAVID WORKING ASSUMPTION — OWNER VALIDATION PENDING` (`SRC-013`). **Scenario stamp: S-1.**

## What P02 specified

| Concern | Where it is specified |
|---|---|
| Authoritative entities, identity concepts, ownership, and invariants | [domain-model.md](domain-model.md) §1 |
| Twenty-three required conceptual distinctions | [domain-model.md](domain-model.md) §2 |
| Category variability — the governed archetype and attribute-definition model | [domain-model.md](domain-model.md) §3, [`ADR-007`](adr/ADR-007-shared-core-plus-governed-category-archetypes.md) |
| Geography, locale, timezone, currency, unit, and address semantics | [domain-model.md](domain-model.md) §4, [internationalization-architecture.md](internationalization-architecture.md) |
| Lifecycles and audit-worthy transitions | [domain-model.md](domain-model.md) §5, §8 |
| Transactional boundaries and consistency requirements | [system-architecture.md](system-architecture.md) §3, §4 |
| Privacy classification, retention, deletion, consent, and access control | [security-privacy-architecture.md](security-privacy-architecture.md) §5–§12 |
| Media asset versus reference, and rights | [domain-model.md](domain-model.md) §1.3 |
| Read models versus sources of truth | [`ADR-006`](adr/ADR-006-discovery-owns-eligibility-ordering-and-placement.md), [search-architecture-requirements.md](search-architecture-requirements.md) §1 |
| Legacy provenance, separability, suppression, and rollback | [`ADR-009`](adr/ADR-009-legacy-records-are-a-distinct-non-discoverable-boundary.md), [security-privacy-architecture.md](security-privacy-architecture.md) §7 |
| The measured threshold for dedicated search infrastructure | [search-architecture-requirements.md](search-architecture-requirements.md) §11 |

## Data ownership rules that constrain any persistence choice

1. **Modules own their persisted data.** Cross-module references are by identifier only. **No cross-module query joins and no cross-module referential constraints** — this, not the shared process, is the real coupling trap in a single deployable, and it is what determines whether later extraction is cheap.
2. **One source of truth in V1.** Discovery queries it directly, so search freshness is read-your-writes and free, and the product state *published but not yet discoverable* does not exist.
3. **`PublishedSnapshot` is allowlist-derived, versioned, and rebuildable.** A field is public because it is explicitly publishable; unknown fields are **absent, not passed through**. The field-set version is recorded per projection so *"was this field public on date D?"* is answerable.
4. **Audit holds references, never payload copies**, and must not be deletable by the same path that deletes domain data — otherwise a deletion request erases the evidence that it was requested. It needs a stated retention period.
5. **Analytics is write-only.** No module reads it, no decision depends on it, and no needed state may exist only as an event. Every event carries `market`, `locale`, `archetype`, and — where ordering is involved — `placementBasis`, **in the record shape from day one**, because history that was never recorded segmented cannot be segmented later.
6. **Every quantity carries its unit; every monetary value carries its currency; every instant carries the zone it belongs to** — and for event dates, that zone is the **service location's**, not the server's or the account's. Ambiguous historical timezone and currency values are often unrecoverable.
7. **Absence must be distinguishable from negation.** Every attribute definition declares whether missing means `no` or `unknown`. Treating missing as matching is what killed legacy search.
8. **Imported records carry provenance per record *and per field***, plus a lawful-basis marker whose default absence means unusable, and they are **structurally not provider profiles**.

## Corrected staged search hypothesis

The prior `PROPOSED` staged hypothesis — relational and geographic capability first, dedicated search only when measured requirements justify it — is **retained and sharpened**. Two corrections:

- It read as an inevitability with a schedule. It is a **branch that may never be taken.**
- It stated only a latency rationale. The **operational** costs must be on the record so the later decision is made against full cost: freshness becomes a product state; divergence becomes a permanent support queue; **every taxonomy change becomes a full reindex**, which `R-010` guarantees will happen; and every bug investigation begins with *which source is wrong?*

The measured thresholds, and the V1 instrumentation without which they are decoration, are in [search-architecture-requirements.md](search-architecture-requirements.md) §11.

## Gap this placeholder previously had

The prior required-work list demanded "projections without premature duplicate sources of truth" but said nothing about **durable deferred work** — which is the one genuine asynchrony requirement V1 has. That gap is exactly where a message broker gets sold in. The requirement is now stated in [system-architecture.md](system-architecture.md) §5 and carried to P03 as `D-04`.

## Still out of scope for P02 — P03 and P05 inputs

- Physical schema, column types, indexes, partitioning, and replication.
- Store selection, and whether geographic predicates live in the same store (`D-01`, `D-02`).
- The durable-deferred-work mechanism (`D-04`).
- Media storage, processing, and delivery, including the **link-out alternative** for performer audio and video that the repository had never named (`D-06`).
- Place resolution and geocoding source, per country (`D-07`).
- Audit and measurement storage (`D-11`).
- Legacy profiling, mapping, quality classification, migration rehearsal, reconciliation, and rollback (P05, `D-14`).
- Scale and query evidence. **The reported ~43,000 legacy registrations are owner-reported and unaudited (`A-001`, `R-001`) and may not size any store, index, or compute.** `SRC-006` is NOT RECEIVED, so there is no traffic evidence at all.
