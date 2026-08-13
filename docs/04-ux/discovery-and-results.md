# Discovery and Results — P04 UX Architecture

> Status: `PROPOSED — OWNER VALIDATION REQUIRED`. Provenance: `TECHNICAL_DISCOVERY`. This is a P04 UX architecture proposal, not an approved design and not a visual specification.
> Working assumptions: `WA-01`–`WA-05` (see `docs/04-ux/README.md`). `G-06` is UNSATISFIED.

This document defines how a customer gets from a need to a set of candidate `ServiceOffering`s, and what a result may honestly say. It is **not** a visual design, a layout specification, a card component, a copy deck, or a ranking algorithm. It selects no search technology — `search-architecture-requirements.md` §11 owns that threshold and P03 owns the mechanism. It contains no colors, no typography, and no component names.

It covers two things that are usually treated separately and must not be: **how a query is formed** and **what a result is allowed to claim**. They are the same problem. A results page that displays a fact the domain cannot evidence is not a presentation defect; it is the marketplace asserting something it does not know.

---

## 1. The discovery model

### 1.1 The primary key is governed, and it is never free text

```text
Need
  → governed `Category` or `CategoryArchetype` reference   `UX-01` `UX-02`
  → governed `Place` reference at a stated granularity     `UX-01` `UX-03`
  → deterministic `EligibilityDecision` per `ServiceOffering`
  → deterministic organic ordering
  → ranked results, each slot carrying `placementBasis`, a disclosure marker, and a ranking explanation   `UX-04`
```

**A governed `Category` or archetype reference is the primary key. Free text is never the key.** Provider-typed and customer-typed text are *resolution inputs* against governed labels and synonyms — they resolve to a governed row or they do not resolve at all. Eligibility and filtering operate on **language-neutral identifiers**, so they are language-independent by construction; labels and synonyms are localized data over those identifiers.

**Unresolvable input is rejected and shown to the customer, never approximated.** This is the load-bearing rule of the whole surface. Approximation is how a directory silently returns the wrong market, the wrong category, or a plausible-looking list of providers who cannot serve the request — and the customer has no way to detect it. Rejection is visible, correctable, and measurable. Approximation is none of those.

### 1.2 The five ways a customer reaches results, and how they relate

| ID | Path | Surface | Produces | Relationship to the governed key |
|---|---|---|---|---|
| `DR-01` | Category and archetype browsing | `UX-02` | A governed `Category` reference with no typing at all | **Directly produces the key.** The safest path, and the one that works when the customer does not know the platform's vocabulary. Adding a `Category` is governed data and adds no screen. |
| `DR-02` | Governed `Place` selection | `UX-01` `UX-03` | A `Place` reference at a stated granularity, optionally with a `GeoPoint` carrying precision and provenance | **Resolves against the governed internal `Place` list with zero vendor calls** (`ADR-019` Level 1). Geocoding applies to provider base addresses and unresolved free text only, **never per search**. |
| `DR-03` | Market page (`Category` × `Place`) | `UX-03` | Both halves of the key at once, from a stable public URL | The acquisition surface. A `Market` is one governed concept across search, liquidity measurement, and future sponsored inventory (`Q-021`, recommended yes, unratified). |
| `DR-04` | Explicit category-scoped filters | `UX-04` | Values bound to `CategoryAttributeDefinition`s marked filterable for the target `Market` | **Narrows within the key; never replaces it.** Filterability is governed, per-`Market`, and **revocable**. A definition must state whether missing means `no` or `unknown`, and a filter must never silently exclude `unknown` without telling the customer. |
| `DR-05` | Free-text terms | `UX-01` `UX-04` (`I-1`) | Optional **secondary matching** over governed labels, synonyms, and provider narrative | **Never the key.** It ranks and disambiguates inside an already-governed candidate set. Provider narrative is never filterable and is **never mined into facets**. |

**Request-driven discovery** is the same key used twice. The `DISCOVERY`-tier composer answers — governed `Category`, governed `Place` — *are* the query. There is no second search model for "search" and "request": the composer's first two questions produce results, and the results produce the recipient. This is why the composer is progressive: asking event date, guest count, or budget before showing whether any supply exists asks the customer to invest in a market that may not exist.

**Desired date is request context only. It is never a filter and never a system claim.** `V1 has no availability model` (`ADR-005`). A date control on a results page would assert a fact the platform does not hold, and would reproduce exactly the failure `DB-10` rejects: a system suppressing supply for a reason no provider can see or diagnose.

### 1.3 There is no AI on the critical path

**The UX must never imply AI is the marketplace source of truth. In V1 there is no AI on the critical path at all.** No surface may present results, ordering, eligibility, or an interpretation of a customer's words as machine-generated understanding. No "we found what you meant", no confidence phrasing, no generated summary of a provider.

If the optional intent experiment is ever enabled (`FUTURE`, four conditions, see `customer-journey.md` `CF-06`), it converts text into **the same structured constraint set the deterministic form produces, and nothing the form cannot produce**. Unmapped constraints are **rejected, not approximated**. The interpreted constraints are shown so the customer can correct them, and interpretation failure is visible rather than silent. That structure is what guarantees the model can never become the source of truth for eligibility or ranking — it can only emit constraints Discovery validates. **The model proposes; the form disposes.**

---

## 2. The governed synonym and alias table — a P04 prerequisite

A governed synonym and alias table on the taxonomy is **required before any model is funded**, and it is a P04 prerequisite rather than a search-infrastructure feature. It absorbs a large share of surface-form variance at near-zero marginal cost, and it is the cheap alternative that must be tried first.

| ID | Requirement | Detail |
|---|---|---|
| `SY-01` | Governed rows, not mined text | Every synonym and alias is an operator-governed row bound to a `Category` identifier. Provider-typed text never becomes a synonym automatically; provider category input becomes a `CategoryProposal`, **never a node** (`ADR-007`). |
| `SY-02` | Unicode, diacritic, and case folding | `norteño` → `norteno`. Folding is applied identically to the governed rows and to the customer's input, so matching is symmetric. |
| `SY-03` | Light stemming | Singular and plural at minimum — "mariachi" / "mariachis". Aggressive stemming is out of scope: it creates false merges that a governed table exists to prevent. |
| `SY-04` | Exact, prefix, then fuzzy — in that order | Exact match wins. Prefix match drives the type-ahead (`I-1`). Fuzzy match is last, bounded, and **must be shown as a correction the customer can reject**, never applied silently. |
| `SY-05` | Bilingual by construction | Labels and synonyms are localized data over language-neutral identifiers. Cross-language discovery in a bilingual market is a **content-model question, not a search-engine question** (`Q-020`, `A-015`). |
| `SY-06` | Governance is an operator act | Adding a `Category` is a governance act; adding an archetype is an engineering act. Synonym rows follow the governance path and are reviewed in `UX-30`. |
| `SY-07` | Measurable | Unresolved input is recorded with the constraint shape attempted. That record is what tells an interpretation failure apart from a coverage failure — and it is the only way to know whether the table is working. |

**The privacy constraint on that measurement.** Unresolved-input and abandoned-query logs contain customer-typed free text, which is classified **customer-private, assume-contains-contact-data**. `Q-033` — who owns that corpus, under what lawful basis, and with what retention — is **`PARTLY LEGAL` and OPEN**, and it blocks both the labelled query set that any relevance claim would need and the AI gate. The UX requirement is therefore stated with its own gate: record the constraint *shape*, and treat the raw text as gated until `Q-033` resolves.

---

## 3. Zero results

Zero results is not an error state. It is the marketplace telling the truth about itself, and it is the highest-value measurement surface in a cold-start market with unknown local liquidity.

### 3.1 The two failure modes are different facts and must be recorded separately

| ID | Failure mode | What it means | What the customer is told | Why the distinction matters |
|---|---|---|---|---|
| `ZR-01` | **No supply in this category** | The `Category` is governed and supported, and no eligible `ServiceOffering` exists in it anywhere relevant to this query | That this service is not yet available on Superola, plainly | It is a **supply** problem. It tells the operator which categories to recruit into. |
| `ZR-02` | **No supply in this area** | The `Category` has eligible supply elsewhere, but none satisfies `LocationEligibility` for this `Place` | That this service exists on Superola but not yet in this area, plainly, with the area named | It is a **coverage** problem. It tells the operator which `Market` to open next. |

These are different facts about the market. Collapsing them into one "no results" message destroys the entire basis for later deciding whether an interpretation problem or a coverage problem exists. Every zero-result search records the constraint shape, the result count, the `undetermined` count, and the `Market`.

### 3.2 Honest coverage explanation

The explanation states what is true and stops. It does not apologise on behalf of a market that does not exist yet, it does not promise imminent supply, and it does not imply that a different query would have worked when it would not. Where the platform knows the `Category` has supply in an adjacent `Market`, saying so is honest and useful; where it does not know, saying nothing is the honest option.

### 3.3 Progressive relaxation — permitted, always named

```text
Zero results   `UX-04`
  → identify which constraint eliminated the candidates
  → offer relaxation of exactly ONE constraint, NAMED
  → show the relaxed result set, still labelled with what was relaxed
  → the customer may restore the original constraint at any point
```

The customer **must see which constraint was relaxed**. An unnamed relaxation is indistinguishable from the system quietly ignoring the customer, which is the same defect as approximation in §1.1 wearing different clothes. Relaxation never crosses the governed key: a `Place` may be widened to a coarser governed granularity, and a filter may be dropped — the `Category` is not swapped for a different one.

### 3.4 Unmet-demand capture, with no promise

An optional, no-promise capture is offered: *tell us what you were looking for*. It carries **no notification promise of any kind**. Durable `DemandWatch`, saved searches, and search alerts are `Q-031`, **unratified and excluded from V1 search scope**. No surface may say "we will let you know", "we will notify you when providers join", or anything a reasonable customer would read as a commitment. Capture is a demand signal for the operator, and the customer is told exactly that.

### 3.5 A query is never a broadcast

**Never convert a query into a broadcast.** No "send this to all providers in the area", no automatic multi-recipient request, no implicit fan-out from a zero-result page. `WA-02` holds everywhere: the customer selects a provider, and one `ServiceRequest` goes to that provider. A zero-result page is the single most tempting place to break this rule and the single most damaging place to break it — a broadcast originating from a failed search is spam with a marketplace's name on it, and it destroys the provider-side value of every request that follows.

---

## 4. Degraded input and degraded results

| ID | Case | What the customer did or the market did | Required behaviour | Never |
|---|---|---|---|---|
| `DG-01` | Too broad | Selected an archetype or a very coarse `Place` | **Rank and paginate. Do not refuse.** Offer narrowing as an option, not a demand. | Refusing to answer, or demanding a filter before showing anything. |
| `DG-02` | Too narrow | Stacked filters until the candidate set emptied | Relax with disclosure, per `ZR`/§3.3, naming the relaxed constraint. | Silently dropping a filter. |
| `DG-03` | Insufficient location granularity | Gave a `Place` too coarse for this archetype's eligibility predicate | **Ask for a governed `Place` at a usable granularity rather than guessing.** State why the granularity is needed for this kind of service. | Guessing a centroid, or driving distance eligibility from a `GeoPoint` without sufficient precision. |
| `DG-04` | Typo or synonym | Typed a variant, a plural, or a diacritic form | Resolve through the governed synonym table (§2): exact, then prefix, then bounded fuzzy **shown as a correction the customer can reject**. | Applying a fuzzy correction silently, or inventing a category that does not exist. |
| `DG-05` | Unsupported category | Typed something governed nowhere | **Say so plainly and capture the demand signal.** Reject and show; do not approximate to the nearest governed row. | Substituting a "close enough" `Category` and returning providers who do not do the thing. |
| `DG-06` | Unsupported market | A `Place` outside the launch geography | Say so plainly, name the geography that is supported, and capture the demand signal. | Silently returning results from a different `Market`. |
| `DG-07` | Provider not accepting requests | An otherwise-eligible offering has `RequestIntake` = `paused` | **Surfaced, not silently excluded.** Shown as "not accepting requests", with the provider's optional horizon and optional provider-authored reason. | **Wording it as date unavailability, in either direction.** `RequestIntake` is an intake state, not availability. |
| `DG-08` | Stale provider | Publication state is `Stale` | **Remains discoverable, with a freshness indicator.** `Stale` is never hidden. | Hiding `Stale`, or letting the customer distinguish `Suspended` from `Deactivated` — both are simply absent. **Also forbidden: demoting a `Stale` provider in ranking on the staleness clock** (`PB-07`), which is `DG-09`'s prohibition applied to the second decay clock. |
| `DG-09` | `unconfirmed` intake | `RequestIntake` decayed from `accepting` after the governed freshness window | Surfaced honestly as an intake-freshness state. **`unconfirmed` may NOT be a ranking input in V1** — eligibility surfaces it, ranking must not demote it. | Demoting a provider on a clock the provider never sees. That is the `DB-10` failure mode. |
| `DG-10` | `undetermined` candidates present | `LocationEligibility` could not be decided for a candidate | **Surfaced with their uncertainty**, never silently dropped and never presented as confirmed matches. The uncertainty carries text, not only a visual treatment. | Coercing `undetermined` to `eligible` (legacy saturation) or to `ineligible` (hides real supply, produces false empty results). |
| `DG-11` | Filterable attribute with missing values | The definition's null semantics apply | The definition states whether missing means `no` or `unknown`, and **a filter never silently excludes `unknown` without telling the customer.** | Treating missing as matching. That is how legacy search died of saturation. |
| `DG-12` | Filter with thin local coverage | The attribute is answered by a small fraction of local supply | Filterability is **revocable per `Market`**; a filter below the governed coverage threshold is not offered in that `Market`. | Offering a filter that produces a broken empty-result page in its own market. |

---

## 5. Results information hierarchy

Every element below is a candidate for the result slot. Each carries a verdict and a reason. The governing test is not "would this help the customer decide?" but **"can the domain evidence this, right now, for this offering?"** An element that fails that test is excluded no matter how useful it would be.

| ID | Element | Verdict | Reason |
|---|---|---|---|
| `RC-01` | Business / provider public display name | **Include V1** | The identity the customer is choosing. `Business` is the party; the profile is its presence. |
| `RC-02` | `ServiceOffering` — the specific service | **Include V1** | Eligibility is per `ServiceOffering`, **never per `Business`**. The result is an offering, and naming the `Business` alone would misrepresent what was matched. |
| `RC-03` | Governed `Category` label | **Include V1** | The primary key made visible, in the customer's locale, over a language-neutral identifier. It is what the customer can verify the match against. |
| `RC-04` | Coarse `Place` label | **Include V1** | Answers "where is this" at a granularity the data supports. **Never the precise provider base location** — that is never publicly exposed. |
| `RC-05` | `ServiceAreaDeclaration` stated in words | **Include V1** | "Serves: Houston, Katy, Sugar Land" or "Travels up to 50 miles from Houston", **with units preserved as the provider entered them**. Stated as a provider *claim*, never as platform-verified fact. Archetype-correct framing: a venue means the customer travels; a performer means the provider travels. |
| `RC-06` | Primary image | **Include V1** | The single highest-value evaluation element, and the only one an image budget can carry. Alternative text is a property of the `MediaReference` placement and is a publication-quality item. Derivatives come from a **small fixed enumerated set**, pre-generated at upload — no on-the-fly sizes (`ADR-018`). |
| `RC-07` | Publication freshness indicator where `Stale` | **Include V1** | `Stale` stays discoverable and flagged. The customer is entitled to know the profile has not been reconfirmed. Carries text, not color alone. |
| `RC-08` | `RequestIntake` state | **Include V1** | `accepting` / `paused` / `unconfirmed`, surfaced rather than silently excluded — a named input to `EligibilityDecision`. **`paused` is worded as "not accepting requests" and never as date unavailability.** |
| `RC-09` | `LocationEligibility` = `undetermined` marker | **Include V1** | `undetermined` is load-bearing and must be representable **and surfaced**. Shown as uncertainty, in text, never as a confirmed match and never dropped. |
| `RC-10` | Trust facts with `verificationBasis` | **Include V1** | Only the basis — for example *contact channel confirmed*, *operator reviewed*, *self-asserted*, *imported, unaudited*. **Never a bare "verified" badge and never `verified: true`.** `verified` is reserved for a precisely defined auditable event, and V1 has no `verified booking`. |
| `RC-11` | Ranking explanation | **Include V1** | Required on every slot. Not a nicety: once sponsorship exists, an unauditable ordering cannot prove organic and paid separation and `R-004` becomes unfalsifiable. At phone width it collapses to one line (`§5.12`). |
| `RC-12` | `placementBasis` disclosure marker | **Include V1** | Per-slot, distinct in **human and machine** channels. Only value in V1 is `organic`. See §6. |
| `RC-13` | The `V1 has no availability model` non-claim | **Include V1** | `ADR-005` requires the non-claim to be visible **verbatim in substance on every profile and search surface**. This is where it lives on `UX-04`. |
| `RC-14` | Distance from the customer's `Place` | **Include V1, informational only, fixed-venue archetypes** | For fixed-location archetypes distance is an **informational and sort factor, not a hard eligibility bar**. Derived only from a `GeoPoint` with sufficient precision; omitted otherwise rather than approximated. |
| `RC-15` | Indicative price | **`OPEN` — pending `Q-040`. No price element ships until `Q-040` is answered.** | **The verdict is not P04's to give, and P04's earlier "include where supplied" was a resolution of an owner question in the guise of a presentation rule.** Three documents currently take three positions: this row, `docs/04-ux/provider-profile.md` `PP-17` which records it `OPEN`, and `docs/04-ux/wireframes.md` which draws no price element and forbids one. **The wireframes are the shipping-safe position and this row now matches them**: no indicative-price element appears on any result slot, `Market` page, or profile until `Q-040` resolves. The rules below **bind only if it resolves in favour**, and none of them authorizes shipping. **P02 does give `ServiceOffering` an *"optional price indication"*** (`domain-model.md` §1, `ServiceOffering` attributes, V1). It is provider-declared and optional, so most cards will not carry it, and **Superola never computes, estimates, or infers one.** Where it exists it is a `PriceStatement`: explicit currency, basis, amount or range, and its **as-of visible beside it** — never a bare number, never a bare `$`, never a sort key, never a filter, never a comparison axis. Where it does not exist, **show nothing** — not "price on request" styled as a value. **This element sits on an unresolved conflict between two P02 documents** (`Q-040`, `R-056`): `DB-02` says no money-shaped language appears on public pages, while the domain model publishes an optional offering price. P04's reading is that `DB-02` targets *transaction*-shaped language — deposit, checkout, hold, guarantee, protection — rather than a provider's own declared price, but **that reading is P04's, is not authoritative, and does not license shipping the element.** `Q-040` is owned by David. |
| `RC-16` | Response behaviour — response rate, response time, "usually replies in X" | **Exclude V1** | `ResponsivenessObservation` is **computed and retained but internal only in V1**. Publishing it before policy validation is a punitive or misleading label, and the `NoResponse` window is `POLICY PENDING` (`Q-032`). Response-rate and response-time badges are on the never-publicly-exposed list. |
| `RC-17` | Ratings, reviews, review counts, stars | **Exclude V1** | No review event exists in V1 and there is no transaction to derive one from. **Do not fabricate ratings or reviews.** `FUTURE`, downstream of booking and payment. |
| `RC-18` | Date or calendar availability | **Exclude V1** | **`V1 has no availability model`.** No surface presents a provider as guaranteed available for a slot (`WA-01`). `G-06` is UNSATISFIED. |
| `RC-19` | Bare "Verified" badge | **Exclude V1** | The named anti-pattern. Superseded by `RC-10`. A badge with no defined auditable event behind it is a trust claim the domain cannot support. |
| `RC-20` | Precise provider base location, map pin, exact address | **Exclude V1** | Precise provider base location is never publicly exposed. Separately, **no rendered map exists in V1** (`ADR-019` Level 3): no journey needs one, and a pin drawn from a coarse `Place` centroid asserts a precision the data does not support. `undetermined` has no honest pin at all. |
| `RC-21` | `Suspended` state, moderation state, report indicators | **Exclude V1** | **Customers must not be able to distinguish `Suspended` from `Deactivated`.** Report contents and reporter identity are never exposed. |
| `RC-22` | Request or lead counts, popularity, "N people asked about this" | **Exclude V1** | A `Discovery → Demand` dependency that would let demand signals become a relevance or trust term with no governance (`R-004`). Also a cold-start lie: at launch the number is zero. |
| `RC-23` | Provider narrative excerpt or free-text snippet | **Exclude V1** | Provider narrative is a `DR-05` secondary matching input; it is **never filterable and never mined into facets**, and an excerpt chosen by a matcher is an implicit relevance claim the ranking explanation would then have to defend. `contentLocale` and machine-translation marking apply wherever narrative is shown at all. |
| `RC-24` | Save / favourite a provider | **`FUTURE`** | Requires an `Account` at discovery time, which `WA-05` deliberately does not require, and it edges toward `Q-031`'s durable-intent territory. |
| `RC-25` | Compare selected providers | **`FUTURE`** | A `RICH` interaction with cross-navigation state, against a finding that **no V1 surface is `RICH`**. It would weaken the Option A rendering recommendation for one unevidenced use. |
| `RC-26` | Sponsored or promoted marker | **`FUTURE`** | Sponsored placement is `FUTURE` (`ADR-008`, `UX-37`). When it ships it is a **separately allocated, separately labelled section**, reached by adding a `placementBasis` value — see §6. |

**Phone-width hierarchy.** Results are a single column. The ordering that survives at phone width is `RC-01` → `RC-02` → `RC-06` → `RC-04` / `RC-05` → `RC-08` → `RC-10` → `RC-11` collapsed to one line. Spanish strings run materially longer than English; **no fixed-width control may depend on English string length**. Every state element — `RequestIntake`, freshness, `undetermined`, `placementBasis` — carries text, because **status is never communicated by color alone**.

---

## 6. Placement basis and the sponsored seam

| ID | Rule | Detail |
|---|---|---|
| `PB-01` | `placementBasis` on **every** result slot | The only V1 value is `organic`. **Carried from day one, while sponsorship does not exist.** |
| `PB-02` | The same basis on **every** measurement event | Impressions, clicks, and requests record `placementBasis` **and position**. Without this, historical organic data becomes incomparable the moment sponsorship launches — the `R-004` and `R-018` failure mode. |
| `PB-03` | A per-slot disclosure marker | Distinct in **human and machine** channels. Present in V1 even though every slot reads `organic`, because the marker is what makes the later distinction auditable rather than announced. |
| `PB-04` | Sponsored is a separate pipeline, not a scorer term | When sponsorship ships it becomes a **separately allocated, separately labelled result section**. Adding it means adding a basis value, not changing the result contract. |
| `PB-05` | **Forbidden** | **A `featured` or `sponsored` boolean on a profile or in a ranking input.** This is the named anti-pattern. `EligibilityDecision` must never gain a paid branch; payment must never buy publication. |
| `PB-06` | Ranking inputs are organic attributes only | **Constraint match strength, geographic fit, and profile completeness beyond the publication minimum** — all deterministic, all `PROPOSED`. **Two decay clocks are excluded from V1 ranking on the same reasoning, not one:** `unconfirmed` (`DG-09`) **and publication freshness / `Stale`.** |
| `PB-07` | **Publication freshness is excluded from V1 ranking** | **This reverses P04's earlier position, which admitted freshness while excluding `unconfirmed` on reasoning that applies to both.** `DG-09` forbids ranking on `unconfirmed` because it demotes a provider on a clock the provider never sees — the `DB-10` failure mode. `Stale` is the same kind of clock: **its threshold is `POLICY PENDING`** and unapproved, so a V1 demotion would run on a window no one has agreed and no provider has been told, and `docs/02-architecture/search-architecture-requirements.md` §6 requires the share of impressions and requests lost to such a clock to be **a measured V1 metric with an accepted bound** before it may demote anyone. That instrument does not exist for `Stale` any more than it does for `unconfirmed`. **The two differences that were offered do not survive:** the provider is notified of `Stale` and can act on it in one step, but the provider is also shown `unconfirmed` on `UX-22`; and the customer sees the freshness indicator, but visibility to the customer is not consent from the provider. **Freshness stays surfaced and never ranked**: `RC-07` keeps the indicator in words on every `Stale` result, and `Stale` stays discoverable and is never hidden. **Admitting freshness as a ranking input later requires exactly what `DG-C07` requires for `unconfirmed`** — an approved window, plus the lost-impressions share measured and bounded — and nothing less. |

Carrying placement provenance in V1 costs one enumerated value and one discipline. Retrofitting it after sponsorship ships requires unpicking ranking, re-deriving historical organic metrics, and re-explaining trust to customers who already saw a corrupted ordering. **The cheap path is also the one that teaches you how to price the thing**, because position-to-request attribution is only obtainable from organic data.

---

## 7. Facet counts — a recommendation to `Q-035`, not a resolution

`Q-035` asks whether a facet-count results page is in scope and whether counts must be live. Its decision owner is **P04 + David**. **P04 recommends; David decides. This section records a recommendation and does not resolve the question.**

**P04 recommendation: NO live facet counts in V1. Filters are listed without counts.**

| ID | Reasoning | Detail |
|---|---|---|
| `FC-01` | It is a page-shape decision wearing an infrastructure costume | `search-architecture-requirements.md` §11 trigger 3 — filtered facet-count cardinality makes counts infeasible — is written as an infrastructure threshold. It is really a decision about what the results page shows. **A results page that lists filters without live counts removes the trigger entirely at zero infrastructure cost.** |
| `FC-02` | The cost is asymmetric and the expensive half is the useless half | Counting attributes the customer already filtered on is cheap. Counting attributes they did *not* filter on is N aggregations per page. |
| `FC-03` | It removes an entire class of contradictions | Live counts against a three-valued `LocationEligibility` and revocable per-`Market` filterability produce "0 results (3)" states. Each one is a support ticket and a trust cost, and none of them is a bug in the count — they are a bug in the promise. |
| `FC-04` | Counts are least trustworthy exactly where they are most tempting | In a cold-start market with thin local supply, a count is a small integer that reads as a market claim. There is no traffic or usability evidence in this repository (`SRC-006` NOT RECEIVED) that counts improve filter selection. |
| `FC-05` | It is reversible in one direction only, cheaply | Shipping without counts and adding them later is a page-shape change. Shipping with counts and removing them reads to customers as capability loss. |
| `FC-06` | What would change the recommendation | Measured filter-abandonment attributable to the absence of counts, on the instrumented results page. **That measurement does not exist and cannot be claimed.** The instrumentation that would produce it is the zero-result and constraint-shape recording already required in §3.1. |

`Q-035` remains **OPEN** until David records a decision.

---

## 8. Result window, pagination, and impression recording

| ID | Rule | Detail |
|---|---|---|
| `RW-01` | The result window is page position and size | A named search input in its own right, not an implementation detail. |
| `RW-02` | **Position is recorded on impression** | Every impression records `placementBasis` **and position**. This is the same record that makes `PB-02` work, and it is the only source of position-to-request attribution. |
| `RW-03` | Too-broad queries paginate; they do not refuse | `DG-01`. Ranking and pagination are the answer to breadth. |
| `RW-04` | Pagination is server-rendered | `UX-04` is `DOC` + `I-1`. Page navigation is a document navigation, not client state that must survive navigation — **no V1 surface is `RICH`**. It must work with no JavaScript and must be crawlable, because public surfaces are bound by `ADR-020` rule 4 and public browseability is the acquisition hypothesis. |
| `RW-05` | Stable, addressable result URLs | A `Market` page (`UX-03`) and a results page (`UX-04`) are public documents with stable URLs. Pagination must not depend on hidden client state to be reachable or shareable. |
| `RW-06` | Keyboard operability | Pagination, filters, and the type-ahead (`I-1`, an ARIA combobox with managed active-descendant and a plain-select fallback) are all keyboard-operable. Every input has a programmatically associated label; every error is associated with its field and **announced, not only colored**. |
| `RW-07` | Reduced motion respected | No motion-dependent state change on result loading or filter application. |
| `RW-08` | Every zero-result search is recorded | Constraint shape, result count, `undetermined` count, and `Market` — subject to the `Q-033` gate on raw customer-typed text (§2). |
| `RW-09` | Search freshness is `read-your-writes` | There is no derived store in V1, so **the product state "published but not yet discoverable" does not exist** and must not be designed for. If a derived store is ever introduced, that state becomes real and must be shown honestly rather than hidden. |
| `RW-10` | Suppression propagates on a bounded short window | Suppression, deactivation, and moderation removal must reach every public surface promptly, independent of any rebuild. This is the only near-realtime requirement in V1 — and it is not a transport requirement: there is **no realtime transport, no server-initiated push**. |

---

## 9. Constraints and gates this surface depends on

| ID | Unresolved item | Status | What moves on this surface if it resolves differently | Owner |
|---|---|---|---|---|
| `DG-C01` | `G-06` / `Q-007` — what "available" promises a customer | **UNSATISFIED.** The gate that formally blocks P04; P04 proceeds on `WA-01` under explicit David authorization. | `RC-13`, `RC-18`, `DG-07`. If "available" comes to mean a date promise, a date filter enters discovery and `search-architecture-requirements.md` §2's "never a filter" rule is contradicted. | Owner. |
| `DG-C02` | `Q-035` — facet counts | **OPEN.** §7 is a **recommendation**, not a resolution. | §7 in full, and `search-architecture-requirements.md` §11 trigger 3, which only becomes a live infrastructure trigger if counts are approved. | **P04 + David.** |
| `DG-C03` | `Q-031` — `DemandWatch` | **Unratified and excluded from V1 search scope.** | `ZR`/§3.4 and `RC-24`. Until ratified, demand capture carries **no notification promise**. | Owner + David + product. |
| `DG-C04` | `Q-033` — ownership, lawful basis, and retention of the zero-result and abandoned-query corpus | **`PARTLY LEGAL`, OPEN.** The corpus that would open the AI gate is itself gated. | `SY-07` and `RW-08`. It also blocks the labelled query set `§11` trigger 2 requires before **any** relevance claim can be measured. | Owner + qualified counsel + David. |
| `DG-C05` | `Q-021` — is `Market` one governed concept across search, liquidity, and future sponsored inventory? | P02 recommends yes; **unratified**. | `DR-03` and `ZR-02`. Three definitions of one unit would make organic health and paid inventory unreconcilable. | Owner + product. |
| `DG-C06` | `Q-020` — canonical locale for a profile | P04 records a recommendation; **not resolved**. | `RC-23` and every localized label. `contentLocale` is explicit on provider narrative, and machine-translated content is explicitly marked so **no surface presents a translation as the provider's own words**. | P04 + owner. |
| `DG-C07` | `RequestIntake` decay window length | **OPEN.** | `DG-09`. Whatever the value, the invariant holds: eligibility may surface `unconfirmed`; **ranking may not use it in V1**, until the share of impressions and requests lost to it is a measured metric with an accepted bound. | Owner + product. |
| `DG-C08` | An offering-level indicative price | **OPEN — `Q-040`.** The object exists in P02 (`ServiceOffering` carries an optional price indication), but `DB-02`'s "no money-shaped language on public pages" and the domain model have not been reconciled, and **P04 found the conflict rather than inherited a resolution.** | `RC-15`, `R-056`. **`RC-15`'s verdict is `OPEN` — pending `Q-040`, and no price element ships until `Q-040` is answered.** P04 records its reading and does not adopt it as settled. `docs/04-ux/provider-profile.md` `PP-17` also records it `OPEN` and `docs/04-ux/wireframes.md` draws no price element; those three positions are now aligned on the shipping-safe one. If it resolves in favour, the presentation rules in `RC-15` bind; if it resolves against, the element does not exist. | David, on `DB-02` versus `domain-model.md` §1. |
| `DG-C09` | Search mechanism selection | Not P04's. | Nothing here selects a technology. This document states **requirements on what a result may claim**; `search-architecture-requirements.md` §11 owns the measured threshold for dedicated search infrastructure, and free-text relevance over provider narrative is **the only real candidate and is not in V1 scope**. | P03. |
| `DG-C10` | `SRC-006` NOT RECEIVED | There is no traffic or usability evidence in this repository. | Every claim in §5 and §7 that would want a number states the **measurement that would produce it** instead. **Do not fabricate ratings, reviews, prices, response times, traffic, or usability findings.** | — |
