# Technology Evaluation — P03

> **Status:** `PROPOSED — DAVID APPROVAL REQUIRED`. Provenance: `TECHNICAL_DISCOVERY`.
>
> **Standing qualifier:** `DAVID WORKING ASSUMPTION — OWNER VALIDATION PENDING` (`SRC-013`). **Scenario stamp: S-1** (one launch geography, one production locale).
>
> **Access date for every external price and capability claim: 2026-08-11.** Prices change; every one must be re-verified before a purchase decision.
>
> **This document recommends ONE technology architecture.** Alternatives are recorded as runner-ups with the reason each lost, not as parallel stacks.
>
> **Nothing here is adopted.** `docs/03-technology/technology-radar.md` records movement; movement into an accepted state requires David's approval and, where the ADR convention applies, an ADR.

## 1. The three findings that reframe the evaluation

These came out of the research and they change what the criteria are, so they belong before the tables.

### 1.1 Throughput is not a selection criterion, and it is not close

Total application requests per month = public page views + searches + RFQs + messages. Media and static assets are excluded — they belong to object storage and the delivery network.

| | Pilot | Early Marketplace | Growth |
|---|---|---|---|
| Total app requests / month | 19,800 | 253,000 | 2,270,000 |
| **Mean requests / second** | **0.008** | **0.098** | **0.88** |
| At an assumed 10–15× diurnal peak | 0.08–0.12 | ~1.0–1.5 | **~8.8–13** |

**At the Growth scenario the entire marketplace peaks under about ten requests per second.** Every candidate stack serves that on one small instance with large headroom. Any argument of the form *"X is faster than Y"* is inadmissible in this evaluation — the benchmarks that separate these candidates operate three to four orders of magnitude above Superola's ceiling.

Three consequences:

1. Runtime cost reduces almost entirely to the **memory floor** — the smallest production-safe instance — not to CPU or requests per second. That is one VM tier.
2. The binding criteria are the ones P02 already named: mechanical boundary enforcement, transactional correctness at `system-architecture.md` §4, one write path, the allowlist projection, one machine-access enforcement point, and one part-time operator. Those are **correctness and maintenance** criteria.
3. A counter-quantity worth carrying: an assumed 30-second unread-count poll across 200 concurrent authenticated sessions is ≈6.7 requests/second — **comparable to all public traffic at Growth combined.** Polling, not page views, is the plausible dominant load. This is exactly why `system-architecture.md` §3 requires every polling interval to be stated with its query cost.

### 1.2 Human time is the dominant cost sensitivity — but it is not part of the bill

**Two quantities, deliberately not summed** (`AGENTS.md`, *Cost framing*; `cost-model.md` §1). **Currency: USD.**

**The invoice:**

| | Pilot | Early Marketplace | Growth |
|---|---|---|---|
| **Infrastructure and services, cash** | **USD $75.98** | **USD $192.99** | **USD $560.79** |

**An illustrative sensitivity — not a forecast, not a budget, not a committed operating cost:**

| | Pilot | Early Marketplace | Growth |
|---|---|---|---|
| Infrastructure operations labour | 3.25 h → USD $244 | 5.00 h → USD $375 | 9.00 h → USD $675 |
| Marketplace operator labour | 8.35 h → USD $334 | 55.42 h → USD $2,217 | 340.58 h → USD $13,623 |
| **Illustrative human total** | **~USD $578** | **~USD $2,592** | **~USD $14,298** |

Rate assumptions, stated separately because the work differs: **infrastructure operations at USD $75/hour** (specialist, band USD $40–150), **marketplace operator case work at USD $40/hour** (non-specialist, band USD $20–60). Both are assumptions, neither is owner-validated, and **every human figure scales linearly with them.** The workload volumes have no measured basis either.

**What the comparison supports: human time is roughly 88–96% of the two tables combined at every scenario, so any decision that saves USD $30 on the invoice and adds one hour of monthly labour is a loss.** That conclusion is robust across the entire rate band. **What it does not support is a combined headline — *"Superola costs USD $14,859/month"* is not a claim this analysis makes.** *(Corrected in P03.1: this section previously carried a `Total cost of ownership` row.)*

**Consequence for every decision in this document: any choice that saves $30 on the invoice and adds one hour of monthly labour is a loss.** `system-architecture.md` P21 already required architecture decisions to be evaluated for the manual queue volume they create. P03 confirms it with numbers, and the numbers are not close.

### 1.3 The volume assumption P03 gave its own researchers was wrong, and the correction is a finding

P03's work packages carried a geocoding assumption of **1,500 / 20,000 / 200,000 address-autocomplete sessions per month**. Those numbers presuppose that customer-side location input resolves through a geocoding vendor.

`domain-model.md` §1.4 does not describe that design. It defines a governed `Place` with a stable internal identifier, a variable-depth parent chain, localized names and synonyms, and states that **provider-typed place text is a resolution input, never a `Place`**. Under that design, customer-side location resolves against Superola's own governed list and makes **no vendor call at all**.

**The two readings differ by ~70× against the same vendor and ~132× against the disqualified platform (§4.7)** (§4.7). The brief's assumption was corrected rather than followed. It is recorded here because it is exactly the failure `R-031` warns about: a volume figure with no source silently buying infrastructure.

## 2. Volume scenarios — stated assumptions, not evidence

Used identically across every work package so cost lines are comparable. **Not derived from any Superola measurement.** `SRC-006` is NOT RECEIVED; the owner-reported ~43,000 legacy registrations are unaudited (`A-001`, `R-001`) and size nothing here.

| | Pilot / Validation | Early Marketplace | Growth |
|---|---|---|---|
| Published providers | 150 | 1,500 | 10,000 |
| Service offerings | ~400 | ~4,500 | ~35,000 |
| Public page views / month | 10,000 | 150,000 | 1,500,000 |
| Marketplace searches / month | 8,000 | 80,000 | 600,000 |
| RFQs / month | 300 | 3,000 | 20,000 |
| In-platform messages / month | 1,500 | 20,000 | 150,000 |
| Transactional emails / month | 3,000 | 35,000 | 250,000 |
| Stored images (originals) | 1,200 | 12,000 | 100,000 |
| Media bytes served / month | ~15 GB | ~250 GB | ~2.5 TB |
| Analytics events / month | ~50,000 | ~800,000 | ~8,000,000 |
| Application log volume / month | ~2 GB | ~25 GB | ~250 GB |
| **Geocoding calls / month (corrected, §1.3)** | **~55** | **~330** | **~1,320** |
| Markets / locales | 1 / 1 | 1–2 / 1–2 | 3+ / 2 |

## 3. The recommended stack

| # | Decision | **Selected** | Runner-up | Reversal |
|---|---|---|---|---|
| — | Backend language and framework | **Kotlin on Spring Boot 4.1, Java 25 LTS** | C# / .NET 10 | **VERY HIGH** |
| `D-09` | Web rendering — **NOT DECIDED. `ADR-020`, deferred to P04** | **Working baseline only:** server-rendered in the framework's own view layer, htmx for partials, React islands where a stateful surface demands it. **P04 may conclude a richer client is justified for named surfaces** | Full-stack JS/TS framework; a component-framework client for named authenticated surfaces | LOW–MEDIUM, **and per-surface** |
| `D-10` | Deployment | **One managed-PaaS deployable + one worker process from the same artifact, single US region** | Self-managed VPS | LOW today |
| `D-10` | Boundary enforcement | **Spring Modulith `allowedDependencies` + ArchUnit + a cross-schema foreign-key test** | Build-tool subprojects | LOW |
| `D-01` | Record store | **PostgreSQL 18, one cluster, one database, schema-per-module** | MySQL 8.4 — **not disqualified**, see §4.1 | MEDIUM |
| `D-02` | Geographic predicates | **PostGIS, confined to exactly ONE of four predicates** | `cube` + `earthdistance` | **LOW by design** |
| `D-03` | Search | **Staged. No separate store in V1.** SQL → PostgreSQL-native bilingual FTS → engine only on a numbered trigger | — | LOW (derived stores rebuild) |
| `D-04` | Durable deferred work | **A job table in the SAME PostgreSQL instance** | Cloud queue | LOW |
| `D-05` | Notification channel | **AWS SES** | Postmark | LOW |
| `D-06` | Media | **S3-compatible object storage with zero egress + pre-generated fixed derivative set + LINK OUT audio and video** | Metered-egress CDN storage | LOW–MEDIUM |
| `D-07` | Place resolution — **split by P03.1: architecture recommended, vendor on `HOLD` (legal), map moved to P04** | **Governed internal `Place` list (open gazetteer) + a permanent-storage geocoder for provider base addresses ONLY.** No rendered map is **assumed** in the cost model; it is no longer **decided** here | OpenCage | LOW (~USD $50, four days) |
| `D-08` | Authentication — **`ADR-017` on `HOLD` after P03.1 verification** | **BUILD on the framework's established security library, credentials in `D-01`** — a *working* recommendation, not a settled decision | Supabase Auth (case strengthened); Clerk | BUILD→BUY easy; **BUY→BUILD is vendor-specific and verified**, not uniformly hard |
| `D-11` | Observability | **Free-tier error tracker + free-tier log store + external uptime + a dead-man's switch on jobs. $0 at Pilot.** Audit in the same database, separate schema, separate owning role | Single-vendor bundle | LOW |
| `D-12` | Labels and localization | **In `D-01`, Catalog's schema, versioned label sets** | — | LOW |
| `D-13` | AI intent mapping | **DO NOT SPEND.** Gate closed and unanswerable in P03 | — | n/a |
| `D-14` | Legacy pipeline | **GATED on `G-09`.** Not evaluated | — | n/a |
| `D-15` | Payment | **EXCLUDED.** `G-02` unresolved | — | n/a |
| — | CI/CD | **Forge-hosted pipeline as the gate, host-native deploy hook as the deploy step** | Host-native auto-deploy alone | LOW |
| — | Infrastructure-as-code | **A committed platform-native declarative file. No Terraform.** | — | LOW |

**Everything above is `PROPOSED — DAVID APPROVAL REQUIRED`.**

## 4. Per-decision records

### 4.1 `D-01` — Marketplace record store

**Selected: PostgreSQL 18, one cluster, one logical database, schema-per-module.**

**Why, independent of familiarity — three capabilities, not five.** An earlier draft listed five; adversarial review correctly struck two, and the honest list is stronger for it because it makes clear how narrow the gap to the runner-up actually is.

1. **Partial indexes.** The publication allowlist is a constant predicate on every discovery query, and `search-architecture-requirements.md` §1 requires it applied **inside** the read. A partial index moves that predicate into the index, so the index contains only publishable rows. **Highest-value index in the system, and no verified equivalent elsewhere.**
2. **In-core Spanish and English full-text search** with accent folding and trigram similarity — **the entire basis for deferring a search engine** (§4.3). This is the single most load-bearing capability in the list.
3. **A variable-depth tree extension** for the `Place` parent chain that `internationalization-architecture.md` §3 mandates, with ancestor operators and an index.

**Struck as non-differentiating:**

- *Declarative partitioning for analytics retention.* The runner-up has range partitioning. And the framing *"turns a vacuum problem into a detach-and-drop"* is **circular** — vacuum is a PostgreSQL-specific problem, so solving it is not an advantage over a store that does not have it.
- *Skip-locked queue semantics.* Real, load-bearing for `D-04` — **and the runner-up has it too.** It is why the decision is comfortable, not why it is right.

**Arguable, and explicitly not resolved:** array columns with containment indexes. The runner-up offers multi-valued indexes over JSON arrays, which give indexed containment; **whether they satisfy the `ServiceAreaDeclaration` semantics was not checked.** That is the specific gap that would reopen this decision.

**Module ownership, and the mechanism question answered honestly.** Table-name prefixes enforce nothing. Separate schemas give namespacing and a place to hang grants. **Per-module database roles would genuinely prevent a cross-module join — and would also forbid the cross-module read `system-architecture.md` §4 requires**, since request creation must re-check eligibility against Provider source truth inside one Demand transaction. Resolution adopted (`R-037`): **schemas per module; cross-module `SELECT` permitted; writes and referential constraints module-scoped; joins prevented in the build.** Per-module write roles remain a trigger-gated escalation.

**Why the runner-up lost — and this is recorded precisely because it did not lose cleanly.** MySQL 8.4 was verified **geospatially adequate** and was **not** shown to be insufficient on the differentiators above; those checks were not completed. `R-007` forbids dressing an unverified comparison as a conclusion. **Read the recommendation as "PostgreSQL is verified sufficient", not "the alternatives were verified insufficient."** If someone closes that gap and MySQL matches on partial indexes and array containment, this decision reopens.

**Cost:** managed instance, $15.15 → $60.90 → $122.10 per month at the three scenarios on the modelled provider. **Analytics is 77–87% of database size at every scenario** (§4.11).

**Reversal difficulty: MEDIUM**, and rising with data volume — above roughly 100 GB a move needs logical replication and becomes a project rather than a maintenance window. Keep the PostgreSQL-specific surface small and named for exactly this reason.

**Reconsideration trigger:** p95 write latency on the *provider responds* transaction exceeds the approved budget on the approved instance class after index tuning and after analytics ingest has moved off the primary write path; **or** more than one schema migration per release window is blocked by lock contention. *Instrumentation:* per-transaction-class latency histograms tagged with the atomic-unit name from §4, and a migration-duration log.

### 4.2 `D-02` — Geographic predicates

**Selected: PostGIS, with exactly ONE of the four predicates depending on it.**

The decisive finding: **three of P02's four geographic predicates are not geospatial problems.**

| Predicate | What it actually is | Needs PostGIS? |
|---|---|---|
| Radius-from-base with an explicit unit | Great-circle distance | **The only candidate** |
| Point-in-named-place containment | **A tree-ancestry test** over the governed `Place` parent chain. No geometry exists | No — `ltree` ancestor operator, or an ancestor array with GIN |
| Delivery zone as a postal-prefix list | **String prefix matching.** Probe the offering's prefix set with the customer code's prefixes | No — btree, five index probes |
| Proximity for sorting | An ordered read over the **already-filtered** candidate set | No |

`search-architecture-requirements.md` §11 collapses all four into one row — *"standard geographic predicates"*. That collapse made a geospatial dependency look mandatory when it is optional for three of the four, and it should be corrected.

**And even the surviving predicate does not strictly require PostGIS.** `earthdistance` with `cube` and a GiST index performs index-accelerated radius filtering. Its documented limit is that it assumes a perfect sphere.

**So the argument for taking PostGIS is not speed — it is that it deletes a governance decision.** `internationalization-architecture.md` §3 states *"'50 miles' and '80 km' are different provider promises, and rounding changes who is eligible"*, and the reported legacy defect makes location correctness a first-class concern. A spherical approximation forces an owner to approve a numeric tolerance they have no basis to evaluate. An ellipsoidal distance removes the question. **That is a proportional reason to adopt an extension, and it is a much narrower reason than the repository's framing implied.**

**Confining it to one predicate is the point.** Reversal becomes one function body, one column type, one index — not a platform migration. This satisfies `system-architecture.md` P10 and converts a platform-level lock-in into a single-function swap. **A build-enforced rule must prevent future use of PostGIS in predicates 2–4 merely because it is installed.**

**The eliminator that was assumed and turned out not to exist:** PostGIS availability was expected to eliminate hosting candidates. It does not — every managed provider checked offers it. The hosting shortlist is decided on point-in-time recovery, price and version currency instead.

**Route corridor — P02's conclusion confirmed, its stated reason corrected.** Three P02 documents say route-corridor semantics *"do not reduce to a containment predicate."* As written that is **false** — a corridor is a buffered linestring and PostGIS evaluates it directly, index-accelerated. The conclusion survives on stronger grounds: **the route input does not exist** (a straight line between two cities is not a road route, and asserting eligibility from one is precisely the false claim P14 forbids); the **permit-jurisdiction dataset** has no named source and no owner; and the **query contract changes** from one customer location to two endpoints, across the form, the constraint set, the `undetermined` conditions, the zero-result recording and any future intent mapper. **Exclude transportation from the launch archetype set. The predicate is cheap; the route input, the permit dataset and the two-endpoint contract are not.**

**Reversal difficulty: LOW, by design.**

**Reconsideration trigger:** the share of `LocationEligibility` evaluations returning `undetermined` for base-precision reasons exceeds an owner-approved ceiling — which indicts the `D-07` source, not the predicate; **or** an approved `Place` requires a polygon boundary rather than a representative point, which is a trigger to *expand* PostGIS use. *Instrumentation:* eligibility outcome recorded per evaluation with the `undetermined` reason code.

### 4.3 `D-03` — Search progression

**Selected: staged, with no separate store in V1 and numbered promotion triggers.**

| Stage | What it is | Entered when |
|---|---|---|
| **0 — Pilot** | Authoritative relational query with the allowlist predicate. **No geospatial index, no FTS, no indexes beyond correctness constraints.** At ~400 offerings a sequential scan beats every index and costs nothing on write. **The governed synonym and alias table ships here regardless** — it is ~3 tables and ~1 MB, and `D-03`, `D-13`, `ADR-006` and §9 all name it as the cheap alternative that must be tried first | Launch |
| **1 — Early** | Four indexes: a partial btree on category filtered to publishable rows; GIN on the declared-place-ID array; GiST on the geography point; btree on postal prefix | `T1` |
| **2 — PostgreSQL-native text** | `tsvector` with the Spanish and English configurations plus `unaccent`, GIN-indexed; `pg_trgm` for typo tolerance; rank as one deterministic input among the organic set | `T2`+`T3` |
| **3 — Dedicated engine** | Only on a measured trigger, and even then §11's full cost table applies | `T5`–`T8` |

**Stage 2 is verified available in core**, including a Spanish Snowball stemmer, `unaccent` as a filtering dictionary, and trigram similarity with GIN and GiST operator classes.

**One gotcha that shapes the design: `unaccent()` is `STABLE`, not `IMMUTABLE`, so it cannot appear in an index expression.** Normalise at write time into a stored column rather than wrapping it to force immutability. That makes the normalisation rule a **governed, versionable artefact** instead of hiding it inside a `CREATE INDEX` — strictly better than the workaround.

**Promotion triggers, each with the V1 instrumentation that makes it observable.** Thresholds are marked `OWNER TO SET`, because §11 requires a named owner to set them at approval and inventing them here would violate the no-number-without-a-source rule.

| # | Trigger | Instrumentation required in V1 |
|---|---|---|
| `T1` | Publishable offering count exceeds `OWNER TO SET`, or a sequential scan of the offering table exceeds `OWNER TO SET` ms on the approved instance class | Monthly publishable-offering count; a recorded plan-and-timing sample of the discovery query |
| `T2` | Zero-result share exceeds `OWNER TO SET` **and** sampled review shows the failures are *interpretive*, not *coverage* | Zero-result records carrying the full constraint set, plus per-`Market` supply counts — **the two failure classes must be separable** |
| `T3` | The synonym table is **demonstrably exhausted**: over one review cycle the operator added synonyms for `OWNER TO SET` distinct zero-result surface forms and the zero-result share did not fall by `OWNER TO SET` | Synonym-table change log recording the surface form that motivated each addition, joinable to zero-result records |
| `T4` | Free-text search over provider narrative is **separately approved as product scope** — a scope gate, not a metric | — |
| `T5` | §11 trigger 1: p95 of the eligibility-and-ranking query exceeds the approved budget **after** indexing and tuning | Per-search `latency_ms`, **`candidate_count_before_ranking`**, `result_count`, `constraint_shape_hash`. **Without candidate-set size the number is uninterpretable** |
| `T6` | §11 trigger 2: a **labelled bilingual query set exists first**, and an engine shows measured relevance gains over the Stage-2 baseline on it | The labelled set itself. **This trigger cannot fire before it exists, by construction — which makes "our search is bad" unfundable as an assertion** |
| `T7` | §11 trigger 3: facet-count cardinality is infeasible at the approved page shape | Per-request facet-computation time and per-attribute cardinality per `Market`. **Check `Q-035` first — changing the page shape is free and removes this trigger** |
| `T8` | **New in P03.** Measured contention: p95 of the *provider responds* or *customer submits request* transaction degrades while discovery read load is high | Per-transaction-class latency tagged with concurrent read volume. **§11's triggers are all read-side; the real reason a marketplace splits search out is usually that reads started hurting writes, and nothing in P02 would observe that** |

**Explicitly not acceptable as triggers:** "search feels slow" without recorded candidate-set size; competitor parity; a vendor benchmark; the unaudited legacy count.

**Why latency is unlikely to be the trigger that fires.** At Growth the discovery working set — offerings, attribute values, service areas, places, profiles, plus 40% index overhead — is **≈395 MB**. It fits in memory on a small instance. **Facet cardinality (`T7`) will bite first, and it is a product page-shape decision wearing an infrastructure trigger's clothes (`Q-035`).**

**A cost-framing correction.** §11 says a dedicated engine is *"likely the largest single new fixed line at this scale"* because it is a new always-on component. That is true of per-hour managed clusters — one verified serverless search offering carries a **$350.40/month floor** at 8,000 searches/month. It is **not** true of usage-priced managed search, which verified at roughly **$0 at Pilot and ~$295/month at Growth**. **The §11 argument should rest on the costs that are pricing-model-independent**: a full reindex on every taxonomy change (`R-010` guarantees these), a permanent *"I updated my profile and cannot find myself"* support queue, two sources of truth, and a new *published but not yet discoverable* product state.

### 4.4 `D-04` — Durable deferred work

**Selected: a job table in the SAME PostgreSQL instance. Incremental always-on infrastructure cost: $0.00 at every scenario.**

`system-architecture.md` §5 demanded *"P03 must compare alternatives including one that adds no new always-on operational component."* That option does not merely tie — it wins on correctness.

| Requirement clause | Satisfied by | Cost |
|---|---|---|
| Durable | The row commits with the write-ahead log, is backed up, and is covered by the same point-in-time recovery as every business record | $0 |
| At-least-once | Claim with `FOR UPDATE SKIP LOCKED`, commit on success | $0 |
| Retry with backoff | Mature library support, verified | $0 |
| Idempotent handlers | An application obligation either way — and **easier here**, because the dedupe key and the business row are in one transaction | $0 |
| Dead-letter visible in an operator queue | A `FAILED` row you query, with a governed retry command recording actor, reason and time — which is what §9 requires, since *"a console action has no actor, no reason, and no audit record"* | $0 |
| Scheduling for sweeps | Recurring-task support in the same library | $0 |

**The load, at Growth:** 250,000 emails ÷ 2,592,000 seconds = **0.096 jobs/second average**, roughly 2/second at peak. Mature libraries document lock-and-fetch at 1,000+ executions/second. **Roughly 100× headroom at the largest modelled volume.** Every alternative is priced against a load two orders of magnitude below where any becomes necessary.

**Why the alternatives lost, stated correctly.** The naive framing — *outbox versus broker* — is wrong, and adversarial review corrected it: **the transactional outbox is exactly how a broker avoids the dual-write problem.** The honest comparison is **outbox alone versus outbox plus a broker plus a relay.** Since the table is common to both, **the broker is strictly additive** — a second always-on component, a second failure domain, a second vendor, and a relay process, bought at 0.096 jobs per second, buying no durability the table does not already provide. It also puts the dead-letter state partly outside the audit boundary, so a retry has no actor and no reason unless that is rebuilt too. What a broker genuinely buys is fan-out to unknown consumers, which §5 establishes V1 does not have.

A Redis-backed queue is precisely the new always-on component the clause asks you to avoid, and its default durability model — periodic snapshot, or one-second fsync — is a poor match for the word *durable* when the payload is a marketplace notification intent.

**Separately, and independently of the vendor comparison, `R-044` and the §4 correction stand:** §4 as written required the intent to be created *after* the business commit and forbade sharing that transaction, which opens a lost-notification window regardless of which mechanism dispatches it. That was a defect in P02, and it is corrected.

**Reconsideration triggers:** measured job-table contention where per-statement analysis attributes ≥20% of a request-path regression to job-table statements; sustained dead-tuple ratio above 30% for 24 hours with autovacuum running; sustained enqueue above 200 jobs/second (**note: ~2,000× the Growth average — this will not fire**); or `system-architecture.md` §5's own broker trigger, a second *independent* consumer with a real named owner. The first three move the **job store**; only the last justifies a **broker**, and they are different decisions.

### 4.5 `D-05` — Notification delivery

**Selected: AWS SES. Runner-up: Postmark.**

**The structural elimination first.** `ADR-010` requires a notification body to be rendered **only after a disclosure check**, in the domain, reading party classification and request state — data no vendor can see. **Every vendor's template, personalization and merge-field feature is therefore unusable by construction.** Superola is buying a pipe plus an event stream, not an email platform. That collapses most vendor differentiation.

**Why SES wins:**

1. **It models a third delivery state nobody else was verified to model.** Alongside bounce (permanent / transient / undetermined, with subtypes) and complaint (with feedback types), SES emits **`DeliveryDelay`** as a separate event with delay types including mailbox-full, spam-detected, and recipient-server-error. A message in flight is neither delivered nor failed. **Fold it into bounce and you suppress a working address; fold it into delivery and you tell a provider a message landed that did not.** This is the three-valued delivery state that mirrors the three-valued `LocationEligibility` the domain model already insists on. It also distinguishes *the recipient rejected it* from *the provider declined to send because of its own suppression list* — not the same fact, and only the first is evidence about the recipient.
2. **It does not retain message bodies by default.** §12 classifies notification-body content as *"derived; treat as permanently exfiltrated once sent"* — and the body carries a **non-guessable link into the authenticated surface**. A vendor retaining that for 30 days is a second copy of a credential-like artefact, in a system with its own breach clock, outside Superola's deletion path. **P02 asks what may appear in a notification body and never asks how long a vendor keeps it. It should.** One evaluated candidate's verified 30-day retention is disqualifying on that axis alone.
3. Roughly 13–17× cheaper at Growth.

**Adjudicating a genuine disagreement between two work packages.** One argued for Postmark at Pilot and Early on the grounds that its included suppression and event handling *is* the "deliverability apparatus" `D-05` calls required-not-optional, and that SES makes you build it. **Resolved in favour of SES**, because `D-05` requires *"hard-failure signals ingested so suppression is derivable"* — Superola must own the suppression list regardless of vendor. A vendor-side suppression list is in fact a **problem**, not a saving: it is global per address, so a marketing complaint suppresses transactional mail **inside the vendor, where Superola's purpose-class model has no reach** (`R-041`). Owning the list is what makes purpose-class separation possible at all.

**Mandatory from day one, and impossible to retrofit after reputation damage:** separate provider configuration sets and **separate sending subdomains per consent purpose class** (transactional/service versus marketing/reactivation), so reputation and suppression are physically partitioned.

**Cost:** $0.30 → $3.50 → $25.00 per month, plus a $15/month dedicated IP only if warranted at Growth. **The whole category is noise** — the Growth spread across all candidates is roughly $7.50 to $132. Do not optimise this line; optimise for failure-signal fidelity and body non-retention, which are correctness properties.

**Degradation confirmed:** `NotificationIntent` is created durably, survives channel failure, and is idempotent. When email is unavailable, providers see the request in-app on next login; delivery state shows failed, structurally distinct from provider non-response. **Speed degrades, correctness does not** — which is `D-05`'s stated free cost lever, and it now has a hard dependency recorded at §4.8.

**Reversal difficulty: LOW** in code — repoint SMTP, re-map ~10 event types — but **2–4 weeks of sender-reputation warm-up** is the real cost.

**Reconsideration trigger:** more than 2% of `NotificationIntent` records reach a terminal failed state with no classifiable event within 7 days — which measures whether the event stream is actually sufficient to derive suppression, rather than measuring the bill.

### 4.6 `D-06` — Media

**Selected: S3-compatible object storage with zero egress, a pre-generated fixed derivative set, and LINK OUT for audio and video.**

**On-the-fly transformation is architecturally excluded, not merely expensive.** `D-06` requires *"a small fixed derivative set"* **and** *"a processing state so no half-processed media is publicly visible."* A derivative generated on first request has no state to gate publication on. **P02 made this decision without noticing it made it** — and it is worth ~$177/month at Growth, so it should be recorded explicitly or a transformation service will be sold in later.

**Zero-egress storage is the decisive property.** `D-06` names served bytes per anonymous public view as *"the dominant variable line… the one cost that grows with acquisition success rather than with revenue-bearing requests."* **The right response to a dominant line is to eliminate it, not optimise it.** Verified: storage $0.015/GB-month, egress **$0.00**, with free-tier operation allowances that absorb Pilot and Early entirely.

At Growth: **~$3.53/month**, against $25–50 from an origin with metered egress and **$375/month** from a host charging $0.15/GB — which would exceed all compute combined. It is also the best **exit** story in the whole evaluation: the same API in and out, and **you are not charged to walk out.**

**Link out audio and video. Four arguments, and the fourth is new.**

1. **Cost.** Hosted video at Growth ≈ **$429/month** — 45–140× the entire image stack — and the delivery line scales with anonymous views.
2. **Operator cost.** Per-asset human moderation: images ≈ $0.03, hosted video ≈ $0.45. **15×**, because a person must watch or listen in real time. 600 videos per month cost more operator time than 8,000 images. Linked media drops to ~$0.15/asset because the host platform has already adjudicated legality and Superola is judging *suitability*, from title, thumbnail and first seconds.
3. **Rights — and P02 never named this.** When Superola hosts, Superola makes the copy and publishes it, and `MediaRightsDeclaration` is the only thing between Superola and a claim. **For mariachis, norteño groups, bands and DJs — the exact owner-reported categories — the performance may be the provider's but the composition almost never is.** Hosting imports music-licensing exposure that appears nowhere in the cost model. Embedding leaves the copy where its own rights adjudication already ran. **Link-out materially strengthens the rights position, in precisely the category where audio and video are central** (`R-043`).
4. **Dead links are the honest cost, and they are asymmetric.** Link rot is real and needs a sweep — but a freshness sweep **must exist regardless**, because `RequestIntake` decays on a governed window, so a dead-media check is marginal. And the failure modes differ in kind: a dead link degrades one profile; hosting's failure mode is a bill that grows with your own success.

**Images stay hosted** — verifiably ~$3/month, presentation consistency on the primary discovery surface is the product, and the fixed derivative set plus processing state require Superola to own that pipeline.

**Trigger to switch to hosting**, both conditions required: measured link-rot above 8% of external media references per quarter sustained across two quarters, **and** a measured relative gap greater than 15% in request conversion between performer profiles with live and dead media. Link rot without measured harm is not a reason to take on a variable line that scales with anonymous traffic.

**Three controls that are free because a re-encode already has to happen:**

- **Strip all metadata during the derivative re-encode.** Provider EXIF routinely carries precise GPS, and §12 classifies precise provider base location as provider-private, never emitted to a projection. **No P02 document names this path** (`R-048`).
- **Verify content type by magic bytes, not by declared type or extension**, and never serve the uploaded bytes back — the derivative set means you never need to.
- **`D-06`'s upload malware and abuse scanning requirement, answered explicitly** — adversarial review found it unaddressed. **The re-encode is the primary control, not a scanner.** Decoding an upload and re-emitting a normalised derivative destroys embedded payloads, polyglot files and malformed-header exploits by construction, and the uploaded bytes are never served. A signature scanner in the worker is a cheap, open-source **secondary** layer whose detection rate on targeted content is modest; it is worth running and is not worth relying on. **Managed scanning-service prices were `NOT VERIFIED`, so the moderation cost in `cost-model.md` §5.2 is human-only and is therefore a floor, not a total.** Abuse scanning of *content* — as distinct from malware — stays human, per `security-privacy-architecture.md` §10, which rejects automated abuse detection at zero measured volume.

**Unresolved and highest-priority before adoption:** whether objects served through a custom domain on the chosen provider's entry plan fall under its developer-platform exemption or its content-delivery restriction on serving *"a disproportionate percentage of pictures"*. **Superola's public pages are image-heavy by design**, so that clause is a live exposure, not a theoretical one.

**Also record:** `PublicationRequirement`'s "required media roles" must gate on the **database fact** that media exists, never on the vendor fact that it is currently servable — or a delivery-network outage silently unpublishes supply (`R-042`).

### 4.7 `D-07` — Place resolution and geocoding

**Selected: a governed internal `Place` list for all customer-side location input, and a permanent-storage geocoder for provider base addresses ONLY.**

> **P03.1 correction.** This section originally selected *"and NO rendered map in V1"* as part of the same decision. **That is a product/UX conclusion, not a technology selection, and it has been moved to P04** (`ADR-019` Level 3). The **invariant** — precise provider-private base location is never publicly exposed — is unchanged and absolute. **The vendor selection is separately on `HOLD` pending two legal readings.** Everything below is preserved as written; read the map paragraph as *why the cost model assumes no map*, not as a prohibition.

**Two candidates are disqualified on terms, not price.**

The largest mapping platform is disqualified **twice**. Its policies forbid pre-fetching, caching or storing Places content beyond stated exceptions — and the resolved coordinate is not among them, while Superola must persist a `GeoPoint` with precision and provenance to evaluate eligibility offline. The second reason is worse: its **one** permitted persistence exception is its own place identifier, and `domain-model.md` §1.4 with `integration-architecture.md` state that **place identity is internal and never a vendor's identifier**. **The vendor's only lawful persistence mechanism is exactly the thing the architecture prohibits.** A second candidate's public instance forbids autocomplete outright in its usage policy; a third carries a termination-deletion clause on the very artefact eligibility depends on.

**The governed internal list is not a cost optimisation — it is P02's own design, unrecognised.** `domain-model.md` §1.4 already specifies a governed place node with a stable internal identifier, a variable-depth parent chain, canonical and localized names, synonyms, and a representative point, and states that provider-typed place text is *"a resolution input, never a `Place`"*. An open gazetteer under a permissive commercial licence plus the Mexican national statistical authority's own municipio and localidad hierarchy (whose terms explicitly permit commercial exploitation with attribution) is a near one-to-one materialisation of that specification — including the **localized names and synonyms** a Spanish-production-locale launch needs.

**Cost consequence at Growth, with the arithmetic shown rather than asserted.** Two readings of the same month. The vendor-autocomplete reading assumes 200,000 customer autocomplete sessions at 4 debounced requests each, 70% completing, plus 12,000 geocodes. The governed-`Place` reading assumes **zero customer-side vendor calls** and 1,320 provider base-address resolutions.

| Growth, per month | Vendor-autocomplete reading | Governed `Place` reading |
|---|---|---|
| **Disqualified major platform** — completed sessions bill Place Details at $5.00/1,000 (10,000 free) then $4.00/1,000; **abandoned sessions dissolve and rebill every keystroke** at $2.83/1,000 then $2.27/1,000; geocoding $5.00/1,000 | 140,000 completions → 90,000 × 5.00/1,000 + 40,000 × 4.00/1,000 = **$610.00**; 60,000 abandonments × 4 = 240,000 requests → 90,000 × 2.83/1,000 + 140,000 × 2.27/1,000 = **$572.50**; geocoding 2,000 × 5.00/1,000 = **$10.00**. **Total $1,192.50** | *(disqualified on terms — §4.7)* |
| **Recommended geocoder** — Search Box $3.00/1,000 sessions (500 free, volume rate above 100,000); Permanent Geocoding $5.00/1,000, no free allowance | sessions 199,500 × 3.00/1,000 tapering to 2.75/1,000 = **$573.50**; permanent geocodes 12,000 × 5.00/1,000 = **$60.00**. **Total $633.50** | sessions (1,320 − 500) × 3.00/1,000 = **$2.46**; permanent geocodes 1,320 × 5.00/1,000 = **$6.60**. **Total $9.06** |

**$633.50 → $9.06 is a ~70× reduction against the same vendor; $1,192.50 → $9.06 is ~132× against the disqualified platform.** Both come from a **design decision, not a procurement decision.**

And the reversal that makes it work: the geocoder's **permanent** tier — no free allowance, the one everybody avoids — becomes the cheapest option in the field once the `Place` list absorbs customer-side volume. **The volume that made "permanent" expensive was never the volume that needed storing.**

> **Note on the abandonment line, because it is the counter-intuitive part and it lands on `R-022`.** Under the disqualified platform's session model, a *completed* address entry costs $610 ÷ 140,000 ≈ **$0.0044** and an *abandoned* one costs $572.50 ÷ 60,000 ≈ **$0.0095** — **abandonment is 2.2× more expensive than completion**, because an abandoned session dissolves and every keystroke rebills individually. 30% of users would generate 48% of the autocomplete bill. `R-022` already treats verification abandonment as a measured cost; this would bill it **twice**, once as lost conversion and once as a higher unit price. Customers do not need their location persisted; providers do, and there are two orders of magnitude fewer of them.

**A second, independent confirmation of the same trap:** if customer location in *search* resolved through a vendor, Growth would be 600,000 searches × permanent-tier pricing ≈ **$3,000/month** — several times the entire infrastructure bill. **State the rule rather than leave it implied: geocode provider base addresses and unresolved free text only; never per search** (`R-045`). This is `D-13`'s unit-economics inversion appearing in a second capability.

**There is also a coherence argument, not only a cost one.** `Market` is a governed (Category × Place-at-a-stated-granularity) pair. If Markets are governed and enumerable, the customer-side location vocabulary is **already constrained to a governed list by construction**. Free-text customer geocoding would let a customer express a location no Market covers, producing `undetermined` eligibility for structural rather than data reasons. **It is not merely expensive — it is incoherent with the `Market` concept.**

**On maps — the invariant, and the conclusion P03.1 separated from it.** §12 classifies precise provider base location as provider-private, readable by the eligibility computation and *"never emitted to a projection, a search result, a notification, or analytics."* **There is therefore no public surface on which a pin at a provider's exact location could lawfully be drawn.** That is the invariant and it holds.

**What does not follow is "no map."** A city centroid, a governed coarse `Place`, a declared service area or market coverage emits no provider-private data. **P03 had no UX evidence to reject those surfaces**, and this document originally wrote the invariant and the product conclusion as one sentence. **The narrower true statement:** no booking, availability, routing, directions or service-area drawing tool exists in V1, and no identified V1 requirement needs a map — so the geographic interface currently reduces to *enter an address* and *display a coarse location label*. **The cost model assumes that**, and it is that assumption which removes the largest line item on every vendor's price sheet and dissolves the map-coupled storage restrictions that disqualified the largest platform. **If P04 approves a geographic surface, the line is re-priced against the specific surface — see `ADR-019` Level 3.**

**Precision is a correctness requirement, and it decides the vendor.** The selected geocoder returns an accuracy classification plus a match code, which maps almost one-to-one onto the three-valued `LocationEligibility`: rooftop/parcel/point may drive radius eligibility; interpolated/approximate yield `undetermined`; intersection routes to operator review. **That is the structural fix for the reported legacy defect, implemented directly.** A vendor returning no precision signal is a correctness problem, not a convenience problem.

**Degradation is unusually good, and P02 did not claim it.** Eligibility computes entirely offline against stored coordinates. Geocoder unavailability affects exactly one flow — a provider entering a new base address — and the domain model already has the right behaviour: hold at `undetermined`, resolve by a later sweep. **The three-valued eligibility justified on data-quality grounds also makes geocoder downtime a non-event.**

**Reversal difficulty: LOW.** At Growth, cumulative stored provider base locations are roughly 10,000–15,000 (10,000 published, plus unpublished drafts and re-geocodes). Re-geocoding all of them through the recommended source at its permanent-storage rate costs 15,000 × $5.00/1,000 = **$75**, and through the runner-up **one month of its $50 plan** — in both cases **a few days of elapsed time** — because `GeoPoint.provenance` is mandatory, so you know exactly which coordinates came from whom. **Geocoding lock-in is a high-volume fear, and this design removes the volume. P02 made the exit cheap by requiring provenance; it just never priced it.**

**Unresolved, and it could flip the recommendation:** whether the selected vendor's general termination clause — requiring destruction of data accessed through the service — reaches permanently-licensed geocodes. If it does, the runner-up's explicit *"you can store the results as long as you like, including after you stop being a customer"* wins. **This is a legal reading, and it must be settled before the source is adopted.** A second legal question: whether rendering a vendor-derived coordinate on a public page constitutes distribution under a licence permitting storage *"for your own business use"* but forbidding distribution or sublicensing.

### 4.8 `D-08` — Authentication

**Selected: BUILD, on the framework's established security library, with credentials in `D-01` and verification mail through `D-05`.**

**Price is not a differentiator, so it must not be the decider.** At 300 / 3,000 / 25,000 monthly active users, two leading vendors are **$0 / $0 / $0** and a third is **$25 flat**. Only one charges meaningfully at Growth. **The ordinary case for buying — cost relief at scale — is absent.** You would be buying convenience and paying for it in exit.

**Four architecture-fit failures, each independent:**

1. **The cliff you have already scheduled.** One vendor's free tier ends at exactly **25,000 monthly active users — the Growth target.** Building identity on a free tier whose ceiling *is* your plan is choosing a cliff you have scheduled yourself.
2. **The commercial pressure runs opposite to the architecture.** `ADR-011` places authorization in the domain: *may this Account, acting for this Business, perform this action on this resource, in this resource state?* Vendors cannot see resource state, and `BusinessMembership` is a domain relation with grant and revocation times needed to answer *"who could read this at time T?"*. **One vendor prices the forbidden model — organizations, roles, permissions — at $100/month.** A future engineer under deadline pressure will see a solved problem rather than a boundary violation.
3. **The `VerificationFact` mismatch.** §2 requires **typed, expiring, revocable** verification facts, because *"verification is never a boolean on a Business"*. Every vendor ships `email_verified` as a boolean on the user. Superola would shadow it immediately — **reimplementing the vendor's flagship feature on day one, with the vendor's copy being the one that cannot expire or be revoked.**
4. **Revocation.** §9 requires operators to suspend an actor. With a server-side session in Superola's own store, revocation is a delete and is immediate. With a vendor-issued token, revocation is bounded by token lifetime unless you call the vendor per request — discarding the reason the token was chosen. **A safety suspension that takes effect "within 15 minutes" is not a suspension.**

**The asymmetry that P03 said decides it — and the P03.1 correction that removes it.** BUILD→BUY is a documented, supported path: vendors publish bulk import accepting hashed passwords. **P03 wrote that BUY→BUILD "may be impossible" because password-hash exportability could not be confirmed for any vendor, and inferred a general documentary asymmetry. P03.1 verified the fact and falsified the inference for two of four vendors:** Supabase documents the hashes as bcrypt in a customer-owned table; Clerk documents a self-service export that *"includes their hashed passwords"*. **Auth0 is gated behind a support case; Cognito has no documented mechanism, which is recorded as *no path found* rather than *proven impossible*.**

**What survives is narrower and still real:** where no export exists, migrating 25,000 accounts means forcing every user to reset — against `R-022`, which already treats verification abandonment as a measured cost, that would be the largest deliberate abandonment event in the product's life. **That risk is now vendor-specific rather than categorical.** `ADR-017` is on **`HOLD`**; the recommendation stands as a *working* recommendation on the axes that survive — revocation semantics, the `VerificationFact` model, and `ADR-004` dual-role account shape. Evidence: `docs/07-research/authentication-vendor-verification.md`, accessed 2026-08-12.

**Honest cost of BUILD, stated as an assumption:** password hashing, session management, email verification, password reset, rate limiting and lockout, on a framework's battle-tested primitives — **8–12 engineering days initially, ~2 days/year maintenance.** That is real money and real risk. The argument is not that BUILD is free; **it is that BUILD is the reversible option.**

**Hard constraint from the `DAVID_DIRECTIVE`:** no mechanism may force role-typed accounts or partition users into disjoint populations. `ADR-004` is now `ACCEPTED`; this is an elimination criterion, not a preference.

**Hard constraint from `R-040`, and neither `D-05` nor `D-08` noticed it:** **Superola must retain at least one non-email credential path.** `D-05`'s free cost lever is *make in-app state authoritative so notification failure degrades speed, not correctness* — which **requires reaching in-app state without email**. Magic-link or email-OTP-only authentication means an email outage locks users out of the very state email failure was supposed to survive. This qualifies `R-022`'s friction-minimisation instinct: the lowest-friction mechanism concentrates all access on the single channel the architecture already treats as unreliable.

**Reconsideration trigger:** a signed requirement for enterprise federation from a named paying counterparty — the one thing genuinely worth buying and genuinely painful to build; **or** account-recovery cases exceeding 2% of monthly active users, measurable from the operator case records `D-11` already requires.

### 4.9 `D-09` — Web rendering and public distribution

**Working baseline, NOT a decision: one server-rendered application in the framework's own view layer, htmx for partial updates, React islands where a genuinely stateful surface demands it.**

> **P03.1 correction.** This was originally written as a selection and packaged into `ADR-013` alongside the platform. **Rendering is now `ADR-020` and is `DEFERRED PENDING P04 UX VALIDATION.`** The reasoning below is preserved because it is a good reason to **start** here — **it is not a reason to approve it before P04 has produced any interaction requirement.** P04 may legitimately conclude that a richer client is justified for named surfaces, and this document does not prejudge that. **Two things are not provisional under any option: authorization is decided in the domain, never in a route guard, a template or a client (`ADR-011`); and public discovery pages stay server-readable.**

`system-architecture.md` §3 already established there is **no realtime, no server-initiated push, and state is fetched on navigation or explicit refresh.** That is precisely the workload a server-rendered application is optimal for, and **nothing in the repository yet describes a rich-client requirement — because nothing in the repository yet describes the interactions at all.**

Every `D-09` requirement becomes cheap: canonical URLs, retained redirect history, locale-distinct URLs, language links and sitemap are **server routing plus a governed table**, and machine-access policy is **one filter ahead of every controller** — literally what `ADR-012` asks for. It also eliminates the version-skew bug class entirely and needs none of the multi-instance cache coordination a full-stack JS framework documents for self-hosting.

**Why the runner-up lost.** A leading full-stack framework's own vendor documentation states its backend capabilities *"are not a full backend replacement"* and serve as an API layer. Taking that at face value means the domain lives elsewhere — which is the two-deployable shape `ADR-001` calls the most expensive increment available. Add a verified record of removals, renames and signature changes in its latest major, plus a shared cache handler requirement that P20 turns from optional into mandatory, and it is the higher-maintenance option for one person.

**`ADR-001`'s asymmetry argument applies to the frontend too, and it is the reason to start here:** adding a JSON API plus a JS client later is **additive** if `ADR-011` holds — the use-case layer already exists. Removing one is not. Start on the recoverable side.

**A contradiction that must be corrected (`R-035`).** `system-architecture.md` §1 and `domain-map.md` describe *"robots / sitemap / per-crawler-class policy — governed data, ONE enforcement point, ONE owner."* But RFC 9309 states plainly that robots rules *"are not a form of access authorization"* and are *"not a substitute for valid content security measures."* **Restate as one governed policy, two artifacts:** a request-path filter that decides allow or deny before rendering (enforcement, and even then best-effort against a non-cooperating client), and a published `robots.txt` **derived** from the same record, never authored separately, with a **deploy-time check that fails if the two renderings diverge.**

**And a second enforcement point to refuse (`R-046`):** entry-tier managed bot mitigation ships a vendor-owned allowlist of "verified good bots" — search and AI crawlers — permitted by default. **Enabling it approves `Q-015` by accident, before `Q-027` has been settled**, violating the stated ordering. Do not enable it while the gate is closed. Volumetric denial-of-service protection is a different thing and makes no crawler-class decisions.

**A sharpening for `ADR-012` worth adopting: make the publication allowlist a TYPE, not a predicate.** `ADR-012`'s validation asks that no public surface can contain an unpermitted field *"including when the query reads source records directly"* — but a predicate inside a SQL string is invisible to every static-analysis tool in every candidate stack. If the projection is a **closed generated record type whose fields are the declared publishable set**, the compiler enforces the allowlist because there is nowhere to put an unpermitted value. Add a golden-file test asserting the serialized field set equals the declared field-set version. **This converts an unverifiable review obligation into a build failure**, and it is available in every statically typed candidate.

**Reconsideration triggers:** a named V1 surface requires client state surviving navigation, evidenced by a specific journey **and** a failed progressive-enhancement attempt; **or** measured p75 interaction latency on request submission exceeds the approved budget, attributable to full-page round trips; **or** the count of client-side islands exceeds five — at which point the single-page argument has arrived on merit rather than preference. *Instrumentation:* per-flow interaction latency, and a counted island inventory in the build.

### 4.10 `D-10` — Deployment, operations, and boundary enforcement

**Selected: one managed-PaaS deployable plus one worker process from the same artifact, managed PostgreSQL with point-in-time recovery included, a content-delivery layer in front of public reads, and object storage for media. Single US region.**

**Why managed over a cheap VPS — with the arithmetic, not the opinion:**

| | Cash saved by self-managing | Extra operator hours/month | **Break-even hourly rate** |
|---|---|---|---|
| Pilot | ~$50 | 6.5 | **~$7.72/h** |
| Early Marketplace | ~$36 | 9.0 | **~$4.03/h** |
| Growth | ~$133 | 15.0 | **~$8.87/h** |

**Self-managing is cheaper only if the operator's time is worth under about $8/hour.** At any rate above roughly $12/hour, at every modelled volume, it loses — by $437 to $992 per month. **The bill is not the cost.** And at Growth it requires high-availability PostgreSQL with automatic failover, which under *small-team operability* is a hiring decision wearing a technology decision's clothes.

**Hard host requirements that eliminate candidates**, drawn from P02 rather than invented: PostGIS or equivalent; automatic backups **with point-in-time recovery** plus bulk export; one artifact with a **separate worker process** and **per-process resource limits** (a host that cannot express this from a single artifact fails a stated `ADR-001` requirement — a criterion `D-10` does not currently list); zero-downtime deploy with health checks and fast rollback; private app-to-database networking; and operability by one part-time person.

Eliminations that matter: one popular platform's PostgreSQL is explicitly **unmanaged** with bolt-on backups and **no built-in point-in-time recovery** — disqualifying against a hard requirement. Another offers PostGIS **only if enabled at provisioning**, so choosing "no" and needing it later means recreating the database — a trap worth naming. A third contradicts `ADR-011` architecturally: its value proposition is a generated API over the database, so adopting it and then refusing to use it is paying for lock-in you disabled.

**Region: select the host now, select the region at the geography gate.** `OR-003` and `A-002` are OPEN. `internationalization-architecture.md` §5 is binding — *"market scope is not topology"* — so this is one region regardless; naming it now would settle a product question by infrastructure side effect. Mitigation: at Pilot volumes a region move is a maintenance window, not a re-platform.

**Latency, honestly.** Measured Mexico-to-US figures could not be verified from primary sources, so what follows is arithmetic from distance and the speed of light in fibre, clearly labelled as a derivation: Mexico City to a US-South region has a theoretical floor near 14 ms and a realistic round trip of roughly **30–40 ms**; to US-East, roughly **60–75 ms**. Guadalajara and Monterrey are closer to Texas than Mexico City is, so **a US-South region is the latency-optimal single choice if Mexico is in scope.**

**What a delivery network fixes, stated so the claim is falsifiable.** It fixes cacheable anonymous public reads — the acquisition surface — and it terminates the TLS handshake at a nearby point of presence, saving roughly two round trips on **every** new connection even on a cache miss. It does **not** fix authenticated reads, location-predicated searches (uncacheable by construction), or any write. So: it closes the gap for 90%+ of request volume and 100% of the acquisition surface, and not at all for the marketplace loop. **At 60–75 ms that residual is acceptable for an asynchronous request product where the fastest human response is measured in hours. It would not be acceptable for a realtime product — and P02 has already established none is required.** The two conclusions support each other.

**Deployment safety — the half of `D-10` that P02 does not say (`R-047`).** *"A fast pipeline so rollback is cheap"* is true for code and **false for schema.** On a single deployable with rolling deploys, old and new code run simultaneously against one schema. Three rules, all free, all absent from P02:

1. **Every migration is expand/contract.** Add nullable, write both, backfill in bounded batches, read new, drop old **in a later release**. Never rename, drop, narrow or add a non-defaulted `NOT NULL` in one step. Note that concurrent index creation cannot run inside a transaction and must be declared non-transactional.
2. **No destructive DDL in the same release as the code change that stops using the column — minimum one release gap.** The invariant a reviewer can check: *at every moment the deployed schema supports both the current and the immediately previous application version.* This is the only thing standing between *"we have rollback"* and *"we have rollback unless we migrated today"* — **and the day you migrate is the most likely day you need it.**
3. **Migrations run as a pre-deploy step under a migration role**, never on application startup, because concurrent replicas race the lock. Failure aborts the deploy; the old version keeps serving.

Plus: **forward-only migrations, no down scripts** — a down script is a rollback fiction that is never tested. And a pipeline check that runs new migrations against a restored copy of the previous schema, then runs the **previous** version's tests, which proves backward compatibility mechanically instead of asserting it.

**Boundary enforcement — "the one place P03 should be willing to spend," and it turns out to be cheap.** Three layers, all failing the build:

| Layer | What it catches | Verified |
|---|---|---|
| **Module verification with explicit `allowedDependencies`** | Module cycles; any reference into another module's internal package; **any dependency not declared** — which is a literal transcription of `domain-map.md`'s 13-row forbidden-dependency table | `ApplicationModules.verify()` throws, failing the test task and therefore the build |
| **Architecture-rule tests** | What module verification cannot express: no module *reads* Analytics; no authorization decision in a controller or template; no module imports the intent mapper; no transactional method touching two modules' repositories | Ordinary test failure |
| **A cross-schema foreign-key test** | **The half of `ADR-001` no import-graph tool can see** | Queries the catalog against a real database; fails the build |

**That third layer is the genuine gap (`R-034`).** `ADR-001` says it itself — *"the shared record store is the real trap, not the shared process… cross-module referential constraints and ad-hoc joins are what make later extraction expensive."* **No import-graph tool in any candidate stack can see a foreign key in a migration or a table name inside a SQL string.** Architecture-rule libraries are bytecode-only; build-tool configurations see types, not strings. Every stack has this hole equally, and roughly thirty lines closes it.

**This also answers `ADR-001`'s own open question.** The ADR worried that ten boundaries is ~10× the enforcement surface of three or four, and asked P03 to revisit the **boundary set** if enforcement cost scales with count. **It does not: the marginal cost of a boundary is one annotation.** The answer depends on the *mechanism*, not the count — which reverses the ADR's implied direction. **The boundary set does not need revisiting on enforcement-cost grounds.**

**One control everyone forgets, and it is free.** Rule-engine mechanisms fail at test time and can be suppressed **by editing the rule file**. So the boundary rule file itself must be protected: put those paths under code ownership and require a commit touching them to reference an ADR. For one person that is self-approval — **the value is not the gate, it is that weakening `ADR-001` becomes visible in the history instead of invisible in a diff.**

**Verified anchors** (accessed 2026-08-11): the recommended framework line is at **4.1.0** (10 Jun 2026) on framework 7.0.8, Java 17 minimum through Java 25/26; the module-verification library is at **2.1 GA** (11 Jun 2026). **An honest counter-argument, recorded rather than discovered later (`R-036`):** that framework line carries roughly a 12-month open-source support window per minor with a ~6-month release cadence — about one upgrade per year, forever, for one part-time developer. The runner-up platform's cadence is gentler. This does not outweigh the fit arguments, but it must be **budgeted, not discovered**.

**A verified difference on the decisive criterion.** The runner-up platform's project references are **transitive by default** — if A references B and B references C, A compiles against C. It is closable (`DisableTransitiveProjectReferences`, or marking references private), but it is **opt-out**, where the recommended build tool's implementation configuration is closed by default. Given P02's own warning that *"deadline pressure attacks boundaries first"*, a default that leaks is a real difference.

**Reconsideration triggers:** measured p95 on the request-submit path or the eligibility query above budget on the **largest shared-CPU instance** after tuning, with profiling attributing it to CPU contention rather than the query — *then move to dedicated CPU on the same platform, not to another platform*; **or** database working set exceeding memory at the 16 GiB tier, measured as sustained cache-hit ratio below 99% for seven days; **or** platform incidents exceeding the approved availability budget across two consecutive months.

### 4.11 `D-11` — Observability, audit, and measurement

**Selected: free-tier error tracking + free-tier structured log storage + external uptime + a dead-man's switch on background jobs. $0.00/month at Pilot.**

**The trap `D-11` names is real and pricable.** A per-host-plus-per-indexed-event platform floors around **$92/month at Pilot before a single log line** and **$940+/month at Growth**, because per-million-event log indexing is driven by logging verbosity — a variable a developer changes casually at 2am. Worse, `ADR-001`'s separate worker process is a **second billable host**, so the architecture's own blast-radius mitigation doubles the monitoring bill. Verified free tiers cover Pilot **and** Early Marketplace entirely.

**Units that punish success, ranked:** per-million-indexed-events; per-host; per-seat (one verified vendor charges $99 per additional user, and free tiers cap at one to three users — going from one person to two is a step change); and per-GB-**retained**-per-month, which compounds.

| Need | Tool class | Pilot cost |
|---|---|---|
| Uncaught errors, release health | Free-tier error tracker | **$0** |
| Structured, queryable logs | Free-tier log store (50 GB/month verified) | **$0** |
| External uptime on public and request paths | Free-tier uptime monitor | **$0** |
| **Background-job and sweep liveness** | Free-tier dead-man's switch | **$0** |
| Search/request latency, dead-letter depth, delivery failures | **Built into the domain**, exposed as counters | **$0** |

**The dead-man's switch is the most under-valued item here.** It is the only mechanism that catches *"the freshness sweep silently stopped running."* **An error tracker structurally cannot detect the absence of an event.**

**The mechanical reason `D-11`'s "build the funnel into the domain" is right, stated so it is falsifiable:** `search-architecture-requirements.md` §11 makes the `D-03` promotion trigger conditional on **candidate-set size and query mix** recorded alongside latency. **No monitoring product on the market knows what a candidate set is.** That instrumentation is domain code and must be written whatever is bought. Buying a platform therefore does not *replace* the work — it duplicates the latency half at recurring cost while leaving the trigger unmeasurable.

**OpenTelemetry verdict: adopt the instrumentation; reject the collector and trace export at Pilot.** Emitting vendor-neutral metrics and logs makes the *backend* swappable, which is the exit obligation `AGENTS.md` imposes on every material decision, and it costs a library. A collector is a second always-on component with no measured need. Traces are the highest-volume, highest-cost signal, and P02 already rejected distributed tracing because there is one process. **The one place a trace would genuinely help — attributing latency inside a slow search across the eligibility, geography and ranking steps — is a profiling question, answered by a one-off session, not a recurring per-GB pipeline.**

**Audit placement is already decided by P02, and the derivation should be recorded rather than re-opened.** `system-architecture.md` §4's atomic units for *publish profile*, *claim grant* and *moderation decision* each include **"plus the audit record"** inside the transaction. A separate audit store would either lose that atomicity — producing a committed decision with no audit row, exactly the failure audit exists to prevent — or require two-phase commit. **Audit lives in the same database, in a separate schema, owned by a separate role.**

**What actually enforces immutability, and what does not:**

| Mechanism | Genuinely prevents | Does not prevent |
|---|---|---|
| **Revoked `UPDATE`/`DELETE`/`TRUNCATE` grants** on the audit schema, application granted `INSERT`/`SELECT` only | The application — including ORM misuse, cascades, bugs, and injection running as the app role — from modifying or removing an audit row. **Highest value per effort in this document. Cost: one line.** | The table owner, a superuser, the migration role, or the host's console. **`TRUNCATE` is a separate privilege and must be withheld explicitly — the most common omission** |
| **A `BEFORE UPDATE OR DELETE` trigger raising an exception** | Modification **even by the owner and by a superuser**, while enabled. The only in-database control that binds a superuser | Disabling or dropping the trigger, dropping the table, or restoring a doctored dump. **A truncate fires a truncate trigger, not a delete trigger — a separate one is required or the control has a hole** |
| **Row-level security** | Scoping *which rows* a role may read — useful for operator reads | **Nothing about immutability. Using it as an append-only control is a category error**: superusers and bypass-privileged roles always bypass, and owners bypass unless forced |

**"Not deletable by the domain-deletion path" is achievable, cheaply, under a precise definition:** (a) **no foreign key from audit to any domain table** — audit holds a reference, never a constraint, and a cascading foreign key is exactly the failure being guarded against and exactly what an ORM will generate by default; and (b) **no destructive grant to the application role.** Both free. **Both asserted in the pipeline, not in a document.**

**The retention period is a question, not a number.** Recorded as a `retention_class` column so the period is **data, not code**, with a scheduled expiry sweep on the `D-04` scheduler. **The sweep runs under a role that is not the application role** — granting the application delete so it can expire old rows silently undoes the control. Owner: David plus counsel, same owner as `Q-024`, due before launch, not before P03.

**Costing the `DAVID_DIRECTIVE` on operator access.** Operator reads of customer-private content go through a named use case taking `operationalPurpose` and `caseRef` as **required parameters, enforced by the compiler** — you cannot forget an argument the compiler demands, and you can absolutely forget a runtime check. One audit row per read. **~1–2 developer-days on top of the operator surface that must exist anyway.**

**The honest limit, recorded rather than glossed:** the managed-database console and the host's shell are unrestricted, unaudited access. The directive is not fully implementable while either is a routine path. Proportional answer: production credentials live only in the deploy secret store; console access is a break-glass path with a self-recorded reason; and the revoked grants plus the trigger mean even break-glass cannot silently rewrite history. **Claiming more than that would be theatre**, and §10 already rejects the privileged-access-management class needed to claim more.

**Business metrics: SQL against the transactional store**, with a nightly rollup added **when the first month-over-month comparison is made** — not for performance, for **comparability**, since a live-computed funnel re-derives history and a definition change silently rewrites last month's number, which is the `R-004`/`R-018` failure mode. Stamp the definition version on the rollup row. Read replica only after rollups and off-peak scheduling are exhausted. **Analytics is 77–87% of database size at every scenario and is the only sizing lever that matters — and nobody owns its retention period (`Q-034`).** Monthly range partitioning must be decided **before launch**; retrofitting a partition scheme onto ~96 million rows is a migration, not a configuration change.

If a query front-end is used for the operator funnel, it must connect through a **read-only role whose grants exclude every customer-private column**. Otherwise it is the unaudited console P02 forbade, with a nicer interface and no purpose recorded. **If curating that grant set is more work than writing three views, write the views.**

### 4.12 `D-12` — Label and localization storage

**Selected: in `D-01`, Catalog's schema. No second store and no reason for one.**

Localized labels and synonyms as rows over stable language-neutral identifiers; a versioned `label_set` as the reviewable unit; the same pattern for every governed vocabulary. Normalised surface forms are **computed at write time**, not in an index expression (§4.3), which makes the normalisation rule a governed artefact. Locale resolves once per request; the active label set is cached in-process and invalidated on publication.

**Structural cost: ~7,000 rows, ~1 MB.** A rounding error — and simultaneously the cheapest high-leverage capability in the search stack, because it is what trigger `T3` protects and what defers Stage 2.

**Operational cost is the real one, and it must not be softened.** `internationalization-architecture.md` §4 already prices it: a second locale's content is MEDIUM-HIGH and **permanent**; bilingual operator staffing is HIGH and **recurring** — *"a multiplier on every queue, not a new queue."* **Adding a locale is a staffing decision wearing a schema's clothes.** Do not report "`D-12`: cheap" without that sentence attached.

**A split worth adopting:** governed vocabulary labels live in `D-01` as versioned, operator-editable, audited rows; **interface chrome** — buttons, errors, empty states — lives in code-resident message bundles shipped with the build. Conflating them means either needing a deploy to fix a category name, or putting button labels in a database with no review.

### 4.13 `D-13` — AI intent mapping

**Selected: DO NOT SPEND.** Full record in `docs/03-technology/ai-evaluation.md`.

The gate is **closed and unanswerable in P03** — the corpus does not exist, the instrumentation that would produce it ships *with* V1, it needs months of operating time after that, and the corpus is **itself privacy-gated by a decision assigned to nobody** (`Q-033`).

Three findings carry the recommendation. **The mapper's ceiling is the form's expressiveness**, and P02 made that vocabulary deliberately tiny — so the maximum value is *typing a sentence instead of using a picker*. **A closed output vocabulary is a classification problem, not a generation problem.** And the one thing a model genuinely does better — compositional multi-constraint parsing — is what the guided structured entry flow removes by construction.

The two cheaper alternatives are **already required by P02** and cost ~13–25 incremental developer-days combined. They are not alternatives to weigh against a model; **they are prerequisites for measuring whether a model would help.**

**The rate-limiting arithmetic, because it is the launch-blocker argument made concrete:** one unremarkable script at 10 requests/second for 30 days against an anonymously reachable endpoint is 25,920,000 calls — **$3,875 to $340,848 depending on model, with zero marketplace effect.**

### 4.14 `D-14` and `D-15` — gated, not evaluated

**`D-14` (legacy pipeline environment): GATED on `G-09` and its legal questions.** Not evaluated. What P03 records is only what the store must provide if a cohort is ever approved: an indexed batch identifier on every row a batch can touch; **per-field provenance**, so a provider-corrected field survives reversal while an imported one does not; a uniqueness constraint on the source record for idempotency; one transaction per source record; and the suppression check **inside** the creating transaction rather than as a prior read, so no race window exists. **None of this favours any store — it is schema discipline that P05 will get wrong if per-field provenance is not designed into the first migration**, because retrofitting field-level origin onto a populated table is unrecoverable.

**`D-15` (payment): EXCLUDED.** `G-02` is unresolved and `A-004` is OPEN. No payment technology was evaluated and no cost line includes it. Named only as an explicitly excluded future sensitivity: a processor would add per-transaction variable cost, card-data compliance scope, payout identity verification, and reconciliation operator load. **No figure in this document covers any of it.**

## 5. Cost model summary

Full detail in `docs/03-technology/cost-model.md`, which is the **single arithmetic source of truth**; `cost-alternatives.md` explains why each line is what it is. **The headline is the cash bill. The human rows below it are a separately labelled sensitivity and must not be added to it.**

| | Pilot | Early Marketplace | Growth |
|---|---|---|---|
| Fixed platform | USD $75.30 | USD $187.05 | USD $505.20 |
| Usage-variable | USD $0.68 | USD $5.94 | USD $55.59 |
| **CASH BILL** | **USD $75.98** | **USD $192.99** | **USD $560.79** |
| *Separately — illustrative human sensitivity, not added above* | | | |
| Human, infra ops @ an assumed USD $75/h | USD $244 | USD $375 | USD $675 |
| Human, marketplace operator @ an assumed USD $40/h | USD $334 | USD $2,217 | USD $13,623 |
| *Illustrative human total* | *~USD $578* | *~USD $2,592* | *~USD $14,298* |

**Deliberately excluded:** payment and booking (`G-02`), the legacy import environment (`G-09`), the AI experiment (recommended against), and second-locale content operations (a staffing decision, not an infrastructure line).

## 6. Staged architecture

**Pilot.** One deployable + one worker from the same artifact. Managed PostgreSQL, smallest tier, with point-in-time recovery. Object storage for media. Delivery network on its free plan with cache rules on public reads. Transactional email. Free-tier observability. Job table in the same database. One small non-production environment for migration rehearsal and vendor sandboxes — **never seeded with production personal data.**

**→ Early Marketplace**, on any one measured trigger: sustained origin CPU above 60% for seven days; p95 on request-submit or the eligibility query above the approved budget after tuning; operator queue backlog age above the approved bound. Changes: a second application instance **for availability during deploy, not for load**; database one tier up; error tracking to a paid tier if error volume crosses the free allowance.

**→ Growth**, on any one measured trigger: database working set exceeding memory (sustained cache-hit ratio below 99% for seven days); restore-rehearsal recovery time exceeding the approved objective; an availability requirement beyond one database node. Changes: larger instances, a database standby, **monthly analytics partitioning with an approved retention and rollup window**. A read replica **only if** the cache hit ratio on public reads is already above the approved floor and reads still saturate.

**The Growth system is deliberately not designed.** Everything above it is unknowable without traffic evidence, and designing it now is the `R-031` failure. **The one thing worth deciding early is analytics partitioning and retention**, because that is the only item on this list that is a migration rather than a slider.

## 7. What we deliberately avoid, and the trigger for each

| Avoided | Why | Measurable trigger |
|---|---|---|
| **Kubernetes** | One deployable plus one worker. Adds a control plane, a node pool, an ingress controller, and a permanent specialist dependency — a hiring decision, not a technology decision | ≥3 independently deployed units with genuinely divergent scaling profiles **and** ≥2 teams blocked on one release train. Both |
| **A message broker, event bus, or stream** | Every deferred action has one known consumer and one known effect. A broker buys fan-out to *unknown* consumers, a problem V1 does not have, at the cost of a second failure domain | `system-architecture.md` §5's own trigger, unchanged: a second *independent* consumer with a real named owner |
| **Redis or any cache component** | The queue case is `D-04` at $0; the cache case is the delivery network at $0 plus in-process invalidation on the database's own notification channel. It would be the first new always-on component and the first non-transactional store | A requirement for shared mutable state across replicas that is **not** a cache — specifically per-endpoint rate limiting where measured abuse shows per-replica counters failed |
| **A dedicated search engine** | Every V1 capability reduces to identifier filters and geographic predicates; the working set fits in memory at Growth | `T5`–`T8` in §4.3, each with its named instrumentation |
| **A vector store or embeddings** | §12 excludes semantic retrieval; §6 states vector recall feeds on behavioural signal that does not exist at launch — zero clicks, zero conversions, zero reviews. **It would be tuned on nothing and would obscure the coverage problem it would be sold as solving.** Declining now forecloses nothing, since the capability exists inside PostgreSQL | `D-13`'s gate, which is closed |
| **Multi-region** | *"Market scope is not topology."* No residency requirement, no latency requirement, no traffic evidence | A legal residency obligation in an approved market; **or** measured p95 from an approved market above budget *after* public reads are cached. Instrumentation: latency by client geography, cache hit ratio |
| **General-purpose infrastructure-as-code** | 5–8 resources, one environment that matters, one person. **Zero of the five threshold conditions hold.** The state file is itself a new outage-class dependency | **Any two** of: two environments that must be provably identical after a drift incident; resources spanning ≥2 providers in a dependency order; >1 person modifying infrastructure; >20–30 managed resources; a tested whole-environment rebuild requirement |
| **An observability platform for product questions, distributed tracing, a warehouse, session replay** | `D-11`'s named trap. There are no services to trace. **Session replay is additionally structurally incompatible with §12** — it would capture request free text | Tracing: the first additional deployable unit. Warehouse: an analytics query degrading p95 **after** rollups and a read replica have both been tried and recorded insufficient |
| **A secrets-management product** | Platform-native environment variables plus the forge's encrypted pipeline secrets, at $0. A dedicated store adds a third party to the boot path and a single-operator lockout risk with no second human to recover | Short-lived dynamic database credentials become a requirement, **or** >~5 people need differentiated production-secret access |
| **A managed bot-mitigation product** | Its verified-bot allowlist is a **second, vendor-owned crawler policy** that would approve `Q-015` by accident, before `Q-027` is settled | Measured abusive automation **and** both gates settled **and** the product configured with an emptied allowlist deferring to the application's policy |
| **A compliance programme** | Pre-launch: no users, no revenue, no enterprise buyer. Build the structural **capability**, not the programme | A first real user cohort, a partner requirement, or counsel's answer |
| **Treating GDPR as in scope** | No working-scenario market requires it; adopting it "to be safe" imports unjustifiable cost | **An EU jurisdiction enters the scenario set** |
| **Write-once audit storage** | §10 rejects it, and it creates an **undeletable retention commitment colliding with deletion rights** | §10's own trigger: regulated payment flows, or dispute volume making evidentiary weight matter |
| **Ephemeral per-PR environments** | One person authors and reviews. The real cost is the data seeder, and it recurs | A second reviewer, or a non-technical reviewer who must see a change without running it |
| **A staging environment seeded with production data** | Forbidden by §7 and `AGENTS.md`. It would move every customer-private class into a weaker, unaudited environment — **silently voiding the operator-access directive** | Never |
| **Video and audio hosting** | ~$429/month at Growth scaling with anonymous views, 15× per-asset moderation, plus unpriced music-licensing exposure | §4.6's two-condition link-rot trigger |
| **A rendered map** | No public surface may lawfully show a pin at a provider's location (§12) | An approved requirement for street-level customer location input |
| **Payment technology** | `G-02` unresolved | `G-02` resolution |

## 8. Contradictions found in P02, and their disposition

P03 was instructed not to redesign P02 unless a technology constraint proved an assumption materially invalid. Four corrections met that bar and were applied; the rest are recorded as findings.

| # | Finding | Disposition |
|---|---|---|
| 1 | **§4's rule that the delivery intent is created *after* the business commit, "never in the same transaction, ever," opens a silent lost-notification window with a same-database job store.** It produces neither delivery-failure nor non-response state — it produces silence | **CORRECTED in `system-architecture.md` §4 and the two matching lines in `domain-map.md`.** `R-044` |
| 2 | §8 rejected "an observability platform" flatly, which appeared to forbid the free-tier error tracker `D-11` requires | **EDIT WITHDRAWN on adversarial review.** The permission was already in the row's own justification column — *"buy technical monitoring at the smallest tier"* — so the conflict was misread, and the edit had also silently inserted a **new** exclusion that was not in the P02 row. **No technology constraint justified changing it.** Recorded as a finding only |
| 3 | `domain-map.md`'s "no module may ever read from Analytics" appeared to forbid viewing the funnel the architecture requires | **EDIT WITHDRAWN on adversarial review.** The premise was wrong: §4.11 answers the funnel with **SQL against the transactional store**, not Analytics reads, so the conflict never arises on the recommended design. The absolute rule, the forbidden-dependency row, and the build-failing architecture rule now all agree again. Recorded as a note in `domain-map.md` |
| 4 | **Operator reads now require a recorded operational purpose**, which §9 did not previously demand | **APPLIED** from the `DAVID_DIRECTIVE`; `Q-026` resolved |
| 5 | `robots.txt` is not enforcement (RFC 9309); "ONE enforcement point" claims something the protocol cannot deliver | Recorded, `R-035`. Restated as one policy, two derived artifacts, with a divergence check |
| 6 | **No import-graph tool in any stack sees a cross-module foreign key** — the half of `ADR-001` that determines exit cost | Recorded, `R-034`. Closed by a catalog test |
| 7 | §11 collapses four structurally different geographic predicates into one row, overstating the geospatial dependency | Recorded, §4.2 |
| 8 | The route-corridor claim is **stated incorrectly** in three documents; the conclusion survives on better grounds | Recorded, §4.2 |
| 9 | §11's "largest new fixed line" framing assumes every search candidate has a fixed floor; usage-priced options do not | Recorded, §4.3 |
| 10 | **"Deletion proof" is a lock-in criterion no store can satisfy** and will be silently ticked by anything with a dump command | Recorded, `R-038`. Restated as a product capability with a disclosed recovery-window exclusion |
| 11 | A single timestamp column does not satisfy data rule 6 — the zone the instant belongs to is a second column | Recorded, `R-039` |
| 12 | **Passwordless email-only authentication cancels `D-05`'s free cost lever.** Neither document notices | Recorded, `R-040`. Non-email credential path is now a hard constraint |
| 13 | Vendor-side email suppression is global per address, so purpose-class separation has no reach inside the vendor | Recorded, `R-041`. Separate configuration sets and sending subdomains from day one |
| 14 | *"Small fixed derivative set"* + *"a processing state"* logically forecloses on-the-fly transformation | Recorded, §4.6 |
| 15 | `PublicationRequirement` risks conflating media existence with media servability | Recorded, `R-042` |
| 16 | **Provider media EXIF emits precise provider-private location to the public projection.** Not named anywhere in P02 | Recorded, `R-048`. Free fix in a re-encode that already happens |
| 17 | Expand/contract discipline is absent; *"rollback is cheap"* is false for schema | Recorded, `R-047` |
| 18 | Per-module database roles would forbid the cross-module read §4 requires | Recorded, `R-037`. Resolution stated |
| 19 | Analytics has no owner for its retention period and is 77–87% of the database | Recorded, `Q-034` |
| 20 | Facet-count cardinality is a page-shape decision presented as an infrastructure trigger | Recorded, `Q-035` |
| 21 | The corpus that would open `D-13` is itself privacy-gated, and the gate is assigned to nobody | Recorded, `Q-033` |
| 22 | **Public acquisition carries TWO unbounded variable-cost lines**, each written as if it were the sole exception | Recorded, `R-033`. Modelled jointly in `cost-model.md` |
| 23 | Analytics segmentation fine enough to be useful can re-identify at launch volumes | Recorded, `R-049` |
| 24 | `D-10`'s host criteria omit **per-process resource limits from a single artifact**, which `ADR-001` requires | Recorded, §4.10 |

## 9. Sources

**Repository inputs.** `AGENTS.md` · `docs/02-architecture/p03-decision-inputs.md` · `domain-map.md` · `domain-model.md` · `system-architecture.md` · `search-architecture-requirements.md` · `internationalization-architecture.md` · `security-privacy-architecture.md` · `integration-architecture.md` · `data-architecture.md` · `decision-branches.md` · `adr/ADR-001`–`ADR-012` · `docs/05-roadmap/mvp-scope.md`, `risks.md` · `docs/07-research/ai-discoverability.md` · `docs/03-technology/cost-model.md`, `infrastructure-options.md`, `technology-radar.md`, `build-vs-buy.md`, `ai-evaluation.md` · `SRC-013`.

**External primary sources — the canonical register is [`cost-model.md` §12](cost-model.md).**

**This document deliberately carries no inline URLs, and that is a governance decision made after adversarial review found the opposite arrangement indefensible.** An earlier draft deferred citations to "work-package records" that **are not in the repository**, which made every price here unfalsifiable and breached `AGENTS.md`'s requirement to persist authoritative primary sources. There is now **one** register — `cost-model.md` §12 — naming, for every load-bearing figure: the vendor, the plan, the price as read, the URL, and the access date of **2026-08-11**. It also carries an explicit `NOT VERIFIED` table.

**Repository state, not chat history, holds the sources. If a figure in this document is not traceable to that register, treat it as unverified.**

**The principal `NOT VERIFIED` gaps, restated here because they qualify conclusions in this document rather than only prices:** the deep comparative case against MySQL and other relational candidates (§4.1 — PostgreSQL was verified *sufficient*, the alternatives were **not** verified insufficient); the object-storage custom-domain and content-type seam (§4.6); the two legal readings on geocoding terms (§4.7); several managed-hosting instance prices and standby-node pricing; and delivery-network cache-purge propagation times, which **no vendor publishes** — meaning P20's bounded propagation must be **measured in V1, never assumed.**

**Closed by P03.1, and the result went against this document's own conclusion:** ~~password-hash exportability for any authentication vendor~~ was verified on 2026-08-12 and is **self-service for Supabase and Clerk**, **gated for Auth0**, and has **no documented mechanism for Cognito**. `ADR-017` moved to `HOLD`. **Residual gaps carried in its place:** Clerk's exported hash format; whether Auth0 access tokens survive session revocation; and Auth0's support-case export SLA. Full evidence: `docs/07-research/authentication-vendor-verification.md`.
