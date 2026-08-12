# P03 — Technology & Cost Evaluation

## Metadata

- ID: P03
- Title: Technology & Cost Evaluation
- Owner: David
- Status: COMPLETED
- Started: 2026-08-11
- Last updated: 2026-08-11
- Authorization: explicit David authorization (`SRC-014`) to start P03 against `docs/02-architecture/p03-decision-inputs.md` (`D-01`–`D-15`).
- Architecture baseline carried forward: the P02 `PROPOSED` logical architecture. **P03 does not re-open it** unless a technology constraint proves a P02 assumption materially invalid.
- Technology status produced by this phase: `PROPOSED — DAVID APPROVAL REQUIRED`. Nothing here is an adopted stack.

## Objective

Select **one** practical technology architecture for the primary working scenario, and quantify its cost and operational tradeoffs with source-backed current prices.

P03 answers `D-01`–`D-15` or states explicitly why a problem stays gated. It does not implement anything and does not start P04–P07.

## Non-goals

- Re-designing the P02 domain or system architecture.
- Evaluating payment, booking, availability, entitlement, sponsored-inventory, or protocol technology — each is gated.
- Selecting a search engine, a message broker, a cache product, or an observability platform without trigger evidence.
- Physical schema design beyond what is needed to assess a technology.
- Producing a numeric non-functional-requirements table. Every number carries a source and a volume scenario or it does not appear.
- Sizing anything to the unaudited ~43,000 legacy registration count.
- Starting P04, P05, P06, or P07.
- Pushing, creating or modifying remotes, or touching historical stashes.

## Context and authoritative inputs

| Input | Why it is authoritative here |
|---|---|
| `docs/02-architecture/p03-decision-inputs.md` | The bounded research backlog. `D-01`–`D-15` with evaluation criteria and the cross-cutting criteria table. |
| `docs/02-architecture/system-architecture.md` | The deployment shape, consistency map, transactional boundaries, the durable-deferred-work requirement, and the 21 checkable principles. |
| `docs/02-architecture/domain-map.md` | Module boundaries, dependency direction, forbidden dependencies, per-module cost direction. |
| `docs/02-architecture/search-architecture-requirements.md` | §11 is the measured threshold for dedicated search infrastructure and the full cost table of a separate read store. |
| `docs/02-architecture/security-privacy-architecture.md` | §10 is a proportionality budget: controls deliberately not recommended. §12 is the data classification allowlist. |
| `docs/02-architecture/internationalization-architecture.md` | §5: multi-country data and policy is **not** multi-region infrastructure. |
| `docs/02-architecture/integration-architecture.md` | The owned-boundary rule and the integration categories deliberately absent from V1. |
| `docs/02-architecture/decision-branches.md` | `DB-01`–`DB-14`, the release-gate status, and the load-bearing ranking. |
| `docs/02-architecture/adr/ADR-001`–`ADR-012` | All `PROPOSED`. `ADR-001`, `ADR-006`, `ADR-011`, `ADR-012` constrain P03 most directly. |
| `docs/05-roadmap/mvp-scope.md` | The B0 envelope, the cost and operating boundary table, and the operating envelope. |
| `docs/05-roadmap/risks.md` | `R-007` (familiarity as selection), `R-021` (approval without TCO), `R-031` (numbers without sources), `R-032` (unenforced boundaries). |
| `docs/07-research/ai-discoverability.md` | The privacy gate for any model experiment and the anti-inflation rule. |
| `docs/03-technology/technology-radar.md` | Candidates and David's experience as **evaluation input only**, never selection. |

Pending inputs remained pending and nothing here inferred their content: `SRC-003`, `SRC-004`, `SRC-005`, `SRC-006`, `SRC-007`, `SRC-002`.

## Known constraints and evidence labels

### The evidence situation — stated plainly

- **There is no traffic evidence at all.** `SRC-006` is NOT RECEIVED. Every volume figure used in this phase is a **stated assumption**, labelled as such, and exists only to make cost lines comparable between candidates.
- **The reported ~43,000 legacy registrations are owner-reported and unaudited** (`A-001`, `R-001`) and size nothing.
- **The P01.1 release gate is still not satisfied.** `G-06` and `G-10` unsatisfied; `G-01`, `G-04`, `G-05`, `G-07`, `G-09` partial. P03 proceeds on David's authorization, as P02 did.
- **`G-02` is unresolved**, so no payment technology is evaluated. `D-15` is recorded as an excluded future sensitivity only.

### DAVID_DIRECTIVE decisions recorded at P03 start

Three directives were supplied with the P03 authorization. They are recorded in the canonical registers, not only here.

| # | Directive | Where recorded |
|---|---|---|
| 1 | **One Account MAY act as both customer and provider.** No mutually exclusive customer/provider account types. | `Q-028` resolved; `ADR-004`; `docs/01-product/open-questions.md` |
| 2 | **Operator access to customer/provider private marketplace content must be policy-governed, least-privilege, auditable, and carry a recorded operational purpose.** No silent unrestricted operator access. | `Q-026` resolved; `docs/02-architecture/security-privacy-architecture.md`; `docs/01-product/open-questions.md` |
| 3 | **`accepting requests != guaranteed date availability`** for the current V1 design. The customer supplies event date/time as context; the provider confirms feasibility through the request and quote interaction. Real-time universal availability and bookable calendar sync are **not** V1 requirements. Guaranteed/bookable calendar availability is a **decision branch**, not the primary scenario. | `DB-10` primary assumption reaffirmed; `ADR-005`; `Q-007`/`G-06` remain open as an **owner** question for P04 |

Directive 3 narrows the working assumption but does **not** satisfy `G-06`. `G-06` asks what "available" promises a **customer**; the directive answers what V1 **builds**. P04 still needs the owner's answer before designing profile and request surfaces.

### Standing technology constraints inherited from P02

- One deployable artifact; out-of-band execution is a separate process from the **same** artifact. No Kubernetes, no microservices, no multi-region.
- No message broker, event bus, or event stream. The requirement is durable deferred work, and P03 must compare an option that adds **no new always-on operational component**.
- No realtime transport. The only near-realtime requirement is safety-suppression propagation to the anonymous-public-read cache.
- No separate search store in V1. Discovery queries source truth with the publication allowlist applied as a predicate inside the query.
- Mechanical module-boundary enforcement in the build: a forbidden cross-module import must **fail the build**. `R-032`.
- Every external capability is reached through an interface in Superola's vocabulary. No vendor identifier, error, or data shape in domain state or public output.
- Launch scenario **S-1**: one geography, one production locale, multi-country and multi-locale capable.
- Familiarity is a delivery-velocity input, never a selection argument (`R-007`).

### Volume scenarios — stated assumptions, not evidence

Used identically by every work package so cost lines are comparable. Not derived from any Superola measurement.

| | Pilot / Validation | Early Marketplace | Growth |
|---|---|---|---|
| Published providers | 150 | 1,500 | 10,000 |
| Public page views / month | 10,000 | 150,000 | 1,500,000 |
| Marketplace searches / month | 8,000 | 80,000 | 600,000 |
| RFQs / month | 300 | 3,000 | 20,000 |
| In-platform messages / month | 1,500 | 20,000 | 150,000 |
| Transactional emails / month | 3,000 | 35,000 | 250,000 |
| Stored images | 1,200 | 12,000 | 100,000 |
| Media bytes served / month | ~15 GB | ~250 GB | ~2.5 TB |
| Analytics events / month | ~50,000 | ~800,000 | ~8,000,000 |
| Markets / locales | 1 / 1 | 1–2 / 1–2 | 3+ / 2 |
| People operating it | 1 part-time | ~1 FTE | small team |

## Tasks and delegation strategy

Six advisory read-only specialists in parallel; the primary agent is the only canonical writer and reconciles every contradiction.

| Specialist | Mandate | Owns |
|---|---|---|
| `application_stack_reviewer` | Web/backend stack against the P02 modular architecture; module-boundary enforcement mechanisms. | `D-09`, `D-10` (build side), `D-12` (partly) |
| `data_search_geo_reviewer` | Relational persistence, geospatial predicates, staged search progression, indexing, caching. | `D-01`, `D-02`, `D-03`, `D-11` (partly), `D-12` |
| `cloud_tco_reviewer` | Deployment approach, hosting, scaling, durable deferred work, and the cost model. | `D-04`, `D-10`, and the cost synthesis |
| `integrations_reviewer` | Auth, email, media/storage, maps/geocoding. | `D-05`, `D-06`, `D-07`, `D-08` |
| `ai_reviewer` | Optional LLM-assisted discovery and future agent readiness, off the critical path. | `D-13` |
| `operations_security_reviewer` | Observability, secrets, backups, CI/CD, deployment safety, privacy/security operations, operator burden. | `D-11`, `D-10` (operations side), `D-14` context |

A separate adversarial critical reviewer then attacks the integrated output. Findings are reconciled in this plan.

- [x] Read the canonical P02 architecture before evaluating any technology.
- [x] Create the P03 branch and the living plan.
- [x] Run the six advisory specialists in parallel.
- [x] Integrate specialist output into one recommended stack; reconcile contradictions explicitly.
- [x] Write `docs/03-technology/technology-evaluation.md`.
- [x] Rewrite `docs/03-technology/cost-model.md` with the three scenarios.
- [x] Rewrite `docs/03-technology/infrastructure-options.md`.
- [x] Rewrite `docs/03-technology/technology-radar.md` as a radar with movement.
- [x] Write `docs/03-technology/ai-evaluation.md`.
- [x] Advance `docs/03-technology/build-vs-buy.md` with the actual decisions.
- [x] Write `PROPOSED` ADRs for the technology choices that warrant them (`ADR-013`–`ADR-019`).
- [x] Record the three `DAVID_DIRECTIVE` decisions in the canonical registers; move `ADR-004` to `ACCEPTED`.
- [x] Apply the four narrow P02 corrections a technology constraint justified.
- [x] Write bilingual `presentation/technology-preview-v0.1.md`.
- [x] Update `docs/01-product/open-questions.md`, `docs/05-roadmap/risks.md`, `presentation/sources.md`.
- [x] Update `plans/README.md`, `README.md`, `CHANGELOG.md`.
- [x] Run adversarial critical review; resolve CRITICAL and HIGH findings.
- [x] Validate against the acceptance criteria; run `git diff --check`.
- [x] Commit `docs: complete Superola P03 technology evaluation`. Do not push.

### Specialist execution note — recorded because it affected coverage

Five of the six work packages were terminated mid-research by a session-level API limit and were **resumed from their transcripts** rather than restarted, with priority ordering supplied so the highest-value research completed first. Two packages additionally lost child research agents. **The consequence is a real and disclosed gap register**, carried in `technology-evaluation.md` §9 and `technology-radar.md`'s `NOT EVALUATED` ring rather than silently filled: the deep relational comparison against MySQL, eleven authentication vendors, several object-storage and mapping candidates, and — **most importantly — password-hash exportability for any authentication vendor, which is the axis on which `ADR-017` is recommended.**

No `NOT VERIFIED` item was filled from memory. That discipline is the reason the gaps are visible.

### Specialist contradictions adjudicated by the primary agent

| Dispute | Resolution |
|---|---|
| Notification vendor: one package recommended the managed provider whose included suppression *is* `D-05`'s "deliverability apparatus"; another recommended the cheaper provider on failure-signal fidelity and body non-retention | **Resolved in favour of the cheaper provider.** `D-05` requires *"hard-failure signals ingested so suppression is derivable"* — Superola must own the suppression list regardless of vendor. A vendor-side list is in fact a **problem** (`R-041`): it is global per address, so a marketing complaint suppresses transactional mail inside the vendor where the purpose-class model has no reach. **Owning the list is what makes purpose-class separation possible at all** |
| Geocoding volume: the brief's own assumption implied vendor autocomplete for customer-side location; one package showed the domain model does not describe that design | **Resolved in favour of the domain model.** The brief was corrected, the ~70–132× difference recorded in `technology-evaluation.md` §1.3, and the rule stated explicitly rather than implied (`R-045`) |
| Module ownership: one package proposed per-module database roles as the enforcement mechanism | **Rejected as over-enforcement.** It would forbid the cross-module read `system-architecture.md` §4 requires. Resolution recorded as `R-037`: schemas for namespacing, cross-module reads permitted, writes and constraints module-scoped, joins prevented in the build |
| Route corridor: one package showed P02's stated reason is technically false while its conclusion is right | **Conclusion upheld, reason corrected.** A corridor *is* a containment predicate; the real costs are the missing route input, the unowned permit dataset, and a two-endpoint query contract |

## Decisions made or ADRs required

Seven ADRs, all `PROPOSED — DAVID APPROVAL REQUIRED`, indexed in `docs/02-architecture/adr/README.md`.

| ADR | Decision | Reversal |
|---|---|---|
| `ADR-013` | JVM/Spring server-rendered single deployable, with build-failing module-boundary enforcement — **P03.1 split this: `ADR-013` is now platform-only and rendering moved to `ADR-020`, deferred to P04** | **VERY HIGH** (platform); LOW–MEDIUM (rendering) |
| `ADR-014` | One PostgreSQL store, schema-per-module, geospatial confined to one predicate | MEDIUM |
| `ADR-015` | Durable deferred work as a transactional outbox in the same store, not a broker | LOW |
| `ADR-016` | Managed platform, single region, no orchestration, no general-purpose infrastructure-as-code | LOW today |
| `ADR-017` | Application-owned authentication, credentials in the marketplace store | Asymmetric |
| `ADR-018` | Zero-egress media storage, pre-generated derivatives, link-out for audio and video | LOW–MEDIUM |
| `ADR-019` | Governed internal `Place` list, geocoding for provider base addresses only — **P03.1 split this into three levels: architecture recommended, vendor on `HOLD` (legal), rendered map moved to P04** | LOW |

**`ADR-004` moved `PROPOSED` → `ACCEPTED`** on an explicit `DAVID_DIRECTIVE`. First accepted ADR in the repository.

Four decisions deliberately **not** written as ADRs, with reasons in the ADR index: the transactional email provider; the observability tier; the audit immutability mechanism (it *implements* `security-privacy-architecture.md` §9 rather than deciding something new); and *"do not spend on a natural-language intent mapper"*, which is a recommendation not to act.

### P02 corrections applied, and the standard they had to meet

The authorization said: *"Do NOT redesign P02 unless a technology constraint proves a P02 assumption materially invalid."* Four edits met that bar. Everything else was recorded as a finding.

| Edit | Justification |
|---|---|
| `system-architecture.md` §4 — notification delivery intent | **A technology constraint proved the rule invalid.** Correct for a broker; with a same-store job table it opens a silent lost-notification window producing **neither** a delivery-failure state nor a non-response state. §3 already required a *"durable intent"*, and an intent that can be lost is not durable. `R-044` |
| ~~`system-architecture.md` §8 — observability row~~ | **ATTEMPTED, THEN WITHDRAWN.** The permission was already present in the row's own justification column, so the conflict was misread — and the edit had additionally inserted a new exclusion that was not in the P02 row. **No technology constraint justified it.** Recorded as a finding |
| ~~`domain-map.md` — Analytics read prohibition~~ | **ATTEMPTED, THEN WITHDRAWN.** The premise was false: the funnel is answered by SQL against the transactional store, not by Analytics reads, so the conflict never arises. Leaving the edit would have left the prose, the forbidden-dependency row and the build-failing rule disagreeing three ways. Recorded as a note in `domain-map.md` |
| `domain-map.md` — two notification lines | Consistency with the §4 correction |

## Open questions and approval gates

All prior open questions remain open. P03 added:

- **`Q-033`** — who owns the de-identified zero-result and abandoned-query corpus, under what lawful basis, with what retention. **Blocks `D-13`'s own prerequisite and is assigned to nobody.** Partly legal.
- **`Q-034`** — the retention period for analytics records, and its owner. **Analytics is 77–87% of database size at every scenario and is the only sizing lever that matters.**
- **`Q-035`** — whether a facet-count results page is in scope, and whether counts must be live. **A page-shape decision currently written as an infrastructure trigger.**

Resolved by `DAVID_DIRECTIVE`: **`Q-028`** (one Account may act in both roles) and **`Q-026`** (operator access is policy-governed, least-privilege, auditable, with a recorded operational purpose).

**Gates, in leverage order, unchanged by P03 except where noted:**

1. **`G-02` / `A-004` — the V1 stopping boundary.** Still the highest-leverage question outstanding. **P03 evaluated no payment technology and no cost line covers it.**
2. **`G-06` / `Q-007` — what "available" promises.** **The availability directive does NOT satisfy this.** It states what V1 builds; `G-06` asks what the platform promises a customer. **Still blocks P04.**
3. **`G-09` plus `Q-025`/`Q-029` — legacy lawful basis.** `D-14` was not evaluated. **Claim adjudication is zero in every operator estimate because no cohort is approved — the single largest swing factor in the cost model.**
4. **`Q-027` before `Q-015`** — the deletion commitment must precede crawler approval. **P03 adds a mechanism consequence (`R-046`): enabling a managed bot-mitigation product would approve `Q-015` by accident, because its verified-bot allowlist is a second, vendor-owned crawler policy.**
5. **`G-04` / `G-05`** — launch cohort, geography and locale. **The region decision is deliberately deferred to this gate**, because naming a region now would settle a product question by infrastructure side effect.
6. **`Q-033`** — new, and it blocks the AI gate from ever opening.

**Two legal readings must be settled before `ADR-019` is adopted:** whether the selected geocoder's termination clause reaches permanently-licensed results, and whether rendering a vendor-derived coordinate on a public page constitutes distribution.

## Validation and acceptance criteria

| Check | Result |
|---|---|
| `D-01`–`D-15` each addressed or explicitly gated. | **PASS.** `D-01`–`D-12` answered in `technology-evaluation.md` §4. `D-13` gated and found **unanswerable during P03**. `D-14` gated on `G-09`, with only store-shape requirements recorded. `D-15` **not evaluated**. `D-06`'s upload-scanning clause was initially missed and was answered on review (§4.6). |
| Exactly one recommended technology architecture. | **PASS.** §3 is one stack; alternatives appear as runner-ups with the axis each lost on, never as parallel stacks. |
| Every price carries a primary official source and an access date. | **PASS after correction.** The first draft deferred citations to work-package records that **do not exist in the repository** — an unfalsifiable cost model and a breach of `AGENTS.md`. A per-line register with vendor, plan, price and URL is now `cost-model.md` §12, alongside an explicit `NOT VERIFIED` table. |
| Three cost scenarios exist, split fixed / usage-variable / human-operational / optional-future. | **PASS.** `cost-model.md` §3, §4, §5, §8, with the unit named on every variable line. |
| TCO considered beyond the infrastructure invoice. | **PASS.** Human cost is 88–96% of total at every scenario; the self-managed comparison is decided on a break-even hourly rate, not a bill. |
| Alternatives and measurable reversal triggers recorded for every material choice. | **PASS with one honest exception.** Every decision carries a trigger except `ADR-013` after first deploy, which is **recorded as an accepted risk rather than dressed as a trigger**, because no observable condition would realistically justify a rewrite at this team size. |
| No payment architecture selected while `G-02` is unresolved. | **PASS.** `D-15` not evaluated; no cost line includes it; stated as an excluded future sensitivity. |
| No dedicated search engine added without trigger evidence. | **PASS.** Staged, with triggers `T1`–`T8` and the V1 instrumentation each requires. `T8` is new in P03 — §11's triggers were all read-side. |
| No Kubernetes, microservices, or event bus introduced. | **PASS.** All rejected with named triggers. The broker rejection was **re-argued on review**: an outbox is how a broker avoids dual-write, so the correct claim is that the broker is strictly *additive*, not that it cannot satisfy the requirement. |
| AI stays optional and off the critical path. | **PASS.** Recommendation is **do not spend**; the gate is closed and unanswerable during P03. |
| Familiarity is never the deciding argument (`R-007`). | **PASS after correction, and this was the closest call.** `ADR-013`'s four "independent grounds" were reduced to **two** on review — two did not discriminate against any candidate, and a third was contradicted by this phase's own `R-036`. The PostgreSQL differentiator list was cut from five to three for the same reason. Both records now state plainly that the alternatives were **not** verified insufficient. |
| Owner-facing bilingual technology preview exists. | **PASS after correction.** Bilingual throughout. Review found one fabricated figure (a seven-day recovery window appearing nowhere else) — removed — and that only volumes, not hourly rates, were disclosed as assumptions. Both fixed. |
| Adversarial critical review passes. | **PASS on the second pass.** The first returned **FAIL**: one CRITICAL (missing citations) and six HIGH. All resolved — see below. |
| `git diff --check` passes. | **PASS.** No whitespace errors. |

### Adversarial review findings and their resolution

The first pass returned **FAIL**. Findings are recorded rather than summarised away, because several are corrections to reasoning rather than to typography.

| Finding | Severity | Resolution |
|---|---|---|
| **Zero primary-source citations existed in five of the six technology documents**, which deferred to work-package records that are not in the repository. The entire cost model was unfalsifiable, breaching `AGENTS.md`'s requirement to persist authoritative sources | **CRITICAL** | Per-line price register added at `cost-model.md` §12 — vendor, plan, price as read, URL, access date — plus an explicit `NOT VERIFIED` table. Vendor anonymisation elsewhere was dropped: it bought nothing, since `technology-radar.md` names them anyway, and cost auditability |
| **The operator-labour model billed a cumulative stock as a monthly flow** on taxonomy exceptions and duplicate resolution — 10,000 providers generating 200 duplicate cases *every month* on a stable base | HIGH | Re-based on new-provider flow. Cut ~26 hours/month at Growth; total cost of ownership moved from ~$16,900 to ~$14,859. **The arithmetic was never wrong; the model was, and it inflated the number the owner is shown** |
| **`ADR-015`'s deciding argument was technically false** — it claimed the same-store queue was "the only mechanism" satisfying the durability requirement, while the transactional outbox is precisely how a *broker* avoids dual-write | HIGH | Re-argued: the outbox table is required either way, so **the broker is strictly additive** at 0.096 jobs/second. The `system-architecture.md` §4 correction itself was judged **justified and stands** |
| **Media moderation was priced in `build-vs-buy.md` and absent from the cost model**, so $190–510/month appeared in no total | HIGH | Added as a flow-driven row in `cost-model.md` §5.2 |
| **Four different Growth cash figures and two geocoding figures circulated**, and a section headed *"arithmetic shown"* showed none | HIGH | Recomputed once and propagated. The geocoding derivations are now written out; the multiple is stated as **~70× against the same vendor, ~132× against the disqualified platform** rather than a single unsourced number |
| **Two of `ADR-013`'s four grounds were not differentiators, and a third was contradicted by `R-036`** | HIGH | Reduced to two grounds, with the withdrawals and the one-sided "first-party or bespoke" standard both stated explicitly |
| **The `domain-map.md` Analytics edit was unnecessary and created a three-way disagreement** it did not fix | HIGH | **Edit withdrawn.** The funnel is answered by SQL against the transactional store, so the conflict never arose. Recorded as a note instead |
| A fabricated seven-day recovery window in the owner preview; rates undisclosed as assumptions; the `system-architecture.md` §8 edit unjustified; `SRC-014` unregistered; `Q-026` half-answered; `D-06` upload scanning unaddressed; a model price with a known 2026-08-31 expiry; the plan reporting `IN PROGRESS` while two registers said `COMPLETED`; the PostgreSQL differentiator list overstated | MEDIUM | All resolved. The §8 edit was **withdrawn**; `SRC-014` registered; `Q-026` marked **PARTLY RESOLVED** with the disclosure limb open; upload scanning answered as *the re-encode is the primary control*; the price expiry footnoted |
| Irreproducible "$37/month… four times" phrasing; a provider-count inconsistency in the geocoding exit estimate; `R-039` and `Q-033` appended out of sequence | LOW | All corrected |

**Findings the reviewer raised and explicitly rejected as non-issues** — recorded so they are not re-litigated: `ADR-004`'s move to `ACCEPTED` is legitimate under the ADR index's own definition; the `system-architecture.md` §4 correction is justified; the core arithmetic across request rates, operator hours, break-even rates and every AI figure was recomputed and confirmed correct; latency figures are adequately disclosed as derivations; the AI recommendation is supported rather than contrarian; and no gate is claimed satisfied that is not.

## Deliverables

**New:** `docs/03-technology/technology-evaluation.md` · `ai-evaluation.md` · `docs/02-architecture/adr/ADR-013`–`ADR-019` · `presentation/technology-preview-v0.1.md` · `plans/P03-technology-evaluation.md`.

**Rewritten:** `docs/03-technology/cost-model.md` · `infrastructure-options.md` · `technology-radar.md` · `build-vs-buy.md`.

**Corrected (narrow, justified):** `docs/02-architecture/system-architecture.md` §4 and §8 · `domain-map.md` (three lines) · `adr/ADR-004.md` (to `ACCEPTED`) · `adr/README.md`.

**Registers updated:** `docs/05-roadmap/risks.md` (`R-033`–`R-049`) · `docs/01-product/open-questions.md` (`Q-033`–`Q-035`; `Q-026` and `Q-028` resolved) · `presentation/sources.md` · `plans/README.md` · `README.md` · `CHANGELOG.md`.

## Handoff and recommended next step

**Recommended: take the two cheap decisions David can make alone, then run P04 — but P04 still needs its own authorization and still needs `G-06`.**

The reasoning:

- **P03 is complete and nothing it produced is adopted.** The next action on the technology track is not more evaluation — it is **David approving or rejecting `ADR-013`–`ADR-019`.** `ADR-013` deserves the most scrutiny, because its reversal difficulty is VERY HIGH and everything else is LOW to MEDIUM.
- **Three verification tasks should precede any purchase**, and each could change a recommendation: password-hash exportability for the runner-up authentication vendor (`ADR-017`); the object-storage provider's custom-domain and content-type seam (`ADR-018`); and the two legal readings on geocoding terms (`ADR-019`). **None is expensive. Each is load-bearing.** *(P03.1 update: the first was performed on 2026-08-12 and **changed the recommendation's status** — hash export is self-service for two of four vendors, so `ADR-017` moved to `HOLD`. The other two remain open.)*
- **P04 is still not authorized, and `G-06` is still the blocker.** The availability directive narrowed what V1 builds; it did not answer what "available" promises a customer, and the gate itself requires that resolved before P04 designs profile and request surfaces. `SRC-004` remains NOT RECEIVED.
- **Owner reconciliation remains the highest-value parallel track**, and `G-02` remains the single question that would most change both the architecture and the cost model.
- **P05 is unblocked in principle but gated in fact.** `D-14` recorded only what the store must provide; the pipeline needs `G-09` and its legal answers. **Note the schema consequence: per-field provenance must be designed into the first migration, because retrofitting field-level origin onto a populated table is unrecoverable.**

**Two things that should happen before any code is written, because they are unrecoverable if missed:** the two-column instant-plus-zone decision (`R-039`), and per-field import provenance. Both are free now and impossible later.

**One thing that should be decided before launch rather than discovered:** analytics partitioning and retention (`Q-034`). It is the only item in the model that is a migration rather than a slider.

Do not treat this plan's completion as authorization for any later phase.
