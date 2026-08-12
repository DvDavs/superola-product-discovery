# Cost Alternatives — why the modelled bill is what it is

> **Status:** `PROPOSED — DAVID APPROVAL REQUIRED`. Provenance: `TECHNICAL_DISCOVERY`. **Scenario stamp: S-1.**
>
> **Scope:** this document explains **why** each cost decision was taken and **what would make a different option win.** It is not a second cost model. **`cost-model.md` is the single arithmetic source of truth.** Every figure here is reproduced from it or derived from its §2.2 volumes; no total is recomputed independently, so the two documents cannot diverge into two answers.
>
> **Currency: USD.** Every `$` in this document is US dollars. No MXN conversion is given — exchange rates are time-sensitive, they change no comparison here, and they would add a second thing to re-verify.
>
> **Illustrative planning scenario — not a forecast.** Every volume in `cost-model.md` §2.2 is a stated `ASSUMPTION`. `SRC-006` (analytics and traffic) is NOT RECEIVED; there is no traffic evidence at all. The owner-reported ~43,000 legacy registrations are unaudited and size nothing.

## 1. Scenario anatomy — where the cash bill actually goes

Three technology cash bills, from `cost-model.md` §1. **These totals reconcile exactly with §3 (fixed) plus §4 (usage-variable) of that document.**

| | Pilot / Validation | Early Marketplace | Growth |
|---|---|---|---|
| Fixed monthly platform (`cost-model.md` §3) | USD $75.30 | USD $187.05 | USD $505.20 |
| Usage-variable (`cost-model.md` §4) | USD $0.68 | USD $5.94 | USD $55.59 |
| **TECHNOLOGY CASH BILL** | **USD $75.98** | **USD $192.99** | **USD $560.79** |
| Published providers assumed | 150 | 1,500 | 10,000 |

### 1.1 Pilot — USD ~$76/month

| Category | Monthly | Share | Fixed or variable |
|---|---|---|---|
| Application containers (web USD $25.00 + worker USD $10.00) | USD $35.00 | 46% | fixed |
| Non-production environment | USD $25.15 | 33% | fixed |
| Managed PostgreSQL | USD $15.15 | 20% | fixed |
| Transactional email (3,000 sends) | USD $0.30 | 0.4% | variable |
| Geocoding (~55 provider addresses) | USD $0.28 | 0.4% | variable |
| Independent off-platform backup | USD $0.10 | 0.1% | variable |
| Media storage and egress | USD $0.00 | 0% | variable |
| Delivery layer · error tracking · logs · pipeline | USD $0.00 | 0% | fixed, free tiers |
| **TOTAL** | **USD $75.98** | **100%** | |

**The finding at Pilot: two thirds of the bill is compute you must have and a non-production environment you must have.** Nothing usage-driven is material. **The single largest optimisation available at Pilot is deleting the non-production environment — and it is refused**, because it is where migration rehearsal happens and `R-047`'s expand/contract discipline is the thing that makes rollback real.

### 1.2 Early Marketplace — USD ~$193/month

| Category | Monthly | Share | Fixed or variable |
|---|---|---|---|
| Application containers (2 web instances USD $50.00 + worker USD $25.00) | USD $75.00 | 39% | fixed |
| Managed PostgreSQL | USD $60.90 | 32% | fixed |
| Error tracking (leaves free tier) | USD $26.00 | 13% | fixed |
| Non-production environment | USD $25.15 | 13% | fixed |
| Transactional email (35,000 sends) | USD $3.50 | 1.8% | variable |
| Geocoding (~330 provider addresses) | USD $1.65 | 0.9% | variable |
| Independent off-platform backup | USD $0.50 | 0.3% | variable |
| Media storage (29.4 GB stored, 250 GB served) | USD $0.29 | 0.2% | variable |
| Delivery layer · logs · pipeline | USD $0.00 | 0% | fixed, free tiers |
| **TOTAL** | **USD $192.99** | **100%** | |

**The finding at Early: the second web instance is bought for availability during deploys, not for load.** At under ten requests per second peak at *Growth*, load is not the reason. **250 GB of media served costs USD $0.00 in egress** — on a $0.09/GB host it would be USD $22.50, more than the entire email and geocoding lines combined.

### 1.3 Growth — USD ~$561/month

| Category | Monthly | Share | Fixed or variable |
|---|---|---|---|
| Managed PostgreSQL (primary USD $122.10 + standby USD $122.10) | USD $244.20 | 44% | fixed |
| Application containers (2 larger web USD $100.00 + worker USD $25.00) | USD $125.00 | 22% | fixed |
| Observability (error tracking USD $26.00 + log storage USD $25.00) | USD $51.00 | 9% | fixed |
| Non-production environment | USD $40.00 | 7% | fixed |
| Transactional email (250,000 sends USD $25.00 + dedicated IP USD $15.00) | USD $40.00 | 7% | variable |
| Delivery layer (paid tier) | USD $25.00 | 4% | fixed |
| Pipeline minutes | USD $20.00 | 4% | fixed, estimate |
| Geocoding (permanent USD $6.60 + autocomplete USD $2.46) | USD $9.06 | 1.6% | variable |
| Media storage (245 GB stored, **2,500 GB served**) | USD $3.53 | 0.6% | variable |
| Independent off-platform backup | USD $3.00 | 0.5% | variable |
| **TOTAL** | **USD $560.79** | **100%** | |

**The finding at Growth: 44% of the bill is the database, and half of that is the standby node.** The standby price is `NOT VERIFIED` and is modelled at the primary rate — it is the largest unverified figure in the model.

**And the finding that matters more: storing 245 GB costs USD $3.53, and serving 2,500 GB on top of it costs USD $0.00.** Egress is 10× the stored volume and is billed at nothing. That is not a discount; it is a structural property of the selected storage, and §6 below shows what it replaced.

## 2. Hosting — managed PaaS vs self-managed VPS vs major cloud

| | **Selected: managed developer platform** | Runner-up: self-managed VPS | Deferred: major cloud |
|---|---|---|---|
| Fixed monthly direction | Higher invoice | **Lowest invoice** | Highest floor |
| Variable unit | per-instance tier; pooled egress allowance | per-droplet; per-TB transfer | per-hour on every supporting resource |
| Human/operator effect | 3.25 → 5 → 9 h/month | **+6.5 → +9 → +15 h/month** on top of that | not modelled |
| Cash at Pilot | USD $75.98 | ~USD $25.98 | `NOT VERIFIED` |

### The break-even, which is the whole argument

| | Cash saved by self-managing | Extra operator hours/month | **Break-even hourly rate** |
|---|---|---|---|
| Pilot | ~USD $50 | 6.5 | **~USD $7.7/h** |
| Early Marketplace | ~USD $36 | 9.0 | **~USD $4.0/h** |
| Growth | ~USD $133 | 15.0 | **~USD $8.9/h** |

*The savings column is rounded; `cost-model.md` §6 states the break-even to two decimals from the unrounded figures. **Do not re-derive the rate from the rounded saving** — it lands a few cents out, which is a rounding artefact and not a second answer.*

**Why the lowest invoice is not the lowest total cost.** At Pilot the two configurations compare like this (`cost-model.md` §6):

| | Cash | Infra-ops hours | At USD $75/h | **Illustrative total** |
|---|---|---|---|---|
| **Managed (selected)** | USD $75.98 | 3.25 | USD $243.75 | **USD $319.73** |
| Self-managed | USD $25.98 | 9.75 | USD $731.25 | **USD $757.23** |

**The self-managed configuration bills a third as much and costs 2.4× as much** — *at the assumed USD $75/hour rate, which is an assumption and not owner-validated.* The rate-independent form is the break-even table above: **self-managing wins only if the operator's hour is worth under about USD $8.**

**Why selected:** for USD $36–133/month the managed platform supplies exactly the capabilities whose absence costs 6.5–15 hours — point-in-time recovery, rolling deploys with health checks, a pre-deploy migration step, private networking, per-process resource limits from one artifact, automatic certificates.

**What would make another option win.** Self-managed wins on all three of: managed bill above USD $500/month, **and** a person whose allocated role includes infrastructure operations, **and** database high availability either unnecessary or that person's demonstrated skill. *At Growth the first condition is met and the other two are not.* Major cloud wins if a genuinely required managed service exists only there, or a residency obligation names a region only available there, or its bill — **including** load-balancer hours, gateway hours and per-GB, address hours, log ingestion and support-plan minimum — is verified lower. **None of those supporting-cast prices was confirmed from an official page, so the option is deferred, not costed.**

## 3. Database — PostgreSQL vs MySQL

| | **Selected: PostgreSQL 18** | Runner-up: MySQL 8.4 LTS |
|---|---|---|
| Fixed monthly direction | USD $15.15 → $60.90 → $122.10 (+ standby at Growth) | **`NOT VERIFIED`** |
| Variable unit | none — instance-tier priced | none — instance-tier priced |
| Human/operator effect | no DBA; annual major-version review | comparable in kind; not measured |

**No price difference is claimed, because none was verified.** Managed MySQL prices at the selected host were not read from an official page during P03, and **nothing here is estimated from memory.** Both are instance-tier products from the same category of managed service; **it would be dishonest to present a price advantage that was never checked**, and the decision does not rest on one.

**Why selected — three capabilities consumed by named P02 requirements**, not admired in the abstract: partial indexes carrying the publication allowlist predicate inside the index; in-core Spanish and English full-text search with accent folding and trigram similarity, **which is the entire reason no search engine is bought** (§4); and a variable-depth tree extension for the `Place` parent chain.

**The honesty that must survive summarisation:** **PostgreSQL was verified sufficient. MySQL was NOT verified insufficient.** The differentiators above were not checked against it before research was exhausted. Two further claimed differentiators were struck on adversarial review because the runner-up has them too, and a third — array containment indexes for `ServiceAreaDeclaration` semantics — remains **arguable and unresolved.**

**Reversal implication, which is the real cost axis.** Exit is a stock dump at Pilot — a maintenance hour. **Above roughly 100 GB it needs logical replication and becomes a project.** The expensive parts of any later move are the full-text work if staged search has shipped, the geospatial column, and the partial indexes — **which is exactly why the geospatial dependency is confined to one predicate of four.** The store-specific surface is small and named on purpose.

**What would make MySQL win:** the deep comparison is completed and it matches on partial indexes and array containment. **That reopens `D-01` and the recommendation should be re-run rather than defended.**

## 4. Search — PostgreSQL staged search vs a dedicated managed engine

| | **Selected: staged, in-database** | Alternative A: usage-priced managed engine | Alternative B: serverless search cluster |
|---|---|---|---|
| Fixed monthly direction | **USD $0 incremental** | USD $0 | **USD $350.40/month floor from day one** |
| Variable unit | none | USD $0.50 per 1,000 searches, first 10,000 free | USD $0.24 per compute-unit-hour, 2-unit minimum |
| Human/operator effect | none new | reindex on every taxonomy change; a divergence support queue | same, plus cluster operations |

**Current incremental cost, at `cost-model.md` §2.2 search volumes:**

| Searches/month | Selected | Managed engine |
|---|---|---|
| Pilot — 8,000 | USD $0.00 | USD $0.00 *(inside the 10,000 free allowance)* |
| Early — 80,000 | USD $0.00 | ~USD $35.00 |
| Growth — 600,000 | USD $0.00 | ~USD $295.00 |

**Why selected:** `search-architecture-requirements.md` §11 names bilingual free-text relevance with per-language analysis and typo tolerance as *"the only real candidate"* for dedicated infrastructure — and the store supplies a credible version of exactly that in core. §6 adds that ranking quality at launch *"is bounded by data, not by engine capability"*: zero clicks, zero conversions, zero reviews. **Buying an engine buys ranking machinery with no signal to rank on.**

**The dollar figure is the small part of this decision.** The real costs of a separate engine are a full reindex on every taxonomy change — and `R-010` guarantees taxonomy change — a permanent divergence support queue, two sources of truth, and a new *published but not yet discoverable* product state that does not currently exist.

**What would make an engine win:** the numbered promotion triggers `T1`–`T8` in `technology-evaluation.md` §4.3, each measured in V1. **Note where the trigger will actually come from:** at Growth the discovery working set is ~395 MB and fits in memory, **so latency is unlikely to fire first — facet-count cardinality will**, and that is a page-shape decision (`Q-035`), not an infrastructure one.

## 5. Email — AWS SES vs Postmark

| | **Selected: AWS SES** | Runner-up: Postmark |
|---|---|---|
| Fixed monthly direction | USD $0 base; USD $15.00/month optional dedicated IP at Growth | USD $15.00 (Basic) / USD $16.50 (Pro) base at 10,000/month |
| Variable unit | **USD $0.10 per 1,000 emails** | overage **USD $1.80 per 1,000** (Basic) / **USD $1.30 per 1,000** (Pro) |
| Human/operator effect | Superola owns suppression, bounce ingestion, warm-up | vendor-managed suppression included |

**Unit-cost difference at the modelled volumes:**

| Emails/month | Selected (SES) | Postmark Basic | Postmark Pro |
|---|---|---|---|
| Pilot — 3,000 | USD $0.30 | USD $15.00 (base) | USD $16.50 (base) |
| Early — 35,000 | USD $3.50 | ~USD $60.00 | ~USD $49.00 |
| Growth — 250,000 | USD $25.00 (+ USD $15.00 IP) | *extrapolated ~USD $447* | *extrapolated ~USD $329* |

> **The Growth column for the runner-up is an extrapolation of published overage rates from the 10,000 base, not a quoted plan.** Postmark publishes higher-volume plans that were **NOT VERIFIED** during P03. It is shown to give the order of magnitude — **13–18× the selected option** — and must not be treated as a quote.

**Why selected, and the reason is not the ~13× unit price.** `D-05` requires *"hard-failure signals ingested so suppression is derivable"* — **Superola must own the suppression list regardless of vendor.** A vendor-side list is in fact a **problem** (`R-041`): it is global per address, so a marketing complaint suppresses transactional mail inside the vendor, where Superola's purpose-class model has no reach. **Owning the list is what makes purpose-class separation possible at all**, which means the runner-up's flagship inclusion is a feature Superola would have to work around.

**Operational and deliverability implication, stated plainly:** the selected option costs more setup work and puts sender reputation on Superola. **Exit either way is 2–4 days of code and a 2–4 week reputation warm-up — the warm-up is the real switching cost, not the code.** And one consequence is structural and un-retrofittable after reputation damage: **separate configuration sets and separate sending subdomains per consent purpose class, from day one** (`R-041`).

**What would make the runner-up win:** measured deliverability materially worse on the selected provider after warm-up, or an operator-time cost for bounce/complaint handling that exceeds the price gap — **which at Growth means more than ~7 hours/month at the assumed USD $40/h operator rate.** That is a large number, and it is why the decision is not close on operational grounds either.

## 6. Media — zero-egress storage vs metered-egress alternatives

**This is the single largest cost decision in the model, and it is a decision about a unit, not a vendor.**

| | **Selected: zero-egress object storage** | Metered-egress alternatives |
|---|---|---|
| Fixed monthly direction | USD $0 | USD $0 |
| Variable unit — storage | **USD $0.015 per GB-month**, first 10 GB free | comparable |
| Variable unit — **egress** | **USD $0.00 per GB** | USD $0.02 / $0.09 / $0.15 per GB depending on host |
| Human/operator effect | none new | none new |

### Growth, 2,500 GB served per month — the illustration that decides it

| Served from | Storage (245 GB) | Egress (2,500 GB) | **Monthly total** |
|---|---|---|---|
| **Zero-egress object storage** ✅ | USD $3.53 | **USD $0.00** | **USD $3.53** |
| Application origin at USD $0.02/GB | — | USD $50.00 | USD $50.00 |
| A host charging USD $0.09/GB | — | USD $225.00 | USD $225.00 |
| A host charging USD $0.15/GB | — | USD $375.00 | **USD $375.00 — more than all compute combined** |

**Why egress dominates storage.** Stored bytes accumulate once and are billed once per month: 245 GB. **Served bytes are billed every time somebody looks**: 2,500 GB/month, roughly **10× the stored volume**, and the ratio widens with every visitor. Storage scales with supply; **egress scales with anonymous public traffic — which is exactly what the acquisition hypothesis is trying to increase.** `D-06` names it: *"the one cost that grows with acquisition success rather than with revenue-bearing requests."*

**Why selected:** **the right response to a dominant variable line is to eliminate it, not optimise it.** Today media is **0.6%** of the Growth bill (USD $3.53 of USD $560.79). At USD $0.15/GB the same traffic costs USD $375.00, the bill becomes USD $932.26, and media becomes **40% of it — the largest single line in the model, ahead of the database.** Zero egress is also the best exit position in the evaluation — **the same API in and out, and you are not charged to walk out.**

**Related decision, same logic — audio and video are linked, not hosted.** Hosted video at Growth ≈ **USD $429/month, about 121× the entire image stack** (429 ÷ 3.53), scaling with anonymous views. *(P03 stated this as "45–140×"; neither endpoint derives from a figure in the model, and the ratio is restated here from the model's own lines.)* It also carries a **15× per-asset human moderation cost** (a person must watch or listen in real time) and imports music-licensing exposure in exactly the owner-reported categories — mariachis, norteño groups, bands, DJs — where the performance may be the provider's but the composition almost never is. **This is a V1 choice, not a permanent media strategy.** Reconsideration is deliberately two-conditional: measured link rot above 8% of external references per quarter, sustained across two consecutive quarters, **and** a measured conversion gap greater than 15% between profiles with live and dead media.

**What would make a metered option win:** served bytes per anonymous public profile view exceeding 400 KB (twice the design assumption) would mean the design, not the vendor, is the problem; or the selected provider invoking its content-type restriction — **an unresolved exposure, since Superola's public pages are image-heavy by design and that clause is `NOT VERIFIED`. It is the highest-priority check before adopting `ADR-018`.**

## 7. Geocoding — governed Place model vs vendor autocomplete vs per-search geocoding

**Architecture changes the unit economics here more than procurement does, and by roughly two orders of magnitude.**

| | **Selected: governed `Place` + provider-only geocoding** | Vendor autocomplete for customers | Per-search geocoding |
|---|---|---|---|
| Fixed monthly direction | USD $0 | USD $0 | USD $0 |
| Variable unit | USD $5.00 per 1,000 permanent geocodes; USD $3.00 per 1,000 autocomplete sessions | USD $3.00 per 1,000 **customer** sessions | USD $5.00 per 1,000 **searches** |
| **What the unit is attached to** | **providers** — ~50/300/1,200 new offerings per month, plus a 10% re-geocode rate (`C-A9`), which is why §1 prices 55/330/1,320 | **customers** (every address entry) | **searches** (600,000/month at Growth) |
| Human/operator effect | gazetteer curation 3–5 days once, ~2 h/quarter; mis-geocode queue 2–4 h/month at Growth | none new | none new |

**Growth, monthly** (`cost-model.md` §7):

| Design | Recommended geocoder | Disqualified major platform |
|---|---|---|
| **Governed `Place`** ✅ | **USD $9.06** | *(disqualified on terms)* |
| Vendor autocomplete | USD $633.50 | USD $1,192.50 |
| Per-search geocoding | ~USD $3,000 | — |

**~70× against the same vendor, ~132× against the disqualified platform — from a design decision, not a procurement decision.**

**Why the architecture wins, and it is not a cost argument first.** `domain-model.md` §1.4 already specifies a governed place node with a stable internal identifier and states that provider-typed place text is *"a resolution input, never a `Place`."* **The governed list is P02's own design, unrecognised.** And `Market` is a governed (Category × Place) pair: **if Markets are enumerable, the customer-side location vocabulary is already constrained to a governed list by construction.** Free-text customer geocoding would let a customer express a location no Market covers — **incoherent with the `Market` concept**, not merely expensive.

**The reversal that makes it cheap:** the permanent-storage tier — no free allowance, the one everybody avoids — becomes the cheapest option in the field once the `Place` list absorbs customer-side volume. **The volume that made "permanent" expensive was never the volume that needed storing.** Customers do not need their location persisted; providers do, and there are two orders of magnitude fewer of them.

**What would make another option win:** an approved requirement for street-level customer location input — a delivery archetype needing door-level eligibility — reintroduces customer-side vendor volume and reopens the whole comparison. **Vendor selection itself remains `HOLD` on two unresolved legal readings** (`ADR-019` Level 2), and a rendered map approved by P04 adds an unmodelled dynamic-map line (`ADR-019` Level 3).

## 8. Authentication — BUILD vs BUY

**Three different quantities are routinely collapsed into one here. They must not be.**

| | **BUILD (current working recommendation)** | BUY (hosted identity vendor) |
|---|---|---|
| **One-time engineering cost** | **8–12 developer-days ≈ USD $4,800–$7,200** at an assumed loaded rate | ~0 to integrate; **plus immediate re-implementation of the `VerificationFact` model** |
| **Monthly vendor cost** | **USD $0/month at every scenario** | USD $0 / USD $0 / **USD $0–225** depending on vendor at 25,000 MAU |
| **Migration / lock-in risk** | **LOW outbound — Superola holds the hashes** | **`NOT VERIFIED` in P03; re-checked in P03.1 — see `ADR-017`** |

**Vendor monthly cost at the modelled ~300 / ~3,000 / ~25,000 monthly active accounts** (`cost-model.md` §12, accessed 2026-08-11): Auth0 free to 25,000 MAU — **the free tier ends exactly at the Growth target**; Clerk 50,000 MRU free, with a B2B/organizations add-on at USD $100/month; Supabase Pro USD $25/month flat to 100,000 MAU; Amazon Cognito Essentials USD $0.015/MAU after 10,000, i.e. **USD $225.00 at 25,000 MAU**.

**Price is not the objection, and that is the finding.** The ordinary case for buying — cost relief at scale — is largely absent. The recommendation rests on architecture fit and exit:

1. **A cliff already on the roadmap.** One free tier ends at exactly the Growth target.
2. **Commercial pressure running opposite to the architecture.** `ADR-011` places authorization in the domain against actor, action, resource **and resource state** — which no vendor can see. **One vendor prices exactly the forbidden model at USD $100/month.**
3. **The `VerificationFact` mismatch.** §2 requires typed, expiring, revocable facts; **every vendor ships a verified-email boolean.** Superola would shadow it on day one.
4. **Revocation.** A server-side session is deleted and the suspension is immediate. **A safety suspension that takes effect "within 15 minutes" is not a suspension.**

**Status: `HOLD`.** ADR-017 remains `PROPOSED / HOLD` and **must not be approved as BUILD merely because the exit fact could not previously be verified.** The verification outcome and its consequences are recorded in `ADR-017` and in `p03-decision-reconciliation.md`. **The exit asymmetry is a load-bearing part of the argument, and an argument that rests on an unverified fact is not settled — in either direction.**

**What would make BUY win.** One condition has **already occurred**: self-service password-hash export is verified for Supabase Auth and Clerk, which weakens the exit asymmetry and makes the runner-up a real candidate again. **That is why the status is `HOLD` and not `RECOMMEND ACCEPT`.** The others: a signed requirement for enterprise federation from a named paying counterparty — the one thing genuinely worth buying and genuinely painful to build; **or** a decision by David that a bounded revocation window (rather than immediate suspension) is acceptable, which is the axis BUY currently loses on.

## 9. AI — no AI spend vs a capped experiment vs per-search model calls

**Kept parametric on purpose. No AI figure below is a committed cost, and none appears in any total in §1.**

| | **Selected: no AI in the V1 critical path** | Capped, optional experiment | Per-search model calls |
|---|---|---|---|
| Fixed monthly direction | USD $0 | USD $0 + a hard application-enforced ceiling | USD $0 |
| Variable unit | none | per **request** submitted (RFQs) | per **search** executed |
| **Volume the unit attaches to** | — | 300 / 3,000 / **20,000 per month** | 8,000 / 80,000 / **600,000 per month** |
| Human/operator effect | none | user confirms/corrects extracted fields — no new operator queue | none directly; abuse triage if unbounded |

**Parametric cost, per-search** (`cost-model.md` §8, `ai-evaluation.md` §6): at USD $0.001/query — USD $8 / USD $80 / **USD $600**. At USD $0.01/query — USD $80 / USD $800 / **USD $6,000, which is 10.7× the entire technology cash bill at Growth** (6,000 ÷ 560.79). *(P03's own documents stated this multiple as 4.6×; it does not reproduce from any denominator in the model and is corrected here.)* And **one un-rate-limited month of an anonymously reachable endpoint is USD $3,875 to USD $340,848 with zero marketplace effect.**

**Parametric cost, intake-scoped** — derived from the same `cost-model.md` §2.2 RFQ volumes, shown to make the structural point: at USD $0.01 per submitted request — USD $3 / USD $30 / **USD $200**. At USD $0.05 — USD $15 / USD $150 / **USD $1,000**.

**The structural point: at Growth there are 600,000 searches and 20,000 RFQs — 30× fewer.** An assist attached to *submitting a request* is attached to a volume 30× smaller, behind an authenticated action rather than an anonymous one, and therefore has a natural rate limit the search path does not. **That is a difference in kind, not a discount.**

**Why no AI spend is selected for V1:** the evidence that would justify a natural-language intent mapper does not exist, cannot exist before launch, and is additionally blocked by an unassigned privacy decision (`Q-033`). The two cheaper alternatives — a governed synonym table and a guided structured entry flow — are **already required by P02** and are prerequisites for measuring whether a model would help at all. **Superola should spend on measuring whether an interpretation problem exists, not on solving one it has not observed.**

**What would change this — and it is not "AI gets cheaper."** For ranking/search: the full gate in `ai-evaluation.md` §11. For the intake assist: **a P04 decision that the UX warrants testing it** (`ai-evaluation.md` §8). **Nothing here says AI has no useful product role in Superola** — it says no AI belongs in the V1 critical path, and that the first place worth testing is assisted intake, not ranking.

## 10. Human cost — kept separate on purpose

**Infrastructure/service cash cost and human operational cost are different quantities and are presented separately throughout this document.** No total in §1 contains an hour of anybody's time.

The human estimates in `cost-model.md` §5 remain available as an **illustrative sensitivity**, with their assumptions visible:

| | Pilot | Early | Growth |
|---|---|---|---|
| Infrastructure operations | 3.25 h/mo | 5.00 h/mo | 9.00 h/mo |
| Marketplace operator case work | 8.35 h/mo | 55.42 h/mo | 340.58 h/mo |
| **At USD $75/h and USD $40/h — illustrative** | **~USD $578** | **~USD $2,592** | **~USD $14,298** |

**The rates are assumptions with sensitivity bands** — USD $40–150/h for specialist infrastructure work, USD $20–60/h for operator case work — **and every figure scales linearly with them.** The workloads are assumptions with no measured basis. **If David performs the work the cash cost is zero and the real cost is his unavailable development time.**

**This is a sensitivity, not a forecast and not a committed operating cost.** What it is genuinely useful for is decision ordering: **a decision that saves USD $30 on the invoice and adds one hour of monthly labour is a loss at any rate above USD $30/hour.** That conclusion is robust across the whole sensitivity band, which is why the analysis is kept — and why it is kept out of the headline.

## 11. What no comparison here covers

- **Payment, booking, payout, refund, dispute and tax.** `G-02` unresolved; no line in any document covers it.
- **Legacy import and migration.** `G-09` unresolved. Claim adjudication is zero everywhere **because no cohort is approved**, and it is the single largest swing factor in the operator estimates.
- **Marketing, supply acquisition, content production, legal counsel, and design.** At Early Marketplace these plausibly exceed everything modelled here.
- **A rendered map**, if P04 approves one (`ADR-019` Level 3).
- **Salaries beyond the two illustrative operator rates.**

---

*Sources and access dates: every price in this document is reproduced from the per-line register at `cost-model.md` §12, **all accessed 2026-08-11 from official vendor pages, and all requiring re-verification before purchase.** Figures explicitly `NOT VERIFIED` are labelled where they appear: the PostgreSQL standby-node price, managed MySQL pricing, Postmark's higher-volume plans, all major-cloud supporting-cast lines, and the object-storage custom-domain content-type seam.*

*Record dates — document created 2026-08-12 (P03.1). No new price research was performed; this document explains `cost-model.md` and does not extend it.*
