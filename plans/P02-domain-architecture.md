# P02 — Domain & System Architecture

## Metadata

- ID: P02
- Title: Domain & System Architecture
- Owner: David
- Status: COMPLETED
- Started: 2026-08-11
- Last updated: 2026-08-11
- Authorization: explicit David authorization to start P02 before owner reconciliation completes (`SRC-013`).
- Product baseline carried forward: **Superola V1 — Marketplace Leads (B0 envelope)**, `PROPOSED — OWNER VALIDATION REQUIRED` (`docs/05-roadmap/mvp-scope.md`).
- Architecture status produced by this phase: `PROPOSED`. Nothing here is an accepted architecture.

## Objective

Convert the current product hypothesis into **one recommended logical architecture**: domain boundaries, conceptual entities and lifecycles, interaction and consistency requirements, and structural requirements for search, internationalization, and security/privacy — plus an explicit register of the owner alternatives that would change it.

P02 produces a *design under stated assumptions*, not a validated architecture and not a technology plan.

## Non-goals

- Selecting any technology: no language, framework, cloud, database, search engine, queue, cache, vendor, or product.
- Designing physical database tables, SQL, DDL, indexes, or a persistence schema.
- Designing payment, booking, payout, refund, or dispute operations (the Transaction Extension).
- Implementing or specifying MCP, A2A, A2UI, UCP, ACP, `llms.txt`, or any agent protocol as architecture.
- Creating microservices or any deployment topology from the domain list.
- Deciding the actual legacy migration strategy (P05) or the UX design (P04).
- Producing cost figures, developer-time ranges, delivery dates, or targets.
- Promoting any `ASSUMPTION` or `PROPOSED` item to a requirement or accepted decision.
- Starting P03, P04, P05, P06, or P07.
- Modifying or deleting historical Git stashes.

## Context and authoritative inputs

| Input | Why it is authoritative here |
|---|---|
| `AGENTS.md` | Evidence labels, provenance rules, architecture guardrails, language policy, definition of done. |
| `docs/00-context/product-context.md`, `interview-evidence.md`, `assumptions.md`, `glossary.md`, `source-register.md` | The only record of what is owner-reported versus David-directed versus discovery synthesis. |
| `docs/01-product/feature-inventory.md` | The category-variability table, cross-category invariants, and the V1 trust minimum are the primary domain-modelling inputs. |
| `docs/01-product/user-journeys.md` | The product-state tables are the lifecycle source of truth. |
| `docs/01-product/product-vision.md`, `actors.md`, `monetization.md`, `open-questions.md`, `owner-reconciliation-matrix.md` | Actor outcomes, monetization staging, open decisions, and the P02/P04 release gate. |
| `docs/05-roadmap/mvp-scope.md` | The canonical B0 envelope — the tightest scope statement in the repository. |
| `docs/05-roadmap/risks.md`, `dependencies.md` | `R-001`–`R-022` constrain what P02 may assume. |
| `docs/06-migration/legacy-data-strategy.md` | Legacy work remains a P05 placeholder; P02 may only define a boundary. |
| `docs/07-research/ai-discoverability.md` | Machine-legibility requirements and the explicit anti-inflation rule. |
| `docs/02-architecture/*` (pre-P02 placeholders) | Prior P00 principles and deferred questions that P02 now advances. |
| `docs/03-technology/technology-radar.md` | Records candidate technologies and David's experience as evaluation input **only**; P02 must not treat it as a selection. |

Pending inputs remained pending and nothing in this phase inferred their content: `SRC-003` (feature/monetization document), `SRC-004` (UI/UX materials), `SRC-005` (legacy schema/export), `SRC-006` (analytics/traffic), `SRC-007` (validated pricing assumptions), `SRC-002` (authorized platform audit).

## Known constraints and evidence labels

### The authorization situation — stated plainly

`docs/01-product/owner-reconciliation-matrix.md` defines a P02/P04 release gate whose BLOCKING items (`G-01`–`G-10`) all still depend on owner input that has not been received. That same gate states a BLOCKING item may be satisfied either by an owner decision **or** by "an explicitly documented bounded scenario set that David accepts as the design envelope", meaning at most two named alternatives with their consequences recorded.

David authorized P02 to proceed. The honest accounting, recorded in full in `docs/02-architecture/decision-branches.md`:

| Gate status | Gates |
|---|---|
| Satisfied in substance | `G-02`, `G-03`, `G-08` |
| Partial | `G-01`, `G-04`, `G-05`, `G-07`, `G-09` |
| **Unsatisfied** | `G-06` (availability claim — the envelope is entirely silent), `G-10` (owner material status — `SRC-003`/`SRC-004` NOT RECEIVED) |

Two further limits that must not be glossed:

- **`G-01` and `G-09` are both in the matrix's explicit "do not start" set.** The "readiness test, not an authorization" sentence means the gate does not itself authorize — David must. It does **not** license overriding the do-not-start clause. P02 proceeded on David's explicit authorization, recorded as a decision rather than as gate satisfaction. `decision-branches.md` supplies the two-alternative set for `G-01` that the envelope did not.
- **`G-09`'s permission component cannot be satisfied by any scenario set**, because whether permission exists to contact or migrate legacy records is an owner-plus-counsel fact. P02's mitigation is to make that answer's absence structurally safe.

Consequences carried into every artifact: the envelope is `ASSUMPTION` with provenance `DAVID_DIRECTIVE`; no artifact describes it as a requirement or the architecture as accepted; every artifact stamps the launch scenario it assumes; and **P02 does not authorize P04.**

### Working Decision Envelope

Recorded as `DAVID WORKING ASSUMPTION — OWNER VALIDATION PENDING`; provenance `DAVID_DIRECTIVE`; source `SRC-013`. Items `E1`–`E10` and their reconciliation against the P01 canonical record are in `docs/02-architecture/decision-branches.md`.

Summary of the primary scenario:

1. Preserve and improve the essential marketplace function: customers discover, evaluate, and contact providers; providers respond; both sides can progress toward a real commercial relationship.
2. Working launch geography United States + Mexico, English + Spanish; architecture must permit additional countries and locales without structural redesign. Architecture extensibility is separate from launch operational scope.
3. Broad event-service category growth, not musicians only. Representative archetypes: performers, venues, professional/event services, food, transportation. Shared invariants plus category-specific extensibility; do not pre-model every category.
4. Discovery: customer expresses or searches a need, Superola returns relevant providers, the customer deliberately chooses whom to contact. Natural-language interpretation may later translate intent into structured constraints. The model must never be the source of truth for marketplace eligibility or ranking.
5. One customer-selected provider per request in V1. No automatic broadcast or fan-out. Multi-provider matching is a documented future branch.
6. Phase 1 is Marketplace Leads. The transaction cluster is a potential later extension. P02 designs Phase 1; the extension influences boundaries only where avoiding future structural redesign is reasonable.
7. Legacy and the new platform may coexist during transition; curated cohort over broad migration; full migration is not a V1 prerequisite; P05 decides the actual strategy.
8. Growth before monetization: free provider onboarding initially, subscription once attributable value is observable, sponsored placement later. Architecture must not prevent them and must not carry their operational complexity in Phase 1.
9. Web first, native mobile later, without mobile-specific architecture in the absence of evidence.
10. Internal AI-assisted discovery is optional; external agent actions are future. Preserve machine-legible domain semantics and clean application boundaries without protocol-specific services.

**Terminology correction P02 applied to the brief's own framing.** The brief named the transaction cluster "V1 — Phase 2". P02 renamed it throughout to **Transaction Extension — `FUTURE`, separately owner-approved**, because a scheduled-phase name silently resolves `A-004` ("booking and payment processing belong in the first production release"), which is OPEN. The brief's own §16 requires that this scope not contaminate Phase 1, so the rename serves its stated intent.

### Standing constraints

- `A-006` (a universal availability model across categories) is `SUPERSEDED` and rejected. **P02 states the positive replacement explicitly: `V1 has no availability model.`** "Accepting requests" is not a date or resource promise.
- Messaging is separate from notification delivery; organic ranking is separate from paid placement (`AGENTS.md`).
- Imported legacy records must remain traceable to source and separable until approved, and cannot receive requests before lawful-use, reachability, control, and publication gates pass (`docs/05-roadmap/mvp-scope.md`).
- `mvp-scope.md` specifies one owner-approved geography and one production locale for the costing and learning baseline. The envelope's two countries and two languages are an *architecture extensibility* requirement, not an approved launch scope. Named scenarios `S-1` and `S-2` keep these separate, and every artifact stamps which it assumes.
- No fabricated cost figures, developer-time ranges, delivery dates, or success targets. **No numeric non-functional-requirements table was produced, deliberately.**
- The legacy platform is referred to generically. The WordPress/MyListing attribution remains `ASSUMPTION` with an unavailable source (`SRC-009`) and is not stated as fact.
- Internal canonical documentation is English only. Bilingual Spanish + English applies only to the owner-facing architecture preview.

## Tasks and delegation strategy

Five advisory read-only specialists ran in parallel; the primary agent was the only canonical writer and reconciled all contradictions.

| Specialist | Mandate |
|---|---|
| `product_constraint_reviewer` | Challenge the envelope against P01 evidence; assess the release gate honestly; find silent-promotion risks. |
| `solution_architect` | Propose domain boundaries, dependency direction, transactional boundaries, consistency requirements, extension points, and deployment shape. No technology. |
| `domain_data_architect` | Conceptual entities, required distinctions, category-extensibility comparison, geography model, and lifecycle state machines. No physical schema. |
| `security_privacy_reviewer` | Structural identity, ownership, claim, contact-disclosure, publication, provenance, audit, and moderation constraints proportional to P01 scope. No vendors. |
| `cost_complexity_reviewer` | Attack disproportionate complexity before it is designed in; supply measurable reconsideration triggers. Direction and drivers only. |

A separate adversarial critical reviewer then attacked the integrated output. Its findings are reconciled below.

- [x] Read the canonical `main` baseline before writing anything.
- [x] Create the P02 branch and the living plan.
- [x] Run the five advisory specialists in parallel.
- [x] Integrate specialist output into one canonical position; reconcile contradictions explicitly.
- [x] Write `docs/02-architecture/domain-map.md`.
- [x] Write `docs/02-architecture/domain-model.md` including lifecycle diagrams.
- [x] Write `docs/02-architecture/system-architecture.md` including the interaction and consistency map.
- [x] Write `docs/02-architecture/search-architecture-requirements.md`.
- [x] Write `docs/02-architecture/internationalization-architecture.md`.
- [x] Write `docs/02-architecture/security-privacy-architecture.md`.
- [x] Write `docs/02-architecture/decision-branches.md`.
- [x] Write `PROPOSED` ADRs for decisions P02 actually recommends.
- [x] Write `docs/02-architecture/p03-decision-inputs.md`.
- [x] Write bilingual `presentation/architecture-preview-v0.1.md`.
- [x] Advance the three pre-P02 architecture placeholders so they do not contradict P02.
- [x] Update `docs/00-context/assumptions.md`, `source-register.md`, `glossary.md`, `docs/01-product/open-questions.md`, `docs/05-roadmap/risks.md`, `dependencies.md`, `presentation/sources.md`.
- [x] Update `plans/README.md`, `README.md`, `CHANGELOG.md`.
- [x] Run adversarial critical review; reconcile findings in this plan.
- [x] Validate against the acceptance criteria; run `git diff --check`.
- [x] Commit `docs: define Superola P02 domain architecture`. Do not push.

### Specialist contradictions adjudicated by the primary agent

| Dispute | Resolution |
|---|---|
| A separate Public Projection boundary? | **No module.** Provider owns the per-field publication policy and its version; the projection is allowlist-derived, versioned, and rebuildable. The security review's substantive requirements survive as invariants. |
| A separate Legacy Import boundary? | **Yes**, reversing an earlier primary-agent position. Two specialists independently argued that an imported record is Superola asserting a business *may* exist while a profile is a business asserting itself — and collapsing them creates a live accidental-publication path. Claim binds ownership without publishing. |
| Where does `BusinessMembership` live? | **Provider.** A business-scoped relation belongs with `Business`; the security review's substance (revocable relation, actor attribution) holds either way. |
| Where does `Block` live? | **Identity & Access** for user-initiated blocks; operator-imposed actor restriction is a separate state applied by governed command from Operations. |
| Does Audit live in Analytics? | **No.** Analytics is write-only and lossy-tolerant; audit must be complete and must not be deletable by the domain-deletion path. |
| Is Discovery's read model a separate store? | **No store in V1.** Ownership (Discovery) and storage (source truth) are separate questions; the cost reviewer's rejection and the solution architect's "projection is a contract, not infrastructure" reconcile without conflict. |

## Decisions made or ADRs required

Twelve ADRs, all `PROPOSED`, indexed in `docs/02-architecture/adr/README.md`.

| ADR | Decision |
|---|---|
| `ADR-001` | Single deployable modular application with module-owned data; mechanical boundary enforcement is inseparable from the decision. |
| `ADR-002` | `Business` and `ProviderProfile` are distinct aggregates within one Provider context. |
| `ADR-003` | Demand owns `RequestDraft`, `ServiceRequest`, and `ProviderResponse`; the offer is an entity inside the request aggregate; the recipient is a single reference, deliberately not a collection of one. |
| `ADR-004` | An Account is not typed by marketplace role; customer and provider are roles. |
| `ADR-005` | `V1 has no availability model`; `RequestIntake` is an intake state; the decline reason is the free instrument that answers the availability question from Phase-1 data. |
| `ADR-006` | Discovery owns eligibility, organic ordering, and placement provenance; no separate search store in V1. |
| `ADR-007` | Shared core plus typed category archetypes, with governed metadata confined to attributes. |
| `ADR-008` | Monetization is a reserved policy seam, not a V1 module; the eligibility ordering invariant is locked. |
| `ADR-009` | Legacy records are a distinct non-discoverable boundary; no runtime coupling to the legacy platform. |
| `ADR-010` | Contact data is resolved through a disclosure decision, never stored on a request, message, or notification. |
| `ADR-011` | One application layer is the single write path; authorization is decided in the domain. |
| `ADR-012` | The public projection is allowlist-derived and versioned; machine access defaults to deny. |

Four decisions were deliberately **not** written as ADRs, with reasons recorded in the ADR index — most notably "no event bus in V1", because the absence of a mechanism is not a decision worth an ADR.

Non-ADR decisions recorded in this plan: the "V1 Phase 2" rename; the `S-1`/`S-2` scenario split; the six specialist adjudications above; and running the category-variability comparison despite the envelope pre-selecting an answer.

## Open questions and approval gates

All P01 open questions remain open. P02 added:

- **Assumptions `A-015`–`A-025`** — two locales at launch, simultaneous two-country launch, broad archetype cohort, the category-model choice, legacy coexistence, the "future-proofing is free" premise, one verification mechanism for both sides, legacy contact data belonging to natural persons, single-owner businesses without an accountability hole, minimal notification bodies retaining utility, and unknown-jurisdiction handling.
- **Questions `Q-019`–`Q-032`** — coexistence identity and URL ownership, multilingual content authorship, `Market` as one governed concept, Transaction Extension seam cost, contact versus request as initiation paths, abuse-evidence and suppression retention (legal), claim evidence bar (partly legal), operator read auditability, deletion commitment for uncontrolled surfaces, dual-role accounts, jurisdiction determination (partly legal), relay notice obligations (legal), `DemandWatch`, and what may be concluded from provider silence.
- **Risks `R-023`–`R-032`.**

Approval gates that remain, in leverage order:

1. **`G-02` / `A-004` — the V1 stopping boundary.** The highest-leverage question outstanding. Several P02 boundaries move together on this single answer, and David cannot decide it.
2. **`G-06` / `Q-007` — what "available" promises.** UNSATISFIED and unaddressed by the envelope. **Blocks P04.**
3. **`G-09` plus `Q-025`/`Q-029` — legacy lawful basis.** Needs the owner and qualified counsel.
4. `G-04` / `G-05` — launch cohort, geography, and locale.
5. `Q-028` and `Q-026` — dual-role accounts and operator read auditability. **Both are David's to settle, both are low cost, and both are structural.**
6. `Q-027` before `Q-015` — the deletion commitment must precede crawler approval.

## Validation and acceptance criteria

| Check | Result |
|---|---|
| Working assumptions explicitly distinguished from owner-confirmed evidence. | **PASS.** The standing qualifier appears in the header of all eight new architecture documents plus the three advanced placeholders. `A-015`–`A-025` and `R-023`–`R-032` carry evidence status and provenance. Gate status is stated, not implied. |
| Exactly one primary architecture exists. | **PASS.** `ADR-001` recommends one shape; `system-architecture.md` §2 gives one recommendation with three rejected alternatives; `decision-branches.md` states explicitly that it is not a set of parallel architectures. |
| Alternatives are decision branches, not parallel architectures. | **PASS.** Fourteen branches, each carrying all twelve required fields including what would and would not change. |
| No technology stack, cloud vendor, database product, or payment vendor selected. | **PASS.** The only product names in P02 output are negative ("not Elasticsearch, not OpenSearch"; "avoid premature Kubernetes", carried from P00). `p03-decision-inputs.md` names categories only. |
| No microservice requirement invented. | **PASS.** `ADR-001` rejects distribution against a five-row evidence table answering "No" four times, with seven measurable flip triggers. |
| No full legacy migration assumed. | **PASS.** `DB-03` makes fresh onboarding the sole V1 baseline; `ADR-009` states the boundary is empty if no cohort is approved; the "no migration" alternative is explicitly kept live. |
| No AI protocol introduced as architecture. | **PASS.** `integration-architecture.md` lists every named protocol as deliberately absent with a trigger; no module may import the experiment. |
| Transaction/payment scope does not contaminate Phase 1. | **PASS after correction.** The rename is complete, including in this plan (a critical-review finding). The entity relationship overview contains no booking, payment, or review node. The payment box was removed from the layer diagram. |
| Internal canonical docs English only; owner-facing preview bilingual. | **PASS.** No Spanish in `docs/` or `plans/` beyond domain proper nouns. The preview is bilingual throughout, including its H1 after correction. |
| P03–P07 remain NOT STARTED. | **PASS.** `plans/README.md` rows unchanged for P03–P07; no plan file exists for any of them; `p03-decision-inputs.md` states it is an input register, not authorization. The P04 non-transfer is stated in six places. |
| Adversarial critical review reconciled. | **PASS.** See below. |
| `git diff --check`. | **PASS** — no whitespace errors. |

### Critical review findings reconciled

The first adversarial pass returned **FAIL** on two CRITICAL findings plus 21 others. All are resolved.

| Finding | Severity | Resolution |
|---|---|---|
| The plan claimed `IN PROGRESS` with unticked tasks, twelve `PENDING` validation rows, and four `PLANS.md`-required sections left as stubs, while three other documents declared the phase COMPLETED. | CRITICAL | This plan rewritten: all sections filled, tasks ticked, every validation row carries a result and its evidence. |
| P02 gave two mutually exclusive answers to how the V1 search read path is fed — "a query over the single source of truth" and "never from source records directly" — with the cost case and the primary privacy guarantee resting on opposite readings. | CRITICAL | Resolved to one statement: the query reads source records directly, **with the publication allowlist applied as a predicate inside that query**. The requirement is allowlist enforcement, not avoidance of source reads. Corrected in `search-architecture-requirements.md`, `ADR-006` (decision, security section, and Option A body), `ADR-012` (decision and validation), and `security-privacy-architecture.md` §12. |
| `decision-branches.md` asserted that no P02 artifact uses "V1 Phase 2" — but this plan used it three times. | HIGH | The plan now uses `Transaction Extension` throughout, making the claim true. |
| The `SRC-013` register entry asserted the envelope satisfies the gate's alternative clause, more broadly than P02's own gate table supports; and `G-01` had no alternative set at all despite being in the "do not start" set. | HIGH | `SRC-013` qualified per gate. `decision-branches.md` supplies `G-01`'s two-alternative set with consequences, and states plainly that `G-09`'s permission component cannot be satisfied by any scenario set and that P02 proceeded on David's authorization rather than gate satisfaction. |
| The "no diagram box" rule was falsified by P02's own layer, domain-map, and preview diagrams. | HIGH | Rule scoped to entity and domain-context diagrams, with §1.13's entity overview as the test. The payment and model-provider boxes were also deleted from the layer diagram, since an "External systems — NONE SELECTED" box gains nothing from naming them. |
| The data classification allowlist put precise provider base location in "audit only", which would make every mobile performer permanently `undetermined` in discovery — the modal launch archetype. | HIGH | Classification now permits the **eligibility computation** to read it while still forbidding emission. Classification must permit the computation it depends on; only the output is restricted. |
| The layer diagram's six inter-module edges contradicted the domain map's full graph, including omitting all domain-to-audit writes. | HIGH | The layer diagram now states that inter-module dependency direction is authoritative in `domain-map.md` and that absence of an edge there is not a prohibition. |
| Eleven ADRs omitted the template's "approval still required" clause. | HIGH | Added to all eleven, naming the outstanding approver. |
| `ADR-003` and `ADR-006` gave the recommended Option A a bare heading with no body, so only the rejected options were argued. | HIGH | Option A bodies written in both. |
| The category-variability comparison ran with its charter's stated precondition (launch cohort selected) unmet. | MEDIUM | Sequence breach stated openly in `domain-model.md` §3 and `ADR-007`. |
| "About seven archetypes" was an invented figure, and it had been promoted to a reconsideration trigger. | MEDIUM | Removed everywhere and replaced with an evidence-derived condition. It was the only unsourced number in P02's output. |
| `Market` was defined as Category-**or**-Archetype at an unstated granularity, which would double-count the liquidity it exists to measure. | MEDIUM | Fixed to Category × Place at a stated granularity, with an archetype rollup defined as a derived aggregate over markets. |
| Suppression propagation was called the only near-realtime requirement but had no stated target. | MEDIUM | The anonymous-public-read cache is now named in the consistency map and in principle P20. |
| The preview was 1–2 slides in the brief but a multi-page document in fact, and asked the owner three questions where seven gates are open. | MEDIUM | A "how to use" note names the two sections that are the 1–2 slides. Two owner asks added, including `G-10`, which is the cheapest question available and unblocks P04. |
| Distinction range stated as 2.9–2.20 while the table runs to 2.23, leaving three distinctions without provenance. | MEDIUM | Corrected. |
| The plan stated "legacy WordPress" as fact against `SRC-009`'s unverified status. | MEDIUM | The plan now refers to the legacy platform generically. |
| `ADR-011`'s security section read "No write path exists in V1", negating its own title. | MEDIUM | Narrowed to "no external or agent write path". |
| Six LOW findings: truncated ADR index title, Discovery owning "result freshness metadata" that cannot exist, `Membership` versus `BusinessMembership`, Audit inside a subgraph titled "domain modules", preview H1 and emphasis asymmetry, a dropped tilde in a Mermaid label. | LOW | All corrected. |

### Merits findings reconciled

The review also attacked the architecture on the merits, independent of process. Four corrections resulted.

| Finding | Resolution |
|---|---|
| **`RequestIntake` decay was a ranking input**, which reproduces exactly the failure mode `DB-10` rejects date-filtered search for: a system clock silently suppresses supply for a reason the provider never learns. | **`unconfirmed` may not be a ranking input in V1.** Eligibility surfaces it; ranking may not use it until the share of impressions and requests lost to it is a measured metric with an accepted bound. The decay window length is an owner and product decision, not a system default. |
| **`publicationGateMet` required only a "resolvable" base location**, while radius eligibility needs precision — so a mobile performer could be published and permanently unmatchable. | The gate is now **archetype-aware**: precision must be sufficient for the eligibility predicate that offering's archetype actually uses. |
| **The non-durable `SearchIntent` model shipped unratified**, despite `domain-model.md` §11 saying to decide first, and `Q-031` was absent from the open-questions table. | `Q-031` added to `architecture-overview.md`'s open-questions table with the trade stated: a durable-never-delivered watch enforces "a search is never a broadcast" just as well while preserving the strongest cold-start mechanism. |
| **Ten module boundaries is the durable decision by P02's own argument, yet no ADR compares the boundary count** — and ten boundaries is ten times the enforcement surface of three. | `ADR-001` now carries a scope note stating that boundary count was fixed by domain analysis rather than treated as a cost variable, naming the three thin modules, and directing P03 to revisit the **boundary set** first if enforcement cost scales with count. |

Two findings were rejected as unsupported after checking: there is no freshness contradiction between the consistency map and the search requirements (both say read-your-writes), and the legacy-boundary reversal is complete and consistent across all six places it appears.

## Deliverables

**New:** `docs/02-architecture/domain-map.md` · `domain-model.md` · `system-architecture.md` · `search-architecture-requirements.md` · `internationalization-architecture.md` · `security-privacy-architecture.md` · `decision-branches.md` · `p03-decision-inputs.md` · `adr/ADR-001`–`ADR-012` · `presentation/architecture-preview-v0.1.md` · `plans/P02-domain-architecture.md`.

**Advanced:** `docs/02-architecture/architecture-overview.md` (now an index and principle record with the P00-to-P02 principle revision traceable) · `data-architecture.md` · `integration-architecture.md` · `adr/README.md`.

**Registers updated:** `docs/00-context/assumptions.md` (`A-015`–`A-025`) · `source-register.md` (`SRC-013`) · `glossary.md` (substantial terminology revision) · `docs/01-product/open-questions.md` (`Q-019`–`Q-032`) · `docs/05-roadmap/risks.md` (`R-023`–`R-032`) · `dependencies.md` · `presentation/sources.md` · `plans/README.md` · `README.md` · `CHANGELOG.md`.

## Handoff and recommended next step

**Recommended: run P04 UX Architecture and P03 Technology Evaluation in parallel — but only after two cheap decisions, and P04 needs its own authorization.**

The reasoning:

- **P03 is ready now.** `p03-decision-inputs.md` gives it fifteen bounded decision problems with evaluation criteria. Three are additionally gated (`D-13` needs the zero-result corpus; `D-14` needs `G-09`; `D-15` must not be touched while `G-02` is open), and the other twelve can proceed.
- **P04 is not ready and P02 does not authorize it.** `G-06` — what "available" promises a customer — is unsatisfied, unaddressed by the envelope, and the gate itself requires it resolved before P04 designs profile and request surfaces. `SRC-004` is NOT RECEIVED.
- **Owner reconciliation is not a blocker for P03**, but it is the highest-value parallel track, and `G-02` is the single question that would most change the architecture.

Two decisions David can make immediately, without the owner, both low cost and both structural: `Q-028` (may one account act in both roles — P02 recommends yes) and `Q-026` (is operator access to customer-private content auditable and disclosed).

Do not treat this plan's completion as authorization for any later phase.
