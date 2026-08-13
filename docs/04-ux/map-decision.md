# Map Decision — P04 UX Architecture

> Status: `PROPOSED — OWNER VALIDATION REQUIRED`. Provenance: `TECHNICAL_DISCOVERY`. This is a P04 UX architecture proposal, not an approved design and not a visual specification.
> Working assumptions: `WA-01`–`WA-05` (see `docs/04-ux/README.md`). `G-06` is UNSATISFIED.

This document provides P04's proposed recommendation for `ADR-019` Level 3, which P03.1 removed from the technology decision and moved to P04. P04 owns exactly one question here: **does any geographic visualization provide real product value in a V1 journey?** The decision is made on user value, not on map-vendor capability — no vendor's tiles, pricing, SDK, terms or coverage were evaluated as inputs to it, and **no vendor is selected, recommended, shortlisted, or named**. It is not a geocoding decision: `ADR-019` Level 1 (governed `Place` identity, customer-side resolution with zero vendor calls, provider-only geocoding, mandatory `GeoPoint` precision and provenance) is architecture that P04 does not touch, and Level 2's vendor selection remains on `HOLD — LEGAL/TERMS VERIFICATION REQUIRED` with counsel, untouched by anything here. It is not a visual design, and it does not specify how a location label is styled.

## 1. What `ADR-019` Level 3 actually asks

P03 wrote "no rendered map in V1" into an architecture record. P03.1 removed it, and the removal is worth quoting because it defines this document's job:

> "P03's *'no rendered map in V1'* did exactly that by promoting a product conclusion into an architecture record. What P03 legitimately established is narrower and is preserved: **no V1 requirement discovered so far needs a map**, and the cost model is built on the assumption that none is rendered."

What `ADR-019` retains at Level 3 is one invariant and nothing else:

> **Precise provider-private base location must never be publicly exposed** — not to a projection, a search result, a notification, or analytics (`security-privacy-architecture.md` §12).

And `ADR-019` is explicit that **the invariant does not prohibit a map**: a city centroid, a governed coarse `Place`, a declared service area or market coverage emits no provider-private data and violates nothing in §12. So P04 arrives with a genuinely open question and no privacy argument pre-loaded on either side.

## 2. The options

| ID | Option | What problem it would solve | For which archetype | Interaction and mobile cost | Privacy properties |
|---|---|---|---|---|---|
| `MAP-A` | **No rendered map.** Coarse `Place` labels, declared coverage in words, published venue address where the provider chose to publish it, link-out to the customer's own map application. | Answers "where is it" and "does this provider cover me" in text that is readable, translatable, screen-reader-native and honest about `undetermined`. | All five `CategoryArchetype`s. | Lowest. No tiles, no vendor SDK, no viewport competition, no gesture model, no focus-trap. | Emits only governed `Place` labels and provider-authored declarations. **Precise base location never leaves the eligibility computation.** |
| `MAP-B` | **Privacy-preserving map.** A rendered surface showing some combination of: city or `Market` label, a coarse area, a `Place` centroid, a declared service region, or a coverage area. | Would let a customer see relative position and distance at a glance, and would make a `ServiceAreaDeclaration` legible as a shape rather than a sentence. | Strongest for **Fixed venue** (§4). Weak for **Mobile performer** and **Mobile professional**, where the provider travels. Not applicable to the excluded **Transportation and route** archetype (`docs/04-ux/design-canon.md` §5.3.1). | Materially higher interaction and accessibility complexity than the current non-map V1 surfaces — **argued, not measured**. It is the only plausible `RICH` surface (§6), it is among the hardest surfaces in the product to make keyboard- and screen-reader-accessible, and at phone width it competes with the result list for the entire viewport. | Emits nothing provider-private **if built correctly** — a centroid is not a base location. But it introduces the misleading-precision hazard in §5, which is a correctness problem rather than a privacy one. |
| `MAP-C` | **Precise pin** at a provider's actual base location. | Would answer "exactly where is this provider" for a customer. | None that V1 has. | Irrelevant — see the right-hand column. | **Never permitted.** A pin at a provider's exact location emits provider-private data to a public projection, violating `security-privacy-architecture.md` §12 and `ADR-019`'s retained Level 3 invariant. This is not a trade-off, a cost, or a preference. It is prohibited under every option, in every phase, including any future phase that approves `MAP-B`. |

`MAP-C` is listed for completeness and to make the boundary explicit, not because it was evaluated. **A provider's home address is the base location for most of the supply in a Mobile performer and Mobile professional marketplace**, and publishing it is the kind of harm the repository's privacy architecture exists to prevent. It is out of the comparison from the first line.

## 3. The decision — **no rendered map in V1**

Evidence: `PROPOSED`. Provenance: `TECHNICAL_DISCOVERY`. `MAP-A` is selected. Canon §5.9.

### 3.1 The reason, stated first

**No V1 journey needs a rendered map.** That is the whole reason, and it is a product finding, not an inherited constraint.

| ID | Argument | Detail |
|---|---|---|
| `MR-01` | **Discovery is category-first and `Place`-first, not proximity-browsing.** | The primary axis of `docs/04-ux/customer-journey.md` is need → governed `Category` → governed `Place` → deterministic eligibility → ranked results (`docs/04-ux/design-canon.md` §5.6). A customer looking for a mariachi in Houston picks a category and a place from a governed enumerable list. **The designed journey gives the customer no point at which a map is the selection instrument**, because the axis it puts them on is category and archetype, not proximity — so a map would answer a question this journey does not ask. **Stated as what it is: an argument from the journey structure, not observed customer behaviour.** No customer has been observed choosing or not choosing by map; `SRC-006` is NOT RECEIVED, and §9's limit 1 says the same thing. The claim is that the journey does not ask the question, **not** that customers would never scan a map if one existed. |
| `MR-02` | **Eligibility is a predicate, not a visual search.** | `EligibilityDecision` has six named inputs (`ADR-006`), one of which is `LocationEligibility` = `eligible` \| `ineligible` \| `undetermined`. The system has already computed the answer before the customer sees the result. A map would invite the customer to re-derive, by eye and less accurately, a decision the domain has already made — and to disagree with it. |
| `MR-03` | **Free-text and street-level customer location do not exist in V1.** | `ADR-019` rule 2 resolves customer-side location against the governed internal `Place` list with **zero vendor calls**, and rule 3 forbids geocoding per search. There is no street-level customer input to plot the customer end of a line against. A map with one end coarse and the other end absent is a decoration. |
| `MR-04` | **The `Market` concept already constrains the vocabulary.** | `Market` is a governed (Category × Place-at-a-stated-granularity) pair. If Markets are governed and enumerable, the geographic vocabulary a customer can express is already a list, and a list renders as a list. |
| `MR-05` | **Nothing downstream needs it.** | `WA-03` ends V1 Phase 1 at marketplace outcome: no booking, no availability model (`ADR-005`), no routing, no directions, no service-area drawing tool, no dispatch. Every product feature that conventionally justifies a map is `FUTURE` or absent. |

### 3.2 The privacy invariant is retained absolutely — and is **NOT** the reason

**This must not be misread, because P03 got it wrong and P03.1 corrected it.**

The invariant holds, without exception and without qualification: precise provider-private base location is never publicly exposed — not to a projection, a search result, a notification, or analytics. `MAP-C` is prohibited. That is unchanged, it is not P04's to weaken, and approving a map later would not weaken it either.

**But the invariant is not the reason for this decision, and citing it as one would repeat the exact defect P03.1 fixed.** A city centroid emits nothing private. A declared coverage area drawn from a provider's own `ServiceAreaDeclaration` emits nothing private — the provider authored it and it is already shown in words on the public profile. A `Market` boundary emits nothing private. **`MAP-B` is compatible with the invariant.** P03 wrote an invariant and a product conclusion as one sentence, and they were never one thing; P03.1 separated them and moved the product conclusion here, which is why P04 had to actually answer it rather than inherit it.

So the honest statement of this decision is: **P04 declines `MAP-B` because no V1 journey needs it (§3.1) and it would be a materially more interaction- and accessibility-intensive surface than the current non-map V1 surfaces (§6) — not because it would leak anything.** That second clause is an argued comparison against the surfaces P04 designed, not a measured cost ranking; nothing has been implemented and no rendering cost has been measured anywhere in this repository. If a journey later needs it, the privacy invariant does not stand in the way; §8 is the path.

## 4. The strongest counter-case — Fixed venue

Taken seriously, because it is the one archetype where the geography inverts.

**The case.** In **two** of the five archetypes the **provider** travels to the customer — mobile performer and mobile professional. In a third, **delivery and food, the product travels and the provider need not**, which is a different movement with the same consequence here. In a fourth, **transportation and route**, the vehicle travels between two customer-supplied endpoints, which is neither shape and is the archetype P04 recommends out of the launch cohort (`docs/04-ux/request-intake.md` §7.5). **What those four share is only this**: the customer's location, or the customer's route, is the operative geography, and the provider's base is an operational detail. **Fixed venue reverses this** — the customer travels to the provider, the provider's location *is* the product, and "where is it, and how far is that from me" becomes a primary evaluation criterion rather than an eligibility input. A customer comparing three quinceañera halls is making a genuinely spatial decision: which one is on the right side of the city, which one their guests can reach, which one is near the church. **That is the best argument for a map anywhere in Superola, and it is a real one.**

**What the customer actually needs there.** Decompose it, and the spatial-looking question is three concrete ones:

| Need | Answered by |
|---|---|
| "Roughly where in the metro is this?" | A **coarse governed `Place` label** from the internal list — the neighbourhood, city or subdivision at the granularity the governed hierarchy supports. |
| "What is the actual address, so I can judge it and get there?" | **The venue's published address, where the provider has chosen to publish it.** A Fixed venue is a commercial premises whose address is ordinarily public, provider-authored, and provider-controlled — this is a publication choice the provider makes, not a system disclosure. |
| "How far is that from me, and how do I get there?" | **A link out to the customer's own map application**, from the published address. |

**Why that is sufficient.** The third need is where a map appears to be required and is not. The customer's map application already knows where the customer is, already has their preferred routing mode, already has traffic, transit and accessibility data Superola will never have, and is already the tool they trust for exactly this. **A rendered map inside Superola would be a worse version of a tool the customer already has open on the same device**, and it would still hand them off for the actual navigation.

**And a link-out is not a rendered map.** This distinction is load-bearing and is the reason `MAP-A` costs nothing:

```text
published venue address (provider-authored, provider-published)
  → standard map-application link in the customer's browser
  → customer's own map application
  → V1 ENDS
```

No tiles are requested. No vendor SDK is loaded. **No dynamic-map cost line enters `cost-model.md`.** No attribution requirement attaches, no storage or caching clause attaches, and no vendor-derived artifact reaches a Superola page — which means `ADR-019` Level 2's second legal reading, whether public rendering constitutes distribution, stays exactly where it is rather than becoming live. The address rendered is provider-authored text, and the coarse label is a governed `Place` label from Superola's own list, so **no vendor artifact reaches public output** (P10) either.

**Residual honesty.** This is not free of risk. If a Fixed-venue-heavy launch cohort is approved and customers demonstrably fail to evaluate venue location from a label plus an address plus a link-out, the counter-case wins on evidence rather than on argument. That is trigger (a) in §8, and it is written to be measurable rather than rhetorical.

## 5. The misleading-precision hazard

This is the argument that makes `MAP-B` worse than neutral rather than merely unnecessary, and it is a **correctness** problem, not a privacy one.

**A pin asserts a point.** Drawn from a coarse `Place` centroid, it asserts something the data does not support. The centroid of a city is an arbitrary point inside a polygon; a customer reading a pin will reasonably conclude the provider is *there*, and for a Mobile performer whose base is thirty kilometres away on the far side of the same governed `Place`, the pin is simply false. The customer cannot tell the difference between a pin meaning "this exact location" and a pin meaning "somewhere in this city" — **both render as the same seven pixels.**

This is not hypothetical for this product. `ADR-019` records that the reported legacy defect was **providers appearing in incorrect locations**, and that this defect is why precision and provenance are a correctness requirement rather than a nicety. `GeoPoint` carries precision and provenance for exactly this reason, and `LocationEligibility` is three-valued so that insufficient precision produces `undetermined` instead of a confident wrong answer. **A pin discards precision at render time — it is a lossy projection of the one field the architecture spent its correctness budget on preserving.** Rendering a map would be a UX decision that quietly undoes an architecture decision taken to fix a defect the owner actually experienced.

**And `undetermined` has no honest pin at all.** Canon §4 rule 10 and `ADR-019` rule 4 require that `undetermined` be surfaced, never coerced. A map has three options for an `undetermined` candidate and all three are wrong: draw it somewhere (a lie), draw it nowhere (a silent drop, which is exactly the coercion the invariant forbids), or draw it with some uncertainty affordance — which is a text explanation attached to a graphic that is already saying something else. **Text has an honest rendering of uncertainty and a pin does not.**

The same applies to coverage. A drawn radius around a `ServiceAreaDeclaration` renders a provider *claim* as a geometric *fact*, complete with a crisp boundary a customer will read as authoritative. Canon §2 is explicit that `ServiceAreaDeclaration` is a provider claim and must never be presented as fact. **"Travels up to 50 miles from Houston" reads as the promise it is. A shaded circle reads as a measurement.**

## 6. Interaction, accessibility and mobile cost

`docs/04-ux/rendering-evidence.md` classifies all thirty-five rendered V1 surfaces and finds **none is classed `RICH`** — no surface requires a rich client state model surviving navigation, the one surface that holds navigation-persistent client state at all (`UX-07`, in its anonymous window) having both a domain owner and a working no-script degradation — which is the evidentiary basis for recommending Option A (server-rendered views plus progressive enhancement, three named islands) under `ADR-020`.

**A map would be the only plausible `RICH` surface in the product, and it would single-handedly weaken that recommendation.** A rendered map is a viewport with pan and zoom state, a marker layer, a selection model, and a synchronisation relationship with the result list beside it — state that users expect to survive navigating to a profile and back, which is the definition of `RICH` in `docs/04-ux/design-canon.md` §5.8. It would also be a fourth island against a five-island reconsideration trigger, consuming half the remaining headroom on one surface. The rendering recommendation is built on a finding that a map would falsify.

**It would be among the hardest surfaces in the product to make accessible**, and `docs/04-ux/design-canon.md` §5.11 sets keyboard operability for every interactive surface as a baseline rather than an aspiration. A tile map is a gesture-first control: pan, pinch-zoom, drag, and marker hit-targets sized for a fingertip. Keyboard equivalents are possible and are routinely done badly; a marker layer needs a real focus order; a screen reader needs a meaningful alternative for a graphic whose entire content is spatial relationships. **The honest accessible alternative to a map of results is a list of results — which is what `MAP-A` already ships.** The map would be an inaccessible duplicate of an accessible surface.

**At phone width it competes with the result list for the entire viewport.** `WA-04` makes the product web-first and responsive, and `docs/04-ux/design-canon.md` §5.12 requires every primary journey to be evaluated at phone width, where results are a single column with the ranking explanation collapsed to one line. A map large enough to be useful occupies most of that viewport; a map small enough to leave room for results is not useful. The conventional resolutions — a toggle, a bottom sheet, a collapsing header driven by scroll position — are each a mobile interaction pattern with its own accessibility and reduced-motion obligations (`docs/04-ux/design-canon.md` §5.11), on the surface that carries the acquisition hypothesis.

## 7. What P04 does instead — the non-map geographic presentation set

`MAP-A` in detail. This is what appears on `UX-03`, `UX-04` and `UX-05`, and what the provider declares on `UX-22`.

| ID | Presentation | Rule |
|---|---|---|
| `GP-01` | **Coarse `Place` label** | Drawn from the governed internal `Place` list at the granularity the governed hierarchy supports, using its canonical and localized labels. Never a vendor's label, never a vendor's identifier, never derived at render time from a stored coordinate. |
| `GP-02` | **Declared coverage, in words** | The provider's `ServiceAreaDeclaration` rendered as a sentence: "Serves: Houston, Katy, Sugar Land" for an enumerated declaration, or "Travels up to 50 miles from Houston" for a radius declaration. Always attributed as a provider claim, never as a system fact. |
| `GP-03` | **Archetype-correct framing** | Who travels is stated, not implied. **Fixed venue: the customer travels to the provider** — show the coarse `Place` and, where published, the venue address. **Mobile performer, Mobile professional, Delivery and food: the provider travels to the customer** — show coverage, not a base. The same coordinate means opposite things in the two framings, and presenting them identically is the defect `GP-03` exists to prevent. **Transportation and route** is modelled but recommended out of the launch cohort (`docs/04-ux/design-canon.md` §5.3.1), and its corridor semantics are the clearest case that coverage does not reduce to a drawable shape. |
| `GP-04` | **Units preserved exactly as the provider entered them** | "50 miles" and "80 km" are **different provider promises** and must not be normalised, converted, rounded, or re-expressed in the reader's locale. A converted number is a number the provider did not commit to, and the conversion would be Superola making a claim on the provider's behalf. This is also an internationalization requirement, not only a fidelity one. |
| `GP-05` | **`undetermined` surfaced as uncertainty** | Where `LocationEligibility` is `undetermined`, the candidate is shown **with its uncertainty stated in text** — never silently dropped, never coerced to `eligible` or `ineligible`, never presented as a confirmed match (`docs/04-ux/design-canon.md` §5.6, `ADR-019` rule 4). Status is never communicated by color alone (`docs/04-ux/design-canon.md` §5.11), so the uncertainty carries words. |
| `GP-06` | **Link-out to the customer's own map application** | From a provider-published address only, on Fixed venue surfaces where an address is published. **Not a rendered map**: no tiles, no vendor SDK, no attribution obligation, no storage clause, no cost line. |
| `GP-07` | **No precise base location anywhere** | Precise provider base location is read by the eligibility computation and never emitted — not to a projection, a search result, a notification, or analytics. Unchanged under every option in §2, and unchanged if `MAP-B` is ever approved. |

## 8. Deferred, not rejected

`MAP-B` is deferred with three precise reopening triggers, taken from `docs/04-ux/design-canon.md` §5.9. **Any one is sufficient**, and none is a matter of opinion — each names something that would be observed rather than argued.

| ID | Trigger | Why it is the right trigger |
|---|---|---|
| `MT-01` | A **Fixed-venue-heavy launch cohort is approved** *and* **venue-selection comprehension failure is observed in evaluation.** Both clauses required. | This is §4's counter-case winning on evidence. The first clause alone is not enough — a venue cohort with customers who evaluate location correctly from `GP-01` plus a published address plus `GP-06` has not demonstrated a need for anything. |
| `MT-02` | An **approved requirement introduces street-level *customer* location input** — for example a delivery archetype requiring door-level eligibility. | This is `MR-03` failing. It also independently trips `ADR-019` Level 1's own reconsideration trigger, because street-level customer input reintroduces customer-side vendor volume and reopens the whole geocoding comparison. The two would be reconsidered together, not separately. |
| `MT-03` | **Service-area comprehension is measured as a named cause of provider-side ineligible requests.** | This is `GP-02` failing at its job. If providers are receiving requests from outside their declared coverage because customers misread a sentence, a shape may communicate better than words — and that would be a measured comprehension finding, not a preference. |

### 8.1 The explicit consequence of approving one later

Approving any rendered geographic surface is **not** a reversal of `ADR-019` — it is P04, or a later phase, exercising a decision `ADR-019` deliberately does not hold. It is, however, a **re-price**, and four things become live at once:

1. **A dynamic-map line enters the cost model.** It is in no total anywhere today. `ADR-019` §Cost implications states that its figures "assume no rendered map", that map loads are priced separately from geocoding by the relevant sources, and that **"Neither was modelled, and neither should be estimated from memory."** That prohibition is carried forward verbatim here: **no dynamic-map price is modelled anywhere in this repository, and none may be estimated from memory.** A re-price means going back to primary sources with a recorded access date, for the specific surface being approved.
2. **The map-coupled storage and attribution clauses become live.** They are dormant today only because nothing renders tiles. `ADR-019` Option C's disqualification — its policies forbid pre-fetching, caching or storing its place content, and its one permitted persistence exception is its own place identifier, which the architecture prohibits — **must be re-read against the specific surface being approved** rather than assumed to still hold or assumed to have lapsed.
3. **`ADR-019` Level 2 legal reading 2 becomes live again.** Whether rendering a vendor-derived coordinate on a public page constitutes *distribution* under a licence that permits storage "for your own business use" while forbidding distribution or sublicensing. `ADR-019` records it as mitigated but not eliminated in the current design, precisely because the public surface shows a governed label rather than a vendor coordinate — **and states that it becomes live again if P04 approves a rendered map, since a tile surface is exactly where a vendor-derived artifact could reach the public page.** It is an unresolved legal interpretation, not a verified fact, and only counsel may settle it.
4. **`ADR-019` Level 1 survives all of it.** Governed `Place` identity, customer-side resolution against the internal list with zero vendor calls, provider-only geocoding, and mandatory `GeoPoint` precision and provenance are unaffected — **because a coarse governed label rendered on a tile is still a governed label.** Approving a map changes what is drawn, never where place identity comes from.

A fifth consequence is P04's own and is not in `ADR-019`: **the Option A rendering recommendation in `docs/04-ux/rendering-evidence.md` must be re-examined for the approved surface**, because §6 argues a map would be the only plausible `RICH` surface in the product and would consume a fourth island against a five-island ceiling.

## 9. Constraint

**This decision is `PROPOSED` and requires David's approval.** `ADR-019`'s stated approval structure is unchanged by this document: David for Level 1, counsel for Level 2's two legal readings, and P04 for Level 3. This document supplies P04's Level 3 answer and nothing else. It does not lift Level 2's `HOLD`, it does not settle either legal reading, and **no vendor purchase may proceed on anything written here.**

Three limits are named so this document is not read as stronger than it is:

1. **There is no usability evidence** (`SRC-006` NOT RECEIVED). §3.1 and §4 are arguments from the journey structure and the domain model, not from observed customer behaviour. `MT-01` and `MT-03` exist because the arguments in §4 and §7 are the kind that should be beaten by measurement when measurement becomes available.
2. **The launch cohort is not fixed.** §4's strength depends on how much of the launch cohort is Fixed venue, and the archetype validation set (`docs/04-ux/design-canon.md` §5.3) is representative rather than final. A materially venue-weighted cohort is half of `MT-01`.
3. **`G-06` is UNSATISFIED**, and P04 proceeds on `WA-01`. Nothing in this document depends on `G-06`'s resolution, but a resolution that introduced venue-date interaction would change `docs/04-ux/customer-journey.md` and should be checked against `MR-01` before this decision is relied on.

What does **not** reopen on any of the above: `MAP-C`. A pin at a provider's precise base location is prohibited under every option, in every phase, and after any future approval of `MAP-B` — it is `security-privacy-architecture.md` §12 and the retained `ADR-019` Level 3 invariant, and it is not P04's to trade.
