# ADR-019 — A governed internal Place list, with geocoding for provider base addresses only, and no rendered map in V1

- Status: PROPOSED
- Date: 2026-08-11
- Decision owner: David (technical decision owner for this repository)
- Related evidence/requirements: `docs/03-technology/technology-evaluation.md` §4.7; `cost-model.md` §7; `p03-decision-inputs.md` `D-07`; `domain-model.md` §1.4, §4.2; `ADR-014`; `internationalization-architecture.md` §3; `security-privacy-architecture.md` §12; `integration-architecture.md`; `R-022`, `R-045`; `SRC-013`
- Supersedes / superseded by: N/A

## Problem / context

`D-07` requires resolving provider-typed and customer-typed place text to **governed `Place` identity**, supplying representative points **and precision**, with the binding constraint that **place identity is internal and never a vendor's identifier**. It flags that *"terms of use, caching and storage restrictions, and attribution requirements frequently conflict with owning place identity internally"* — and that the reported legacy defect, providers appearing in incorrect locations, makes **precision and provenance a correctness requirement, not a nicety.**

## Constraints

A coordinate without sufficient precision **may not** drive distance eligibility. `LocationEligibility` is three-valued and `undetermined` must be representable, not coerced. Country and locale are policy anchors; subdivision vocabulary and address shape differ per country. Precise provider base location is **provider-private** (§12), readable by the eligibility computation and *"never emitted to a projection, a search result, a notification, or analytics."*

## Options considered

### Option A — Governed internal `Place` list for all customer-side location input, a permanent-storage geocoder for provider base addresses only, no rendered map (recommended)

The `Place` list is materialised from an open gazetteer under a permissive commercial licence plus the relevant national statistical authority's own subdivision hierarchy, whose terms explicitly permit commercial exploitation with attribution.

### Option B — Full-vendor geocoding for both sides

Rejection factors: cost (below), and it is **incoherent with the `Market` concept** (below).

### Option C — The largest mapping platform

**DISQUALIFIED, twice, and neither reason is price.** Its policies forbid pre-fetching, caching or storing its place content beyond stated exceptions, **and the resolved coordinate is not among them** — while Superola must persist a coordinate with precision and provenance to evaluate eligibility offline. The second reason is worse: its **one** permitted persistence exception is its own place identifier, and `domain-model.md` §1.4 with `integration-architecture.md` require that place identity is internal and never a vendor's. **The vendor's only lawful persistence mechanism is exactly what the architecture prohibits.**

A separate finding worth carrying: its session-based autocomplete billing means an **abandoned** address entry costs roughly **2.2× a completed one**, because an abandoned session dissolves and every keystroke request rebills individually. That lands directly on `R-022`, where verification abandonment is already a measured cost — **abandonment would be billed twice, once as lost conversion and once as a higher unit price.** It is the inverse of the usual assumption.

### Option D — Other disqualified or rejected sources

One widely used public instance **forbids autocomplete outright in its own usage policy.** One commercial source carries a **termination-deletion clause on the exact artefact eligibility depends on.** Self-hosting a geocoder is an always-on component with a multi-gigabyte import and periodic reimport — **a hiring decision to save roughly $50 per month**, failing small-team operability.

## Decision

**Option A**, with four rules:

1. **Customer-side location resolves against the governed internal `Place` list. Zero vendor calls.**
2. **Geocode provider base addresses and unresolved free text only. Never per search** (`R-045`).
3. **No rendered map in V1.**
4. **The vendor's precision signal is translated at the boundary into Superola's own vocabulary**, and is what drives the three-valued eligibility outcome.

**Approval still required:** David, **and two legal readings must be settled before adoption** — see Validation.

## Rationale

**The governed list is not a cost optimisation. It is P02's own design, unrecognised.** `domain-model.md` §1.4 already specifies a governed place node with a stable internal identifier, a governed kind, a **variable-depth parent chain**, canonical and localized names, synonyms, and an optional representative point — and states that **provider-typed place text is *"a resolution input, never a `Place`."*** An open gazetteer plus a national subdivision hierarchy is a near one-to-one materialisation of that specification, including the localized names and synonyms a Spanish-production-locale launch needs (`DB-04`: **locale does not follow country**).

**Cost consequence at Growth, arithmetic in `cost-model.md` §7:**

| | Vendor-autocomplete design | Governed `Place` design |
|---|---|---|
| Disqualified platform | $1,192.50 | *(disqualified)* |
| Recommended geocoder | $633.50 | **$9.06** (= $6.60 permanent geocodes + $2.46 autocomplete sessions) |

**~70× against the same vendor, ~132× against the disqualified platform from a design decision, not a procurement decision.** And the reversal that makes it work: the geocoder's **permanent-storage** tier — no free allowance, the one everybody avoids — becomes the cheapest option in the field once the `Place` list absorbs customer-side volume. **The volume that made "permanent" expensive was never the volume that needed storing.** Customers do not need their location persisted; **providers do**, and there are two orders of magnitude fewer of them.

**A second, independent confirmation of the same trap:** if customer location in *search* resolved through a vendor, Growth would be 600,000 searches at permanent-tier pricing ≈ **$3,000/month**, several times the entire infrastructure bill. This is `D-13`'s unit-economics inversion appearing in a second capability, and **no P02 document named it** — hence rule 2 is stated rather than implied.

**And a coherence argument, not only a cost one.** `Market` is a governed (Category × Place-at-a-stated-granularity) pair consumed by search, liquidity measurement and future sponsored inventory. **If Markets are governed and enumerable, the customer-side location vocabulary is already constrained to a governed list by construction.** Free-text customer geocoding would let a customer express a location no Market covers, producing `undetermined` eligibility for **structural rather than data reasons**. It is not merely expensive — **it is incoherent with the `Market` concept.**

**No map, and the argument is classification, not cost.** §12 classifies precise provider base location as provider-private. **There is therefore no public surface on which a pin at a provider's location could lawfully be drawn** — it would emit provider-private data to a public projection. And no other map requirement exists in V1: no booking, no availability, no routing, no directions, no service-area drawing tool. The geographic interface reduces to *enter an address* and *display a coarse location label*. **One product decision removes the largest line item on every vendor's price sheet and simultaneously dissolves the map-coupled storage restrictions** that disqualify Option C. An open-source renderer is therefore also unnecessary — not because it is inadequate, but because **there is nothing to render.**

**Precision decides the vendor.** The selected source returns an accuracy classification plus a match code, translated at the boundary: rooftop-class accuracy may drive radius eligibility; interpolated or approximate yields `undetermined`; an intersection match routes to operator review. **That is the three-valued `LocationEligibility` implemented directly, and it is the structural fix for the reported legacy defect.** A source returning no precision signal is a correctness problem, not a convenience problem.

## Consequences

Positive: the geocoding line becomes negligible; place identity is genuinely internal; the coarse public location label comes from the governed list rather than a vendor, so no vendor artefact reaches public output (P10); and adding a locale is a data act on the governed list.

Negative: a one-time gazetteer load and curation — **a governance act, not an engineering one**, and the same governance the taxonomy already requires. A recurring refresh. And a **mis-geocode exception queue, which must exist regardless of vendor** because the legacy defect makes provider location a correctness surface. Subdivision-level disambiguation in the second market is where human time will actually go, because international geocoders flatten local vocabulary that the national authority's own hierarchy does not.

**Degradation is unusually good, and P02 did not claim it.** Eligibility computes **entirely offline** against stored coordinates. Geocoder unavailability affects exactly one flow — a provider entering a new base address — and the domain model already has the correct behaviour: hold at `undetermined`, which *"must be representable and surfaced, never silently coerced"*, and resolve by a later sweep. **The three-valued eligibility justified on data-quality grounds also makes geocoder downtime a non-event.** Discovery, ranking, the request loop and conversation are untouched.

## Cost implications

**$0.28 → $1.65 → $9.06 per month** across the three scenarios (permanent geocodes plus provider-side autocomplete sessions; see `cost-model.md` §4), against $633.50 at Growth under the vendor-autocomplete reading and ~$3,000 under per-search geocoding. Gazetteer load and curation: 3–5 days once. Recurring refresh: ~2 hours per quarter. Mis-geocode queue: ~2–4 operator hours per month at Growth.

## Lock-in and exit implications

**LOW, and lower than the category's reputation.** At Growth, cumulative stored provider base locations are roughly 10,000–15,000 (10,000 published, plus unpublished drafts and re-geocodes). Re-geocoding the entire corpus costs 15,000 × $5.00/1,000 = **$75** at the recommended source's permanent rate, or **one month of the runner-up's $50 plan**, in both cases over **a few days** — **because `GeoPoint.provenance` is mandatory, so you know exactly which coordinates came from whom.**

**Geocoding lock-in is a high-volume fear, and this design removes the volume. P02 made the exit cheap by requiring provenance; it simply never priced it.**

The runner-up has the **best exit terms verified anywhere in this evaluation** — results storable indefinitely, explicitly including after ceasing to be a customer — and is the fallback if either legal reading below goes the wrong way.

## Security and privacy implications

The precision gate is evaluated **before** any distance computation, so an insufficiently precise coordinate cannot drive eligibility — which is the structural fix for the legacy defect and costs nothing. Precise base location is read by the eligibility computation and **never emitted**; the public surface shows only the coarse governed label. Attribution requirements are satisfied in a footer, not by embedding a vendor surface. **No vendor identifier enters domain state or public output** (P10).

## Reconsideration trigger

Any one: **more than 5% of published offerings carrying `undetermined` eligibility attributable to insufficient coordinate precision, sustained over 60 days** — which measures the correctness requirement directly and is readable from domain state; **or** verified confirmation that the selected vendor's termination clause reaches permanently-licensed geocodes, which flips the recommendation to the runner-up; **or** an approved requirement for street-level customer location input — for example a delivery archetype needing door-level eligibility — which reintroduces customer-side vendor volume and reopens the whole comparison.

## Validation

Verified 2026-08-11 from official pricing pages, terms of use and policies: the disqualifying caching and place-identifier clauses; the session-token billing rule and its abandonment consequence; the recommended source's temporary-versus-permanent distinction and its precision taxonomy; the runner-up's storage and post-termination stance; the public instance's autocomplete prohibition; the third source's termination-deletion clause; and the gazetteer and national-authority licences, including that commercial exploitation is permitted with attribution.

**Two legal readings remain open and must be settled before adoption:**
1. Whether the selected vendor's general termination clause — requiring destruction of data accessed through the service — reaches permanently-licensed geocodes. **If it does, the runner-up wins.**
2. Whether rendering a vendor-derived coordinate on a public page constitutes *distribution* under a licence permitting storage *"for your own business use"* while forbidding distribution or sublicensing. **Mitigated in the recommended design, because the public surface shows a governed label rather than a vendor coordinate.**

Required instrumentation: eligibility outcome per evaluation with the `undetermined` reason code; geocoding call count and cost per month; and the share of published offerings blocked on precision.
