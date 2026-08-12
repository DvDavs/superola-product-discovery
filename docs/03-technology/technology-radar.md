# Technology Radar — P03

> **Status:** every entry below is `PROPOSED — DAVID APPROVAL REQUIRED`. **No technology is adopted.** Movement into an accepted state requires David's approval and, where the convention applies, an ADR.
>
> **Standing qualifier:** `DAVID WORKING ASSUMPTION — OWNER VALIDATION PENDING` (`SRC-013`). **Scenario stamp: S-1.** All capability and price claims accessed **2026-08-11**; re-verify before purchase.
>
> This is the one document in the repository where vendor and product names carry a recommendation. The reasoning lives in `technology-evaluation.md`; this is the index of **what moved, in which direction, and why.**

## Rings

| Ring | Meaning |
|---|---|
| **SELECTED** | The P03 recommendation for the primary scenario. `PROPOSED — DAVID APPROVAL REQUIRED` |
| **RUNNER-UP** | Evaluated, lost on a stated axis, and would be the fallback if the recommendation is falsified |
| **DEFERRED** | Not now, with a named measurable trigger |
| **REJECTED** | Not for this product shape, with a named trigger |
| **DISQUALIFIED** | Fails a hard requirement or a terms constraint. Not a price judgement |
| **GATED** | Cannot be evaluated until an owner or legal gate resolves |
| **NOT EVALUATED** | Named in the brief or discovered, but not researched. **A gap, not a dismissal** |

## Current engineering context

Evidence: `CONFIRMED`. Provenance: `DAVID_DIRECTIVE` (self-reported). David has experience with Kotlin, Java, Spring Boot, React, Next.js, TypeScript, PostgreSQL, PostGIS, Docker, CI/CD, n8n, GoHighLevel, AI-assisted development, and infrastructure work.

**This is a delivery-velocity and delivery-risk input. It is never technical proof, and `R-007` exists to catch the substitution.** Where a P03 recommendation aligns with this list, `technology-evaluation.md` states the technical grounds that would carry the decision **without** it, and names what would have to be falsified for the recommendation to fall. Two entries below are recorded as *not cleanly won* for exactly this reason — see PostgreSQL and PostGIS.

---

## Application platform

| Technology | Ring | Note |
|---|---|---|
| **Kotlin on Spring Boot 4.1 / Java 25 LTS** | **SELECTED — on two grounds, not four** | Verified 2026-08-11: Spring Boot 4.1.0 (10 Jun 2026), Spring Framework 7.0.8, Java 17 minimum through Java 25/26. **Adversarial review narrowed the case and it is recorded narrowed.** The two surviving grounds: (1) a **first-party modular-monolith verifier** transcribing `domain-map.md`'s forbidden-dependency table at one annotation per boundary — no other evaluated ecosystem ships one; (2) a **dependency configuration closed by default**, where the runner-up's is transitive by default and therefore opt-out. **Withdrawn as non-differentiating:** the transactional outbox and post-commit event semantics, both available in every candidate ecosystem. **Withdrawn as contradicted by this phase's own `R-036`:** "longest support horizon" substituted the language runtime's horizon for the framework's, and **on framework cadence the recommendation is worse than the runner-up** — ~12-month open-source support per minor on a ~6-month cadence, roughly one upgrade per year, forever, for one part-time developer |
| **Spring Modulith 2.1 GA** | **SELECTED** | Verified 2026-08-11: `ApplicationModules.verify()` throws — and therefore fails the build — on module cycles, references into another module's internal packages, and **any dependency not declared in `allowedDependencies`**. That third rule is a literal transcription of `domain-map.md`'s forbidden-dependency table. **This is what makes P02's "the one place P03 should be willing to spend" cost one annotation per boundary**, and it answers `ADR-001`'s open question about boundary count |
| **ArchUnit** | **SELECTED** | Second enforcement layer for rules module verification cannot express. Bytecode-only by its own documentation — hence the third layer below |
| **A cross-schema foreign-key catalog test** | **SELECTED** | ~30 lines. **The only layer that catches the half of `ADR-001` no import-graph tool can see** (`R-034`) |
| **C# / .NET 10, ASP.NET Core** | **RUNNER-UP** | Genuinely close. Lost on: no first-party equivalent to the modular-monolith or outbox products, both of which would be bespoke; a shorter support horizon; and a verified default that cuts against the decisive criterion — **project references are transitive by default**, so A compiles against C. Closable, but **opt-out** where the recommendation is closed by default |
| **TypeScript / Node full-stack framework** | **RUNNER-UP** (rendering) / **REJECTED** (backend) | Strongest on developer familiarity and rendering ergonomics. Lost because its own vendor documentation states the backend capabilities *"are not a full backend replacement"*; because of a verified record of removals, renames and behaviour changes in its latest major; and because self-hosting requires a shared cache handler that P20 turns from optional into mandatory — a new always-on component to make caching *correct* |
| **Separate JS frontend + backend API (two deployables)** | **REJECTED** | `ADR-001`: the first additional deployable unit is by far the most expensive one, applied to the tier where it buys least, at volumes where it buys nothing. Trigger: the rendering triggers in `technology-evaluation.md` §4.9 |
| **htmx + server-rendered templates, React islands where justified** | **SELECTED** | Matches §3's product-wide no-push stance exactly. Island count above five is a reconsideration trigger |
| Go | **REJECTED** | Its only compile-enforced boundary primitive expresses subtree privacy, not a **directed graph of ten siblings with a named forbidden-edge list**. Its real advantages — small footprint, fast start — are worth nothing at these volumes. Trigger: memory cost becomes material **and** the boundary set has shrunk to ≤4 |
| Python / Django | **REJECTED** | **GeoDjango is the best geospatial developer experience in the comparison and this is recorded as a genuine loss.** Rejected because no compilation step means no mechanism that fails a build — only a lint gate. Choosing the weakest enforcement to buy geospatial ergonomics spends P02's stated budget in the wrong place, especially since §11 establishes the eligibility query is hand-written SQL in every candidate |
| Ruby / Rails | **REJECTED** | Same structural objection. Its boundary tooling is the most serious in a dynamic language but is still static analysis, not a compiler |
| Elixir / Phoenix | **REJECTED** | Rejected on **architectural contradiction, not merit**: its flagship interaction model holds a persistent connection per client, and §3 states *"no V1 surface requires server-initiated push."* Using it as designed reverses a P02 decision quietly. Also the smallest hiring pool against a one-developer constraint |
| Blazor Server | **REJECTED** | Same persistent-connection objection |
| Astro | **REJECTED as the whole product** | Excellent for public pages; not designed to own the authenticated surfaces that are the bulk of the build. Public pages in one tool plus the app in another **is the two-deployable shape with an extra runtime** |
| Kubernetes / container orchestration | **REJECTED** | `D-10` explicitly. Trigger: ≥3 independently deployed units with divergent scaling profiles **and** ≥2 teams blocked on one release train. **Both** |
| Serverless / functions for the application | **REJECTED** | Fights the single-deployable model and §4's transactional boundaries |
| GraalVM native image, ahead-of-time compilation, class-data sharing | **DEFERRED** | Build complexity buys startup time and memory — the two least valuable properties here. Trigger: measured memory cost becomes material |

## Data

| Technology | Ring | Note |
|---|---|---|
| **PostgreSQL 18** | **SELECTED — but not cleanly won, and narrower than first written** | **Three capabilities genuinely differentiate** it for this model: **partial indexes** carrying the publication allowlist predicate inside the index; **in-core Spanish and English full-text search** with accent folding and trigram similarity, which is the entire Stage-2 deferral of a search engine; and a **variable-depth tree extension** for the `Place` parent chain. **Adversarial review correctly struck two more that an earlier draft listed:** declarative partitioning is not a differentiator (the runner-up has range partitioning, and *"turns a vacuum problem into a detach"* is circular because vacuum is a PostgreSQL-specific problem), and the queue-table locking clause is not either (the runner-up has it too). **Array containment is arguable** — the runner-up offers multi-valued indexes over JSON arrays, and whether they satisfy the `ServiceAreaDeclaration` containment semantics was **not** checked. **`R-007` discipline: PostgreSQL was verified *sufficient*; the alternatives were NOT verified *insufficient* — and the honest list of live differentiators is three, not five.** |
| **PostGIS** | **SELECTED — confined to ONE of four predicates** | **Three of P02's four geographic predicates are not geospatial problems**: point-in-named-place is a tree-ancestry test, postal-prefix zones are string prefix matching, and proximity sort runs over an already-filtered set. The surviving predicate works without PostGIS too — the argument for taking it is that an ellipsoidal distance **deletes a governance decision** rather than forcing an owner to approve a spherical-approximation tolerance on an eligibility boundary the repository already flagged as correctness-critical. **Confinement is the point: reversal is one function body, one column type, one index.** A build-enforced rule must prevent its use in the other three |
| MySQL 8.4 LTS | **RUNNER-UP** | Verified geospatially adequate. **Did not lose on evidence — it lost on unverified differentiators.** Recorded as runner-up rather than eliminated precisely because dressing an unverified comparison as a conclusion is the `R-007` failure. If someone verifies partial indexes and array containment and MySQL matches, `D-01` reopens |
| `cube` + `earthdistance` | **RUNNER-UP** | Index-accelerated radius filtering with no extension beyond contrib. Loses on the spherical-approximation governance point, and its point-based operator hardwires statute miles against the explicit-unit rule |
| `ltree` / ancestor arrays with containment indexes | **SELECTED** | The correct mechanism for two of the four predicates. No geospatial capability involved |
| PostgreSQL native full-text search — Spanish stemmer, accent folding, trigram similarity | **SELECTED for Stage 2** | Verified in core. **Gotcha that shapes the design: the accent-folding function is `STABLE`, not `IMMUTABLE`, so it cannot appear in an index expression.** Normalise at write time into a stored column — which makes the normalisation rule a governed versionable artefact rather than hiding it in an index definition |
| CockroachDB | **REJECTED** | Its value is horizontal write scaling and multi-region — both explicitly rejected by P02 — and it imposes serializable-retry handling on every transaction, application complexity bought for a problem V1 does not have |
| SQLite / libSQL | **REJECTED** | Single-writer concurrency and the operational shape of point-in-time recovery are a poor fit for concurrent RFQ writes plus analytics ingest plus discovery reads |
| MongoDB / document stores | **REJECTED** | P02's model is relentlessly relational — 40+ entities, heavy many-to-many, and a rule that attribute values must reference an existing definition with **no orphan keys**, which `domain-model.md` calls *"the legacy failure in miniature."* A document store's advantage is denormalised aggregate reads; this product's core query joins across aggregates and enforces referential discipline. **It would move the exact constraint that killed the legacy platform out of the database and into application code** |
| Multi-model stores | **REJECTED** | Bought to avoid running three stores. This design runs one |
| SQL Server / Azure SQL | **NOT EVALUATED** | Provisionally set aside on licensing and small-team operability, **with no verified price.** A gap |
| MariaDB | **NOT EVALUATED** | A gap |

## Search

| Technology | Ring | Note |
|---|---|---|
| **Authoritative SQL with the allowlist as a query predicate** | **SELECTED — Stage 0/1** | `ADR-006`. At Growth the discovery working set is ~395 MB and fits in memory — **latency is unlikely to be the trigger that fires** |
| **A governed synonym and alias table** | **SELECTED — ships at Pilot regardless** | ~3 tables, ~1 MB. Already required as a data model by `D-12`. Named by `ADR-006`, `D-03` and `D-13` as the cheap alternative that must be exhausted first. **The single cheapest high-leverage capability in the search stack** |
| Typesense, Meilisearch, OpenSearch, Elasticsearch, Algolia, managed search | **DEFERRED — Stage 3 only** | Triggers `T5`–`T8` in `technology-evaluation.md` §4.3, each with named V1 instrumentation. **A cost-framing correction:** §11 calls a dedicated engine *"likely the largest single new fixed line"* — true for a serverless cluster with a verified **$350.40/month floor** at 8,000 searches/month, **false** for usage-priced managed search at ~$0 at Pilot and ~$295/month at Growth. The argument should rest on the pricing-model-independent costs: full reindex on every taxonomy change (`R-010` guarantees these), a permanent divergence support queue, two sources of truth, and a new *published but not yet discoverable* product state |
| Vector store, embeddings, semantic retrieval | **REJECTED** | `search-architecture-requirements.md` §12 excludes it; §6 states vector recall feeds on behavioural signal that does not exist at launch — zero clicks, zero conversions, zero reviews. **It would be tuned on nothing, evaluated against nothing, and would obscure the coverage problem it would be sold as solving.** Declining forecloses nothing: the capability exists inside PostgreSQL if ever approved |

## Deferred work, cache, and messaging

| Technology | Ring | Note |
|---|---|---|
| **A job table in the same PostgreSQL instance** | **SELECTED** | **$0 incremental always-on infrastructure.** Satisfies every clause of `D-04`'s verbatim requirement. At Growth the load is **0.096 jobs/second average**, ~2/s peak, against libraries documenting 1,000+/s — roughly **100× headroom**. And it is the only option under which `system-architecture.md` §4's durability requirement holds literally (`R-044`) |
| Cloud queues | **REJECTED** | Loses transactional enqueue, reintroducing the dual-write problem; puts the dead-letter state **outside the audit boundary**, so a retry has no actor and no reason |
| Redis / Valkey — as queue or cache | **REJECTED** | The queue case is answered at $0; the cache case is answered by the delivery network at $0 plus the database's own notification channel for cross-replica invalidation. It would be the **first new always-on component** and the **first non-transactional store**. Its default durability model is a poor match for the word *durable* when the payload is a marketplace notification intent. **$5/month is not the objection — the second failure domain is.** Trigger: shared mutable state that is **not** a cache, specifically rate limiting where measured abuse shows per-replica counters failed |
| Kafka, RabbitMQ, any broker or event stream | **REJECTED** | Every deferred action has one known consumer and one known effect. Trigger: `system-architecture.md` §5's own — a second *independent* consumer with a real named owner |
| Delivery-network edge cache with purge on suppression | **SELECTED** | The anonymous-public-read cache P20 requires. **No vendor publishes a purge propagation time**, so P20's bounded propagation must be **measured in V1, never assumed** |
| In-process cache invalidated by the database notification channel | **DEFERRED — second layer, only if measured** | Works across replicas at $0. **Its failure mode is genuinely unpleasant** — a missed notification produces a stale replica, intermittent and replica-specific. A short time-to-live is not an optimisation, it is the safety net |

## Integrations

| Technology | Ring | Note |
|---|---|---|
| **AWS SES** | **SELECTED** | Models a **third delivery state** — in-flight-not-yet-failed — that no other candidate was verified to model. Fold it into bounce and you suppress a working address; fold it into delivery and you tell a provider a message landed that did not. **And it does not retain message bodies by default**, which matters because the body carries a non-guessable link into the authenticated surface |
| Postmark | **RUNNER-UP** | Finer bounce taxonomy — notably distinguishing an auto-responder, which is neither delivery failure nor human response and whose conflation corrupts the `R-016` metric. Lost because its advantages concentrate in features `ADR-010` makes unusable, and it retains bodies |
| Resend | **REJECTED** | Verified 30-day data retention. Disqualifying given what the body contains |
| Mailgun | **REJECTED** | Log retention of 1–5 days on lower tiers makes reconciliation impossible beyond the window |
| SendGrid, Brevo, Loops, MailerSend | **NOT EVALUATED** | A gap |
| SMS, push notifications | **REJECTED for V1** | Each is *"a second copy of the whole reliability apparatus"* plus per-jurisdiction consent and registration. Push additionally needs a channel decision `A-005` has not made |
| **Cloudflare R2** | **SELECTED** | **Egress $0.00, verified.** The right response to `D-06`'s dominant variable line is to eliminate it. Also the best exit story in the evaluation — same API in and out, **and you are not charged to walk out.** **Highest-priority unresolved item before adoption: whether objects served through a custom domain on an entry plan fall under the developer-platform exemption or the content-delivery restriction on serving *"a disproportionate percentage of pictures"* — and Superola's public pages are image-heavy by design** |
| Bunny.net | **RUNNER-UP** | Cheap, no request fees, no ambiguous terms clause. Loses on metered egress and a South America rate 4.5× its North America rate, with **Mexico's classification unverified** — an unverified multiplier on the dominant variable line |
| Cloudinary, imgix, ImageKit, Uploadcare, Cloudflare Images transformations | **REJECTED** | **On-the-fly transformation is architecturally excluded**, not merely expensive: *"a small fixed derivative set"* plus *"a processing state so no half-processed media is publicly visible"* are incompatible with generating a derivative on first request. Worth ~$177/month at Growth if adopted anyway |
| Mux, Cloudflare Stream, Bunny Stream — video hosting | **REJECTED** | ~$429/month at Growth scaling with anonymous views; 15× per-asset human moderation because a person must watch; **plus unpriced music-licensing exposure in exactly the owner-reported categories** (`R-043`). Trigger: the two-condition link-rot test in `technology-evaluation.md` §4.6 |
| **Link-out to provider-hosted audio and video** | **SELECTED** | Converts the highest variable-cost line in the product into a near-zero one, cuts moderation labour ~3×, and **materially strengthens the rights position** |
| AWS S3 + CloudFront, Backblaze B2, DigitalOcean Spaces | **NOT EVALUATED** | Two central numbers — Latin American egress and invalidation cost — were not obtainable from official pages. **Under this repository's rules that is an elimination, not a deferral**, but it is recorded as a gap |
| **A governed internal `Place` list** (open gazetteer + national statistical authority hierarchy) | **SELECTED** | **Not a cost optimisation — it is P02's own design, unrecognised.** `domain-model.md` §1.4 already specifies it. Reduces the geocoding line ~70× against the same vendor, ~132× against the disqualified platform and makes geocoder downtime a non-event |
| **Mapbox permanent geocoding — provider base addresses only** | **SELECTED** | Its accuracy classification maps almost one-to-one onto the three-valued `LocationEligibility`, which is the structural fix for the reported legacy location defect. **Unresolved and could flip the recommendation:** whether its general termination clause reaches permanently-licensed geocodes |
| OpenCage | **RUNNER-UP** | **The best exit terms verified anywhere in this evaluation** — results storable indefinitely, including after ceasing to be a customer — and it returns components that can carry a Mexican *colonia*. Loses on no autocomplete, a monthly floor, and a confidence signal that is a bounding-box-size proxy rather than a match-type taxonomy |
| Google Maps Platform | **DISQUALIFIED** | **Twice.** Its policies forbid storing the resolved coordinate, which Superola must persist to evaluate eligibility offline. And its **one** permitted persistence exception is its own place identifier — which `domain-model.md` §1.4 forbids as `Place` identity. **The vendor's only lawful persistence mechanism is exactly what the architecture prohibits.** Separately, its session-token billing means an **abandoned** address entry costs ~2.2× a completed one, landing directly on `R-022` |
| Nominatim public instance | **DISQUALIFIED** | Its usage policy explicitly forbids autocomplete |
| LocationIQ | **DISQUALIFIED** | A termination-deletion clause on the exact artefact eligibility depends on |
| Self-hosted Photon / Nominatim | **REJECTED** | An always-on component with a multi-gigabyte import and periodic reimport — **a hiring decision to save ~$50/month** |
| A rendered map, MapLibre, tile providers | **REJECTED for V1** | §12 classifies precise provider base location as provider-private, never emitted to a projection. **There is no public surface on which a pin at a provider's location could lawfully be drawn.** One product decision removes the largest line on every vendor's price sheet *and* dissolves the map-coupled storage restrictions. Trigger: an approved requirement for street-level customer location input |
| HERE, Radar, MapTiler, AWS Location, Azure Maps | **NOT EVALUATED** | Radar and HERE publish no rates on the pages fetched; the rest are gaps |
| **Application-owned authentication on the framework's security library** | **SELECTED** | Price is **not** a differentiator here — two vendors are $0 at all three tiers. **The decision is architecture fit and exit.** Four independent failures on the buy side, and the deciding asymmetry: **BUILD→BUY is documented and supported; BUY→BUILD may be impossible** |
| Supabase Auth | **RUNNER-UP** | $25/month flat with headroom, custom sending included at every tier, no organizations gravity, and **users in a queryable schema — so "export the hashes" is a query.** Categorically better exit than any hosted-only vendor. **The runner-up case rests on one unverified fact: whether the password-hash column is readable and exportable on the paid plan** |
| Auth0 | **REJECTED** | **Its free tier ends at exactly 25,000 monthly active users — the Growth target.** Building identity on a free tier whose ceiling *is* the plan is choosing a cliff already scheduled |
| Clerk | **REJECTED** | Excellent developer experience and a distant cliff. **Prices the forbidden model — organizations, roles, permissions — at $100/month.** `ADR-011` places authorization in the domain; that is $100/month to violate the architecture, and a future engineer under deadline pressure will see a solved problem rather than a boundary violation |
| AWS Cognito | **REJECTED** | The only candidate that charges meaningfully at these volumes, and the exit question is unresolved |
| WorkOS, Firebase, Stytch, Kinde, Logto, Ory, Keycloak, FusionAuth, Descope, Zitadel, SuperTokens | **NOT EVALUATED** | Eleven named candidates unscored. **A real gap** |

## Operations

| Technology | Ring | Note |
|---|---|---|
| **Free-tier error tracking + free-tier log storage + external uptime + a job dead-man's switch** | **SELECTED** | **$0.00/month at Pilot, verified.** The dead-man's switch is the most under-valued item: it is the only mechanism that catches *"the freshness sweep silently stopped running"* — **an error tracker structurally cannot detect the absence of an event** |
| **OpenTelemetry instrumentation** | **SELECTED** | Makes the backend swappable, which is the exit obligation `AGENTS.md` imposes. Cost: a library |
| OpenTelemetry collector, trace export | **DEFERRED** | A second always-on component and the highest-cost signal, for a single process. Trigger: more than one deployable emits telemetry |
| Datadog, New Relic, and per-host or per-indexed-event platforms | **REJECTED** | **~$92/month floor at Pilot before a single log line; ~$940+/month at Growth.** And `ADR-001`'s separate worker process is a **second billable host — so the architecture's own blast-radius mitigation doubles the monitoring bill.** This is `D-11`'s named trap with a price attached |
| Distributed tracing | **REJECTED** | There is one process. Trigger: the first additional deployable unit |
| Session replay | **REJECTED** | **Structurally incompatible with §12**, not merely expensive — it would capture request free text, classified customer-private and forbidden from export |
| A data warehouse or reporting stack | **REJECTED** | The funnel is durable domain state queried directly. Trigger: an analytics query degrading p95 **after** rollups and a read replica have both been tried and recorded insufficient |
| Self-hosted query front-end for the operator funnel | **DEFERRED** | Free and viable — **but it must connect through a read-only role whose grants exclude every customer-private column**, or it is the unaudited console P02 forbade with a nicer interface. **If curating that grant set is more work than writing three views, write the views** |
| **Forge-hosted pipeline as the gate + host-native deploy hook** | **SELECTED** | Verified free-tier minutes comfortably exceed one developer's realistic usage, with a smooth per-minute overage curve rather than a plan-tier step. **Host auto-deploy alone cannot fail the build on a boundary violation before the artifact is produced** — and the entire cost case for `ADR-001` depends on that gate. Do not enable both, or there are two paths to production |
| **Testcontainers-backed integration tests** | **SELECTED** | The deciding argument is specific: the audit controls **are grants**. They must be asserted against a real database with real role separation. **If the test runs as superuser, the single most important audit control passes vacuously** |
| **Platform-native declarative infrastructure file** | **SELECTED** | Free, reviewed with the code, no state file |
| Terraform, OpenTofu, Pulumi | **REJECTED at Pilot** | 5–8 resources, one environment, one person — **zero of the five threshold conditions hold.** The carrying cost is the **state file**, a new stateful artefact whose corruption is an outage-class event. Trigger: **any two** of the five conditions in `infrastructure-options.md` §9 |
| Dependabot or Renovate, plus container-image scanning, plus secret scanning in the pipeline | **SELECTED** | All $0. Image scanning is the gap dependency scanning leaves |
| Commercial vulnerability scanning at the free tier | **REJECTED** | **It meters test count** — a security control that stops working when you commit more is the wrong unit for this scope |
| Managed bot mitigation | **REJECTED while the crawler gate is closed** | Its verified-bot allowlist is a **second, vendor-owned crawler policy** that would approve `Q-015` by accident, before `Q-027` is settled (`R-046`) |
| Dedicated secrets-management products, self-hosted secret managers | **REJECTED** | Platform-native variables plus encrypted pipeline secrets at $0. A dedicated store adds a third party to the boot path and a single-operator lockout risk with no second human to recover. **The largest secrets risk is lockout, and no product fixes it** — an offline sealed copy of recovery material does |
| Write-once audit storage | **REJECTED** | §10 rejects it, **and it creates an undeletable retention commitment colliding with deletion rights** |
| **An independent off-platform encrypted backup** | **SELECTED** | Under $1/month at Pilot. **Host point-in-time recovery covers operator error; only this covers account termination, suspension or compromise — and with one operator, account loss is the most plausible catastrophic failure.** The cheapest insurance in the model and the item most likely to be skipped |

## AI

| Technology | Ring | Note |
|---|---|---|
| **A natural-language intent mapper of any provider** | **GATED — and the recommendation is DO NOT SPEND** | `D-13`'s gate is closed and **unanswerable in P03**: the corpus does not exist, the instrumentation ships *with* V1, it needs months of operating time after that, and **the corpus is itself privacy-gated by a decision assigned to nobody** (`Q-033`). Full record in `ai-evaluation.md` |
| A non-model classifier over the governed vocabulary | **DEFERRED — evaluate before any model** | **The output space is closed, which makes this a classification problem, not a generation problem.** Deterministic, explainable by construction, no privacy gate, no rate-limit surface, no provider drift |
| Purpose-built moderation classification for uploaded media | **DEFERRED — conditional** | Async and non-blocking only. **If it gates publication, a model outage becomes a publication outage.** And if link-out is chosen for performers there may be no hosted media to moderate |
| Model-assisted taxonomy mapping during a future legacy import | **GATED on `G-09`** | The only genuine win on the off-critical-path list, because the unit is *per distinct legacy string, not per record* — so drafting is most of the work and human review is fast |
| Model-generated provider profile content | **REJECTED** | It generates public content Superola publishes, producing exactly the homogeneous pages `R-011` warns become search-quality debt — **on the surface carrying the acquisition hypothesis.** The free alternative is strictly better: prompt the provider about which governed fields are missing |
| MCP, A2A, A2UI, UCP, ACP, `llms.txt` | **REJECTED for V1** | `R-012` and the anti-inflation rule: **no V1 deliverable may exist whose only consumer is an emerging protocol.** P02 already carries the entire structural preparation, each piece independently justified by a Phase-1 need. **Nothing further is needed.** Trigger: a named approved channel with **confirmed access, measurable economics, and target-market availability** — all three |

## Gated and excluded

| Area | Ring | Note |
|---|---|---|
| Payment, booking, payout, refund, dispute, tax | **GATED — not evaluated** | `G-02` unresolved, `A-004` OPEN, `D-15` forbids evaluating it. **No cost line in P03 covers any of it** |
| Legacy import environment | **GATED** | `G-09` plus its legal questions. **Claim adjudication is zero in every operator estimate because no cohort is approved — the single largest swing factor in the operator table** |
| Billing and subscription | **GATED** | `ADR-008`: the seam is one decision point, not a system |
| n8n, GoHighLevel, Floci | **NOT EVALUATED** | Named in the pre-P03 radar. No V1 requirement was identified that they serve; local-development tooling is a post-selection question |

## Movement log

| Date | Movement |
|---|---|
| 2026-08-11 | P03 opened. Every candidate above moved from *unranked candidate* to a ring. **No entry reached ADOPTED — all selections are `PROPOSED — DAVID APPROVAL REQUIRED`.** |
| 2026-08-11 | `ADR-004` moved `PROPOSED` → `ACCEPTED` on a `DAVID_DIRECTIVE`. The first accepted ADR in the repository. Binding consequence: any authentication mechanism forcing role-typed accounts is disqualified |

## Sources

All external claims accessed **2026-08-11**. **The canonical per-line source register — vendor, plan, price as read, URL and access date — is [`cost-model.md` §12](cost-model.md); model-provider prices are cited per line in [`ai-evaluation.md` §5](ai-evaluation.md).** Reasoning lives in `technology-evaluation.md`, `infrastructure-options.md` and `build-vs-buy.md`.

**This radar names vendors but deliberately does not duplicate their citations.** There is one register, and adversarial review is why: an earlier draft deferred citations to work-package records that **are not in the repository**, which made the whole cost model unfalsifiable. **Sources now live in repository state, not in a transcript.**

**The `NOT EVALUATED` ring is a gap register, not a dismissal.** Eleven authentication vendors, several object-storage and mapping candidates, and the deep relational comparison against MySQL are unscored. **The single most important unverified item in the entire evaluation is not a price: password-hash exportability could not be confirmed for any authentication vendor — and that is the axis on which `D-08` is recommended.**
