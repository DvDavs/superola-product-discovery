# ADR-014 — One PostgreSQL store with schema-per-module, and geospatial capability confined to a single predicate

- Status: PROPOSED
- Date: 2026-08-11
- Decision owner: David (technical decision owner for this repository)
- Related evidence/requirements: `docs/03-technology/technology-evaluation.md` §4.1–§4.3, §4.11–§4.12; `p03-decision-inputs.md` `D-01`, `D-02`, `D-03`, `D-11`, `D-12`; `ADR-001`, `ADR-006`; `system-architecture.md` §4; `search-architecture-requirements.md` §4, §11; `data-architecture.md`; `R-007`, `R-031`, `R-034`, `R-037`, `R-038`, `R-039`, `R-049`; `Q-034`, `Q-035`; `SRC-013`
- Supersedes / superseded by: N/A

## Problem / context

`D-01` requires a store satisfying the twelve atomic units in `system-architecture.md` §4, module-owned data with **no cross-module joins or referential constraints**, append-only audit not deletable by the domain-deletion path, per-record retention and deletion, batch enumeration and reversal, point-in-time recovery, bulk export, and small-team operability. `D-02` requires four geographic predicates across five archetypes with a three-valued eligibility outcome. `D-03` requires a staged search strategy with measured promotion triggers.

`R-007` is live here: David has PostgreSQL and PostGIS experience, and the repository exists partly to stop that becoming the selection argument.

## Constraints

No traffic evidence (`SRC-006` NOT RECEIVED). The unaudited legacy count sizes nothing (`R-031`). One part-time operator. A coordinate without sufficient precision may not drive distance eligibility, and `undetermined` must be representable rather than coerced. Every quantity carries its unit; every instant carries the zone it belongs to — **for event dates, the service location's zone.** Modelled load peaks under about ten requests per second at Growth.

## Options considered

### Option A — PostgreSQL 18, one cluster, one logical database, schema-per-module; PostGIS adopted but used by exactly one of four predicates (recommended)

**Three capabilities genuinely differentiate it** for this model: **partial indexes** carrying the publication allowlist predicate inside the index; **in-core Spanish and English full-text search** with accent folding and trigram similarity, which is the entire Stage-2 deferral of a search engine; and a **variable-depth tree extension** for the `Place` parent chain.

**Two further capabilities were listed in an earlier draft and struck on adversarial review**, because they do not discriminate against the runner-up: declarative partitioning (the runner-up has range partitioning, and *"turns a vacuum problem into a detach"* is circular, since vacuum is a PostgreSQL-specific problem), and skip-locked queue semantics (the runner-up has them too). Both are real and load-bearing; **neither is a reason to prefer this store.** A third — array columns with containment indexes — is **arguable and unresolved**: the runner-up offers multi-valued indexes over JSON arrays, and whether they satisfy the `ServiceAreaDeclaration` semantics was not checked.

### Option B — MySQL 8.4 LTS

**Not rejected on evidence.** Verified geospatially adequate for this workload. The differentiators above were **not** checked against it before research was exhausted. Recorded as runner-up rather than eliminated, because presenting an unverified comparison as a conclusion is precisely the `R-007` failure.

### Option C — A document or multi-model store

Rejection factors: P02's model is relentlessly relational — 40+ entities, heavy many-to-many structure, and a rule that an offering attribute value must reference an existing definition with **no orphan keys**, which `domain-model.md` calls *"the legacy failure in miniature."* A document store's advantage is denormalised aggregate reads; this product's core query joins across aggregates and enforces referential discipline within a module. **It would move the exact constraint that killed the legacy platform out of the database and into application code.** A multi-model store is bought to avoid running three stores; this design runs one.

### Option D — A distributed SQL store

Rejection factors: its value is horizontal write scaling and multi-region, both explicitly rejected by P02, and it imposes serializable-retry handling on every transaction — application complexity bought for a problem V1 does not have.

### Option E — Plain PostgreSQL with spherical distance functions, no geospatial extension

Genuinely viable: index-accelerated radius filtering with nothing beyond contributed modules. Rejection factor: it assumes a perfect sphere, which forces an owner to approve a numeric tolerance on an eligibility boundary the repository has already made a **correctness** concern. Its point-based operator additionally hardwires statute miles, against the explicit-unit rule. **Retained as the fallback if the extension is ever removed.**

## Decision

Option A, with four scoping rules that are the substance of the decision:

1. **Schema per module** for namespacing and grants. **Cross-module `SELECT` is permitted**; writes and referential constraints are module-scoped; joins are prevented in the build (`ADR-013`) and cross-schema foreign keys by a catalog test (`R-034`). Per-module write roles remain a trigger-gated escalation. **Per-module roles must NOT be used as the primary mechanism, because they would forbid the cross-module read `system-architecture.md` §4 requires** (`R-037`).
2. **The geospatial extension is used by exactly ONE of the four predicates** — radius-from-base. Point-in-named-place is a tree-ancestry test; postal-prefix zones are string prefix matching; proximity sort runs over an already-filtered set. **A build-enforced rule prevents its use in the other three.**
3. **Search is staged**, with no separate store in V1 and the numbered promotion triggers in `technology-evaluation.md` §4.3. The governed synonym table ships at Pilot regardless.
4. **Audit lives in the same database, separate schema, separate owning role**, with no foreign key to any domain table and no destructive grant to the application role.

**Approval still required:** David. **And read the recommendation as "PostgreSQL is verified sufficient", not "the alternatives were verified insufficient."**

## Rationale

The three surviving capabilities in Option A are consumed by named P02 requirements rather than admired in the abstract, and the most load-bearing is the second: `search-architecture-requirements.md` §11 identifies bilingual free-text relevance with per-language analysis and typo tolerance as *"the only real candidate"* for dedicated infrastructure, and the store supplies a credible version of exactly that in core. Combined with §6's statement that ranking quality at launch *"is bounded by data, not by engine capability"* — zero clicks, zero conversions, zero reviews — **those two facts together are the whole `D-03` argument.**

On geography: **three of the four predicates are not geospatial problems**, which `search-architecture-requirements.md` §11 obscures by collapsing them into one row. The argument for adopting the extension is not speed — it is that an ellipsoidal distance **deletes a governance decision** instead of asking an owner to approve a spherical-approximation tolerance on an eligibility boundary tied to a reported legacy defect. **Confining it to one predicate converts platform-level lock-in into a single-function swap**, which is what `system-architecture.md` P10 asks for.

An unexpected result worth recording: **`ADR-001` makes the discovery query cheaper, not more expensive.** Because cross-module joins are forbidden, the application resolves the category subtree and the place ancestor chain first — both tiny, both governed, both ideal in-process cache contents — and passes them as arrays into a **single-module query**. That is simpler and faster than the joined version would have been.

## Consequences

Positive: one store, one backup, one investigation surface; search freshness is read-your-writes and free; the *published but not yet discoverable* product state does not exist; the extension dependency is one function wide.

Negative: **analytics is 77–87% of database size at every scenario**, and nobody owns its retention period (`Q-034`). Monthly partitioning must be decided **before launch** — retrofitting onto ~96 million rows is a migration. A second consequence: schema-per-module means **every new table needs a grant, and forgetting one is a runtime error, not a build error.**

Operational: no database administrator; the recurring cost is reviewing annual major-version upgrades, and a full-text reindex if Stage 2 has shipped when the major version changes.

Modelling obligations that are unrecoverable if missed: **a single timestamp column does not satisfy data rule 6** — the zone the instant belongs to is a second column (`R-039`); and **place in analytics records is carried at a coarse governed level only**, because at ~150 providers a fine-grained tuple can re-identify (`R-049`).

## Cost implications

Managed instance $15.15 → $60.90 → $122.10 per month across the three scenarios. **The sizing lever is analytics retention, not the marketplace**, which never exceeds ~4.7 GB. At Growth the discovery working set is ~395 MB and fits in memory — **so latency is unlikely to be the trigger that promotes search**; facet-count cardinality will bite first, and that is a page-shape decision (`Q-035`). Extension cost: a few megabytes of catalog. Full detail in `cost-model.md`.

## Lock-in and exit implications

Stock export format, no proprietary encoding. **The exit window closes with data volume** — at Pilot the move is a maintenance hour; above roughly 100 GB it needs logical replication and becomes a project. The expensive parts of a later move are the full-text work if Stage 2 shipped, the geospatial column, and partial indexes. **Each is a reason to keep the store-specific surface small and named, which rule 2 does deliberately.**

**"Deletion proof" is not a database feature and must not be recorded as one** (`R-038`). It is a product capability: a per-subject enumeration complete by construction, an audit record written by a different role, a re-run returning zero, and **an explicitly disclosed exclusion for the recovery window.** A recovery window that can restore to before a deletion is a truthful limit and must be stated, not hidden — and any restore must be followed by re-application of the suppression list, or `R-023` fires.

## Security and privacy implications

Audit immutability rests on **ownership and revoked grants**, not on row-level security — using row-level security as an append-only control is a category error, since privileged roles always bypass and owners bypass unless forced. A `BEFORE UPDATE OR DELETE` trigger is the only in-database control binding a superuser, and **a truncate fires a different trigger, so a separate one is required or the control has a hole.** The retention sweep runs under a role that is **not** the application role — granting the application delete so it can expire old rows silently undoes the whole control. Precise provider base location is readable by the eligibility computation and never emitted; the publication allowlist is applied as a predicate **inside** the discovery read, sharpened by `ADR-013` into a closed type.

## Reconsideration trigger

p95 write latency on the *provider responds* transaction exceeds the approved budget on the approved instance class **after** index tuning and **after** analytics ingest has moved off the primary write path; **or** more than one schema migration per release window is blocked by lock contention; **or** the deep comparison against the runner-up is completed and it matches on partial indexes and array containment, which reopens `D-01`.

For the geospatial extension specifically: the share of eligibility evaluations returning `undetermined` for base-precision reasons exceeds an owner-approved ceiling — which indicts the `D-07` source, not the predicate; **or** an approved place requires a polygon boundary, which is a trigger to *expand* its use.

## Validation

Verified 2026-08-11 from primary sources: current major version and support horizon; extension availability across every managed provider checked — **which disproved the assumption that this requirement would eliminate hosting candidates**; the Spanish stemmer, accent-folding dictionary and trigram operator classes; the documented queue-table locking clause; partition detach semantics; and the accent-folding function's volatility class, which forces write-time normalisation rather than an index expression.

Required instrumentation: per-transaction-class latency histograms tagged with the atomic-unit name from §4; per-search latency with **candidate-set size** and constraint shape; eligibility outcome with the `undetermined` reason code; migration duration; and a schema test asserting the application role lacks destructive privileges on the audit schema and that no foreign key crosses a schema boundary.
