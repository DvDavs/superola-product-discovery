# Cost Model — P03

> **Status:** `PROPOSED — DAVID APPROVAL REQUIRED`. Provenance: `TECHNICAL_DISCOVERY`.
>
> **Standing qualifier:** `DAVID WORKING ASSUMPTION — OWNER VALIDATION PENDING` (`SRC-013`). **Scenario stamp: S-1.**
>
> **Every price was read from the vendor's own page on 2026-08-11 and must be re-verified before any purchase.** Where a price could not be confirmed from an official page it is marked `NOT VERIFIED` and was **not** estimated from memory.
>
> **Every volume figure is a stated ASSUMPTION, not evidence.** `SRC-006` (analytics and traffic) is NOT RECEIVED — there is no traffic evidence at all. The owner-reported ~43,000 legacy registrations are unaudited (`A-001`, `R-001`) and **size nothing in this document** (`R-031`).

## 1. The headline, before the arithmetic

| | Pilot / Validation | Early Marketplace | Growth |
|---|---|---|---|
| **Fixed monthly platform** (§3) | $75.30 | $187.05 | $505.20 |
| **Usage-variable** (§4) | $0.68 | $5.94 | $55.59 |
| **CASH BILL** | **$75.98** | **$192.99** | **$560.79** |
| Human — infrastructure operations (§5.1) | 3.25 h → $243.75 | 5.00 h → $375.00 | 9.00 h → $675.00 |
| Human — marketplace operator case work (§5.2) | 8.35 h → $334 | 55.42 h → $2,217 | 340.58 h → $13,623 |
| **TOTAL COST OF OWNERSHIP** | **~$654** | **~$2,784** | **~$14,859** |
| **Human share of total** | **88%** | **93%** | **96%** |

**The finding this model exists to deliver: the infrastructure bill is not the cost.** At every modelled volume, human time is between 88% and 97% of total cost of ownership. **Any decision that saves $30 on the invoice and adds one hour of monthly labour is a loss.** `system-architecture.md` P21 already required architecture decisions to be evaluated for the manual queue volume they create; this table is that requirement with numbers attached.

## 2. Rate and volume assumptions

### 2.1 Rates — assumptions, not owner-validated

| ID | Assumption | Value | Note |
|---|---|---|---|
| `C-R1` | Infrastructure-operations hourly rate | **$75/hour** | Specialist work: patching, backups, incident response, dependency upgrades. Sensitivity band $40–$150 |
| `C-R2` | Marketplace-operator hourly rate | **$40/hour** | Non-specialist case work: taxonomy review, duplicates, report triage, support. Sensitivity band $20–$60 |

**If David performs this work himself the cash cost is zero and the real cost is his unavailable development time — which is *more* expensive, not less.** Both framings are true; the second is the honest one at Pilot.

Every human figure below scales linearly with these rates. **The conclusion that human cost dominates is robust across the entire sensitivity band** — it would take an implausible order-of-magnitude error to invert.

### 2.2 Volumes — assumptions, not evidence

| | Pilot | Early | Growth |
|---|---|---|---|
| Published providers | 150 | 1,500 | 10,000 |
| Service offerings | ~400 | ~4,500 | ~35,000 |
| Public page views / month | 10,000 | 150,000 | 1,500,000 |
| Marketplace searches / month | 8,000 | 80,000 | 600,000 |
| RFQs / month | 300 | 3,000 | 20,000 |
| Messages / month | 1,500 | 20,000 | 150,000 |
| Transactional emails / month | 3,000 | 35,000 | 250,000 |
| Stored images (originals) | 1,200 | 12,000 | 100,000 |
| Media bytes served / month | ~15 GB | ~250 GB | ~2,500 GB |
| Analytics events / month | ~50,000 | ~800,000 | ~8,000,000 |
| Application logs / month | ~2 GB | ~25 GB | ~250 GB |
| Monthly active accounts | ~300 | ~3,000 | ~25,000 |
| **Geocoding calls / month** | **~55** | **~330** | **~1,320** |

### 2.3 Derived assumptions, each named so it can be attacked individually

| ID | Assumption | Value |
|---|---|---|
| `C-A1` | Runtime is JVM-class. Web container needs ≥1 GiB, comfortable at 2 GiB; worker ≥1 GiB | — |
| `C-A2` | Image storage: original ~2 MB + three derivatives ~0.15 MB each ≈ 2.45 MB per original | — |
| `C-A3` | Cache hit ratio on public reads | 80% / 80% / 85% |
| `C-A4` | Peak-to-average request ratio | 10–15× |
| `C-A5` | Non-production environment: one small web instance + one small database. **Not a full-fidelity staging clone**, and **never seeded with production personal data** | — |
| `C-A6` | Analytics row width including tuple header | ~300 bytes |
| `C-A7` | Index overhead on marketplace tables | 40% of heap |
| `C-A8` | Bloat, fill-factor and free-space multiplier | 1.5× |
| `C-A9` | Geocoding is **provider base addresses only**, at ~50/300/1,200 new offerings per month plus a 10% re-geocode rate. **Customer-side location resolves against the governed internal `Place` list at zero vendor cost** | — |

**`C-A9` is a correction to P03's own brief and it is the single largest cost line in the model.** See §7.

## 3. Fixed monthly platform cost

| Line | Pilot | Early | Growth |
|---|---|---|---|
| Application container — web | $25.00 | $50.00 (2 instances, **for availability during deploy, not load**) | $100.00 (2 × larger) |
| Application container — worker (same artifact, separate process) | $10.00 | $25.00 | $25.00 |
| Managed PostgreSQL | $15.15 | $60.90 | $122.10 |
| PostgreSQL standby node | — | — | *$122.10* — **`NOT VERIFIED`, modelled at the primary rate** |
| Non-production environment (`C-A5`) | $25.15 | $25.15 | $40.00 |
| Content-delivery layer | $0.00 (free plan) | $0.00 | $25.00 |
| Error tracking | $0.00 (free tier) | $26.00 | $26.00 |
| Log storage | $0.00 (free tier, 50 GB/month) | $0.00 | $25.00 |
| External uptime + job dead-man's switch | $0.00 | $0.00 | $0.00 |
| Pipeline minutes | $0.00 (within free allowance) | $0.00 | *$20.00* — estimate |
| **FIXED SUBTOTAL** | **$75.30** | **$187.05** | **$505.20** |

**Notes.** The delivery layer stays on its free plan at Pilot and Early because `R-046` forbids enabling heuristic bot mitigation while the crawler gate is closed — the paid tier is bought later **to satisfy `ADR-012`, not for performance.** Log storage moves off the free tier only at Growth, where 250 GB/month exceeds the verified 50 GB allowance.

## 4. Usage-variable cost — with the unit named on every line

| Line | **Unit** | Pilot | Early | Growth |
|---|---|---|---|---|
| Object storage | **$0.015 per GB-month**, first 10 GB free | 2.94 GB → **$0.00** | (29.4 − 10) × 0.015 → **$0.29** | (245 − 10) × 0.015 → **$3.53** |
| Object-storage write operations | **$4.50 per million**, first 1M free | ~4,800 → **$0.00** | ~48,000 → **$0.00** | ~400,000 → **$0.00** |
| Object-storage read operations | **$0.36 per million**, first 10M free | 14,000 → **$0.00** | 210,000 → **$0.00** | 1,575,000 → **$0.00** |
| **Media egress** | **$0.00 per GB** | **$0.00** | **$0.00** | **$0.00** |
| Application egress | **$0.02 per GiB**, pooled allowance | **$0.00** | **$0.00** | **$0.00** |
| Transactional email | **$0.10 per 1,000 emails** | 3,000 → **$0.30** | 35,000 → **$3.50** | 250,000 → **$25.00** |
| Dedicated sending IP (Growth only, if warranted) | **$15.00 per month** | — | — | **$15.00** |
| **Geocoding — provider base addresses** | **$5.00 per 1,000**, permanent-storage tier, no free allowance | 55 → **$0.28** | 330 → **$1.65** | 1,320 → **$6.60** |
| **Provider address autocomplete sessions** | **$3.00 per 1,000 sessions**, first 500/month free | 55 → **$0.00** | 330 → **$0.00** | (1,320 − 500) × 3.00/1,000 → **$2.46** |
| Independent off-platform backup | **~$0.00695 per GB-month** | **$0.10** | **$0.50** | **$3.00** |
| **VARIABLE SUBTOTAL** | | **$0.68** | **$5.94** | **$55.59** |

**Media egress is $0.00, and that single decision is worth more than the hosting-vendor choice.** `D-06` names served bytes per anonymous public view as *"the dominant variable line… the one cost that grows with acquisition success rather than with revenue-bearing requests."* At Growth, 2,500 GB/month costs:

| Served from | Monthly |
|---|---|
| **Zero-egress object storage** ✅ | **$3.53** (storage only) |
| Application origin at $0.02/GiB | $50.00 |
| A host charging $0.09/GB | $225.00 |
| A host charging $0.15/GB | **$375.00 — more than all compute combined** |

**The right response to a dominant variable line is to eliminate it, not optimise it.**

## 5. Human and operational cost

### 5.1 Infrastructure operations — at `C-R1`, $75/hour

| Activity | Pilot h/mo | Early h/mo | Growth h/mo |
|---|---|---|---|
| Dependency and security upgrades | 1.50 | 1.50 | 2.00 |
| Backup verification + amortised restore rehearsal | 0.50 | 0.50 | 1.00 |
| Monitoring and alert review | 0.50 | 0.75 | 1.50 |
| Expected incident response | 0.50 | 1.50 | 3.00 |
| Vendor and platform change handling, capacity review | 0.25 | 0.75 | 1.50 |
| **TOTAL** | **3.25** | **5.00** | **9.00** |
| **× $75/hour** | **$243.75** | **$375.00** | **$675.00** |

### 5.2 Marketplace operator case work — at `C-R2`, $40/hour

**A model correction applied after adversarial review, because it changed the headline.** An earlier version of this table billed taxonomy exceptions and duplicate resolution against the **cumulative published-provider count, every month** — treating a stock as a flow. A duplicate resolved in month 1 does not recur in month 2, and a taxonomy exception adjudicated once is adjudicated. **Both are driven by *new* supply.** Re-basing them cut ~26 hours/month at Growth and moved total cost of ownership from ~$16,900 to ~$14,859. The arithmetic was never wrong; **the model was**, and it inflated the number the owner is shown.

The stock-driven classes are marked as such and were correct: stale-supply decay applies to the **whole published base**, because every published provider's intake state decays on a governed window.

**Volume driver, added because the correction requires it — an ASSUMPTION with no measured basis:**

| | Pilot | Early | Growth |
|---|---|---|---|
| **New published providers / month (flow)** | **25** | **100** | **300** |
| Cumulative published providers (stock) | 150 | 1,500 | 10,000 |

Case-rate assumptions, all stated as assumptions with no measured basis:

| Driver | Basis | Assumed rate | Handling time |
|---|---|---|---|
| Taxonomy proposal review | **flow** | 1 per 15 new providers (Pilot) → 1 per 50 → 1 per 100. **Decays as taxonomy matures** | 10 min |
| Taxonomy governance and synonym curation | **standing** | A block that grows with configured category count — label review, per-market divergence, synonym additions. **`R-010` guarantees taxonomy change, so this never reaches zero**, and `DB-05` names taxonomy governance the highest-ranked operator cost driver | 2 / 4 / 8 h |
| Duplicate resolution | **flow** | 2% of **new** providers — a duplicate is detected when a new registration collides | 15 min |
| Report triage | flow | 0.5% → 0.75% → 1% of RFQs | 20 min |
| Stale-supply handling | **stock — correctly** | 10% of **published** providers per month; intake decay applies to the whole base | 5 min |
| Notification-failure follow-up | flow | 1% of RFQs | 10 min |
| **Media moderation** | **flow** | 8 images per new provider at 6 s each, plus linked audio/video for 30% of new providers at 30 s each. **Linked, not hosted — hosting would be ~15× per asset** (`ADR-018`) | see left |
| **Claim adjudication** | — | **ZERO — no legacy cohort approved (`G-09` unresolved)** | — |
| Data-subject requests | absolute | 2 / 8 / 30 per month | 45 min |
| Customer and provider support | flow | 3% of RFQs | 12 min |

| Case class | Pilot h/mo | Early h/mo | Growth h/mo |
|---|---|---|---|
| Taxonomy proposals + standing governance | 2.28 | 4.33 | 8.50 |
| Duplicate resolution | 0.13 | 0.50 | 1.50 |
| Report triage | 0.50 | 7.50 | 66.67 |
| Stale-supply handling | 1.25 | 12.50 | 83.33 |
| Notification-failure follow-up | 0.50 | 5.00 | 33.33 |
| **Media moderation** | **0.40** | **1.58** | **4.75** |
| **Claim adjudication** | **0.00** | **0.00** | **0.00** |
| Data-subject requests | 1.50 | 6.00 | 22.50 |
| Customer / provider support | 1.80 | 18.00 | 120.00 |
| **TOTAL hours/month** | **8.35** | **55.42** | **340.58** |
| **× $40/hour** | **$334** | **$2,217** | **$13,623** |
| As full-time equivalent (160 h/month) | 0.05 | **0.35** | **2.13** |
| **Ratio to the technology bill** | **~4×** | **~11×** | **~24×** |

**Claim adjudication is ZERO at every scenario** because no legacy cohort is approved. **Trigger that makes it non-zero:** `G-09` and its legal questions resolve in favour of a cohort. Expect the **highest per-case cost of any class** — `security-privacy-architecture.md` §4 requires a non-disclosing challenge, evidence recorded by reference, a decision with basis, the record state at grant time, a post-claim change window, and failed-attempt counters per record and per actor. Budget 30–45 minutes per contested claim.

### 5.3 The three operator lines architecture can actually reduce

Every line above is a consequence of a design choice. Three are directly reducible, and two are the largest:

1. **Customer and provider support — 120 h/month at Growth, the single largest line.** `D-05` already names the free lever: make in-app request state authoritative and complete. The most predictable support contact in an RFQ marketplace is *"did they get my request?"*, answered by the product instead of a person. **P02 names this lever for delivery-reliability reasons only. It is also the largest operator-cost lever in the product, and P02 does not say so.**
2. **Stale supply — 83 h/month at Growth, and the only stock-driven line that stays large.** Reducible by making the freshness confirmation a **one-click provider self-service action reachable from a notification** rather than an operator task. That converts an operator hour into a provider's ten seconds. It is a notification-body design question, not an operations question — **and because it scales with the cumulative base rather than with new supply, it is the line that grows even when acquisition stops.**
3. **Report triage — 67 h/month at Growth.** Not reducible by tooling in the same way, because §10 correctly rejects automated abuse detection at this scope. **It is reducible by the report intake design**: a structured report form that captures the class and the referenced content up front removes the clarification round-trip that dominates handling time.

**And one that the correction removed from this list:** duplicate resolution was previously the third-largest line at 50 h/month. Re-based on new supply it is **1.5 h/month** — a real finding, not a rounding change. A non-blocking similarity hint at registration is still worth building, but **it is no longer a cost argument**; it is a supply-quality argument.

### 5.4 One-time build cost the invoice never shows

The operator surface `security-privacy-architecture.md` §9 requires — *"each must be possible without a database console, because a console action has no actor, no reason, and no audit record"*:

| Capability | Developer-days |
|---|---|
| Operator surface shell — auth, operator role, layout, **the audited-read wrapper**, case list | 4–6 |
| Hide / restore content | 1.5–2 |
| Restrict / suspend / reinstate an actor | 2–3 |
| Approve / deny / escalate a claim | 3–4 — **0 if no cohort is approved** |
| Unpublish / republish a profile, with bounded cache invalidation | 1–1.5 |
| **Execute a data-subject request** | **5–8** — the expensive one. If §8's subject-reference normalisation holds this is 5 days; **if it does not, it is unbounded** |
| Reverse an import batch | 3–4 — **0 if no cohort is approved** |
| Case queue with owner, class, age, backlog metrics | 3–4 |
| Operator funnel views | 1–2 |
| **TOTAL, no legacy cohort** | **~19–27 developer-days (≈1 developer-month)** |
| **TOTAL, approved legacy cohort** | **~25–35 developer-days** |

**Deferring this does not save the month. It converts the month into an unaudited console habit that cannot be retro-fitted, because the operator reads that happened during the console period have no record and never will.** That is a schedule decision disguised as a tooling decision, and it should be made explicitly at approval.

Also one-time: authentication build 8–12 developer-days (`D-08`); synonym table and guided entry flow ~13–25 developer-days (`D-13`'s cheaper alternatives, both already required).

## 6. The self-managed alternative, and why a cheaper bill is a worse total

| | Cash saved by self-managing | Extra operator hours/month | **Break-even hourly rate** |
|---|---|---|---|
| Pilot | ~$50 | 6.5 | **~$7.72/h** |
| Early Marketplace | ~$36 | 9.0 | **~$4.03/h** |
| Growth | ~$133 | 15.0 | **~$8.87/h** |

The extra hours are operating-system patching, PostgreSQL patching and major upgrades, backup tooling configuration and verification, TLS and reverse-proxy maintenance, a self-run monitoring pipeline, firewall and vulnerability triage, and unmanaged incident load with no automatic failover — plus, at Growth, operating high-availability PostgreSQL.

**Self-managing is cheaper only if the operator's time is worth under about $8/hour.** At $75/hour it is worse by $437 to $992 per month. **At any rate above roughly $12/hour, at any modelled volume, it loses.**

**Stated as plainly as the analysis supports, with the arithmetic rather than a slogan.** At Pilot:

| | Cash | Infrastructure-operations hours | At `C-R1` $75/h | **True monthly cost** |
|---|---|---|---|---|
| **Managed (recommended)** | $75.98 | 3.25 | $243.75 | **$319.73** |
| **Self-managed** | $25.98 | 3.25 + 6.5 = 9.75 | $731.25 | **$757.23** |

**The self-managed configuration bills a third as much and costs 2.4× as much.** *(An earlier draft of this paragraph said "roughly four times" and did not reproduce from its own inputs — corrected on adversarial review. The multiple also moves with the rate, which is why **the break-even table above is the honest form of this argument** and this paragraph is only the illustration.)*

What does not depend on the rate: **the saving is paid in the resource this project has least of** — the founder's time, competing directly against building a product whose demand is not yet validated (`A-011`, `A-013`, `R-014`).

## 7. The geocoding correction — the largest single line in this model

P03's own work packages carried an assumption of 1,500 / 20,000 / 200,000 address-autocomplete sessions per month, presupposing that customer-side location resolves through a vendor. **`domain-model.md` §1.4 does not describe that design** — it defines a governed internal `Place` with a stable identifier and states that provider-typed place text is *"a resolution input, never a `Place`."*

| Growth, monthly | Vendor-autocomplete reading | Governed `Place` reading |
|---|---|---|
| Disqualified major platform | $1,192.50 | *(disqualified on terms)* |
| Recommended geocoder | $633.50 | **$9.06** (= $6.60 permanent geocodes + $2.46 autocomplete sessions) |

**~70× against the same vendor, ~132× against the disqualified platform depending on candidate, from a design decision rather than a procurement decision.**

A second, independent confirmation of the same trap: if customer location in *search* resolved through a vendor, Growth would be 600,000 searches at permanent-tier pricing ≈ **$3,000/month**, several times the entire infrastructure bill. **The rule is now stated rather than implied: geocode provider base addresses and unresolved free text only; never per search** (`R-045`).

This is `D-13`'s unit-economics inversion appearing in a second capability — and `R-033` records that **public acquisition carries two unbounded variable-cost lines, media egress and per-query model cost, each previously written as if it were the sole exception.** Both are addressed here: egress is eliminated by zero-egress storage; the model line is not funded at all.

## 8. Optional and future cost — explicitly separated

| Item | Pilot | Early | Growth | Note |
|---|---|---|---|---|
| **Search engine, if a trigger fires** | — | +$0–35 | +$35–350 | **Verified: a usage-priced managed engine is ~$0 at Pilot and ~$295/month at Growth; a serverless cluster carries a $350.40/month floor from day one.** The dollar figure is the small part — the real costs are a full reindex on every taxonomy change (`R-010` guarantees these), a permanent divergence support queue, two sources of truth, and a new *published but not yet discoverable* product state |
| **AI intent mapper** | *parametric* | *parametric* | *parametric* | **Recommended against** (`ai-evaluation.md`). Parametrically, at $0.001/query: $8 / $80 / **$600**. At $0.01/query: $80 / $800 / **$6,000** — **4.6× the entire verified infrastructure bill at Growth.** And one un-rate-limited month of an anonymously reachable endpoint is **$3,875 to $340,848** with zero marketplace effect |
| **Video and audio hosting** | — | — | **~$429/month** | **Recommended against.** Scales with anonymous views; 15× per-asset human moderation; plus unpriced music-licensing exposure in the owner-reported categories (`R-043`) |
| **On-the-fly image transformation** | — | — | ~$177/month | **Architecturally excluded** — incompatible with the processing-state requirement |
| **Second locale content operations** | — | *staffing* | *staffing* | **Not an infrastructure line.** Structural cost is near-zero; operational cost is HIGH and recurring — *"a multiplier on every queue, not a new queue."* A headcount decision, deliberately not priced |
| **Legacy import environment** | — | — | — | **Not costed.** `G-09` unresolved |
| **Payment and booking** | — | — | — | **EXPLICITLY EXCLUDED.** `G-02` unresolved, `A-004` OPEN, `D-15` forbids evaluating it. A processor would add per-transaction variable cost, card-data compliance scope, payout identity verification, and reconciliation operator load. **No line in this document covers any of it** |

## 9. Lock-in, exit, and the window that closes

| Component | Bulk export | Replacement effort | Note |
|---|---|---|---|
| Managed PostgreSQL | Stock dump format, no proprietary encoding | **LOW** — any PostgreSQL host | **The exit window closes with data volume.** At Pilot the whole platform is a dump, a sync and a DNS change inside one maintenance hour. Above ~100 GB the database move needs logical replication and becomes a project |
| Application container | Image + platform spec | **LOW** | The artifact is a container |
| Object storage | S3-compatible API, **$0 egress to leave** | **LOW** | **You are not charged to walk out — an anti-lock-in property, not just a cost property** |
| Delivery network | Zone export | **LOW** | Lower time-to-live values first |
| Transactional email | Suppression list export | **LOW–MEDIUM** | Code is 2–4 days; **the 2–4 week sender-reputation warm-up is the real cost** |
| Geocoding | Coordinates owned for own business use | **LOW** | ~$75 (15,000 × $5.00/1,000) and a few days to re-geocode the whole corpus, **because `GeoPoint.provenance` is mandatory** |
| Authentication | **BUILD — you hold the hashes** | **LOW outbound** | **BUY→BUILD may be impossible. Hash exportability could not be verified for any vendor** |

**Deletion proof is not a database feature and must not be recorded as one (`R-038`).** It is a product capability: a per-subject enumeration query complete by construction, an audit record written by a different role, a re-run returning zero retained as evidence, and **an explicitly disclosed exclusion for the backup and point-in-time-recovery window.** A recovery window that can restore to before a deletion is a truthful limit and must be stated, not hidden.

## 10. What this model does not cover

- **Payment, booking, payout, refund, dispute and tax.** `G-02` unresolved.
- **Legacy import and migration.** `G-09` unresolved. Claim adjudication is zero here **because no cohort is approved**, and that is the single largest swing factor in the operator table.
- **Marketing, supply acquisition, content production, legal counsel, and design.** Outside this package's scope, and at Early Marketplace they plausibly exceed everything modelled here.
- **Salaries beyond the two operator rates in §2.1.**

## 11. Reconsideration triggers for the model itself

This model should be rebuilt, not adjusted, when any of these occur:

1. **`SRC-006` arrives**, replacing every volume assumption with evidence.
2. **A launch geography and locale are approved** (`OR-003`, `OR-004`), which fixes the region and settles whether the operator line doubles for bilingual coverage.
3. **`G-02` resolves toward including payment**, which adds the largest single cost cluster in the programme plus per-market legal work.
4. **`G-09` resolves toward an approved legacy cohort**, which turns claim adjudication from zero into the most expensive operator hour in the product.
5. **Any price in §3 or §4 changes**, which is expected — re-verify before purchase.

## 12. Price register — every figure, its vendor, and its source

**Added after adversarial review, which correctly found that deferring citations to work-package records was a governance failure: those records are not in the repository, so every price above was unfalsifiable.** `AGENTS.md` requires *"access date, authoritative primary sources"* to be persisted here. Vendor anonymisation elsewhere in P03 bought nothing — `technology-radar.md` names them anyway — and cost auditability.

All accessed **2026-08-11**. **Re-verify before purchase.**

| Line in §3 / §4 | Vendor and plan | Price as read | Source |
|---|---|---|---|
| Application container — web, worker | DigitalOcean App Platform, shared 1 vCPU/2 GiB · 1 vCPU/1 GiB · 2 vCPU/4 GiB | $25.00 · $10.00 · $50.00 per month | [docs.digitalocean.com/products/app-platform/details/pricing](https://docs.digitalocean.com/products/app-platform/details/pricing/) |
| Managed PostgreSQL | DigitalOcean Managed Databases, 1 GiB/1 vCPU · 2 GiB · 4 GiB/2 vCPU · 8 GiB/4 vCPU | $15.15 · $30.45 · $60.90 · $122.10 per month | [digitalocean.com/pricing/managed-databases](https://www.digitalocean.com/pricing/managed-databases) |
| Application egress | DigitalOcean App Platform overage, 50–900 GiB pooled allowance by tier | $0.02 per GiB | [docs.digitalocean.com/platform/billing/bandwidth](https://docs.digitalocean.com/platform/billing/bandwidth/) |
| Self-managed comparator (§6) | DigitalOcean Droplets, 2 GiB · 4 GiB/2 vCPU · 8 GiB/4 vCPU · 16 GiB/8 vCPU; Backups add-on 20% weekly | $12 · $24 · $48 · $96 per month | [digitalocean.com/pricing/droplets](https://www.digitalocean.com/pricing/droplets) |
| Object storage, operations, **egress** | Cloudflare R2 Standard | $0.015 per GB-month (10 GB free); Class A $4.50/M (1M free); Class B $0.36/M (10M free); **egress $0.00** | [developers.cloudflare.com/r2/pricing](https://developers.cloudflare.com/r2/pricing/) |
| Content-delivery layer | Cloudflare Free; Pro | $0.00; $20/month annual or $25 monthly | [cloudflare.com/plans](https://www.cloudflare.com/plans/) |
| Transactional email | Amazon SES à la carte; managed dedicated IP | $0.10 per 1,000 emails; $15.00 per month | [aws.amazon.com/ses/pricing](https://aws.amazon.com/ses/pricing/) |
| Email runner-up (§4 note) | Postmark Basic · Pro, base at 10,000/month | $15.00 · $16.50; overage $1.80 · $1.30 per 1,000 | [postmarkapp.com/pricing](https://postmarkapp.com/pricing) |
| Geocoding — permanent storage | Mapbox Permanent Geocoding, **no free tier** | $5.00 per 1,000 | [mapbox.com/pricing](https://www.mapbox.com/pricing) |
| Provider address autocomplete | Mapbox Search Box, 500 sessions/month free | $3.00 per 1,000 sessions | [mapbox.com/pricing](https://www.mapbox.com/pricing) |
| Error tracking | Sentry Developer; Team (annual) | $0.00 (5,000 errors, 1 user); $26.00 per month | [sentry.io/pricing](https://sentry.io/pricing/) |
| Log storage | Grafana Cloud Free (50 GB logs/month, 14-day, 3 users); Axiom Cloud at Growth | $0.00; $25.00 per month (1 TB) | [grafana.com/pricing](https://grafana.com/pricing/) · [axiom.co/pricing](https://axiom.co/pricing) |
| Uptime and job liveness | UptimeRobot Free; healthchecks.io Hobbyist | $0.00; $0.00 | [uptimerobot.com/pricing](https://uptimerobot.com/pricing/) · [healthchecks.io/pricing](https://healthchecks.io/pricing/) |
| Pipeline minutes | GitHub Actions, Free plan private repos; Linux 2-core overage | 2,000 minutes/month included; $0.006 per minute | [docs.github.com/billing/concepts/product-billing/github-actions](https://docs.github.com/en/billing/concepts/product-billing/github-actions) |
| Independent off-platform backup | Backblaze B2, egress free to 3× stored | $6.95 per TB-month ≈ $0.00695 per GB-month | [backblaze.com/cloud-storage/pricing](https://www.backblaze.com/cloud-storage/pricing) |
| Search engine, if promoted (§8) | Algolia Grow (10,000 searches free, then per-1,000); AWS OpenSearch Serverless minimum 2 OCU | $0.50 per 1,000 searches; $0.24 per OCU-hour → **$350.40/month floor** | [algolia.com/pricing](https://www.algolia.com/pricing) · [aws.amazon.com/opensearch-service/pricing](https://aws.amazon.com/opensearch-service/pricing/) |
| Observability platform rejected (`D-11` trap) | Datadog Infrastructure + APM, per host; log indexing per million events | $15 + $31 per host per month; $1.70 per million events | [datadoghq.com/pricing](https://www.datadoghq.com/pricing/) |
| Authentication vendors compared (`ADR-017`) | Auth0 (free to 25,000 MAU); Clerk (50,000 MRU free; B2B add-on $100/month); Supabase Pro ($25/month, 100,000 MAU); AWS Cognito Essentials ($0.015/MAU after 10,000) | as listed | [auth0.com/pricing](https://auth0.com/pricing) · [clerk.com/pricing](https://clerk.com/pricing) · [supabase.com/pricing](https://supabase.com/pricing) · [aws.amazon.com/cognito/pricing](https://aws.amazon.com/cognito/pricing/) |
| Geocoding disqualified (`ADR-019`) | Google Maps Platform — Autocomplete Requests $2.83/1,000, Place Details Essentials $5.00/1,000, Geocoding $5.00/1,000, Dynamic Maps $7.00/1,000 | see §7 | [developers.google.com/maps/billing-and-pricing/pricing](https://developers.google.com/maps/billing-and-pricing/pricing) · policy: [developers.google.com/maps/documentation/places/web-service/policies](https://developers.google.com/maps/documentation/places/web-service/policies) |
| Geocoding runner-up | OpenCage X-Small (10,000/day) | $50.00 per month | [opencagedata.com/pricing](https://opencagedata.com/pricing) |
| Managed PostgreSQL extension support (elimination evidence) | Neon; DigitalOcean; Render; Fly.io; Railway | capability, not price | [neon.com/docs/extensions/pg-extensions](https://neon.com/docs/extensions/pg-extensions) · [docs.digitalocean.com/products/databases/postgresql/details/supported-extensions](https://docs.digitalocean.com/products/databases/postgresql/details/supported-extensions/) · [render.com/docs/postgresql-extensions](https://render.com/docs/postgresql-extensions) · [fly.io/docs/mpg](https://fly.io/docs/mpg/) · [docs.railway.com/guides/postgresql](https://docs.railway.com/guides/postgresql) |
| Robots protocol is not enforcement (`R-035`) | IETF RFC 9309 | — | [rfc-editor.org/rfc/rfc9309](https://www.rfc-editor.org/rfc/rfc9309.html) |
| Platform and boundary-enforcement anchors (`ADR-013`) | Spring Boot 4.1.0 (2026-06-10); Spring Modulith 2.1 GA (2026-06-11); verification semantics | — | [docs.spring.io/spring-modulith/reference/verification.html](https://docs.spring.io/spring-modulith/reference/verification.html) · [api.spring.io/projects/spring-boot/releases](https://api.spring.io/projects/spring-boot/releases) |

**Model-provider prices** for the AI evaluation are cited per line in `ai-evaluation.md` §5.

### Explicitly `NOT VERIFIED` — carried as gaps, none filled from memory

| Gap | Consequence |
|---|---|
| **PostgreSQL standby-node price** | Modelled at the primary rate in §3; labelled in the table |
| **Render instance and workspace prices; Hetzner per-plan prices; DigitalOcean load-balancer price** | Render cannot be selected until confirmed; the self-managed comparator's Early and Growth fixed cost is incomplete |
| **All major-cloud supporting-cast lines** — load-balancer hours, network-gateway hours and per-GB, public-address hours, log ingestion, support-plan minimum | That option is **deferred, not costed**. No figure was estimated |
| **Delivery-network cache-purge propagation time** | **No vendor publishes one.** P20's bounded propagation must be **measured in V1, never assumed** |
| **Password-hash exportability for any authentication vendor** | The axis on which `ADR-017` is recommended. Not a price, and the most important unverified item in the phase |
| **The object-storage custom-domain / content-type seam** | Highest-priority item before adopting `ADR-018` |
| **The two geocoding legal readings** | Requires counsel, not research. Blocks `ADR-019` adoption |
| **Sonnet-tier model pricing after 2026-08-31** | An introductory rate with a known expiry — see `ai-evaluation.md` §5 |

**Repository inputs:** `docs/02-architecture/p03-decision-inputs.md` (cross-cutting criteria) · `system-architecture.md` (P21) · `domain-map.md` (per-module cost annotation) · `security-privacy-architecture.md` §4, §9, §10, §12 · `internationalization-architecture.md` §4 · `docs/05-roadmap/mvp-scope.md` · `risks.md` (`R-021`, `R-031`, `R-033`) · `technology-evaluation.md`, `ai-evaluation.md` · `SRC-013`, `SRC-014`.
