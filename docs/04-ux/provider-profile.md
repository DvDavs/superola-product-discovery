# Provider Profile — P04 UX Architecture

> Status: `PROPOSED — OWNER VALIDATION REQUIRED`. Provenance: `TECHNICAL_DISCOVERY`. This is a P04 UX architecture proposal, not an approved design and not a visual specification.
> Working assumptions: `WA-01`–`WA-05` (see `docs/04-ux/README.md`). `G-06` is UNSATISFIED.

This document defines the V1 public provider profile (`UX-05`): which elements exist, where each comes from, whether it is public, and the rule that constrains it. It is **not** a layout, not a wireframe, not a copy deck, and not a component specification. It does not decide contact-disclosure policy (`Q-008`, `A-010`, `OR-011`), does not decide the map question — that is `docs/04-ux/map-decision.md` — and does not design ranking, which belongs to search.

`UX-05` is the **acquisition surface**. It is public, anonymous, indexable, and it is where the largest share of served bytes and the largest share of privacy risk meet. Every rule below exists because that intersection is where marketplaces leak.

Its rendering classification is `DOC`: server-rendered, no client island, no client state that must survive navigation. Nothing on this surface justifies otherwise.

---

## 1. Element inventory

The public projection is a `PublishedSnapshot` — **allowlist-derived**. A field is public because it is explicitly marked publishable; **unknown fields are absent, not passed through.** The table below is therefore the allowlist expressed as user-visible elements, not a description of what the editor holds.

| ID | Element | Source entity | Public? | V1 / Future | Rule or constraint |
|---|---|---|---|---|---|
| `PP-01` | Provider display name | `Business` display name | Yes | V1 | The identity a customer sees everywhere: results, profile, conversation, notification bodies. **The only party identifier permitted in a notification body is this public display name.** |
| `PP-02` | Profile identity and public URL | `ProviderProfile`; slug as a locale alias | Yes | V1 | Renaming or re-categorizing **never** changes the identifier; slug changes retain redirect history. |
| `PP-03` | Self-asserted legal-name claim | `Business` | **No** in V1 | Future | Unverified, and V1 performs no business-registry verification. Publishing an unverified legal name beside a trust indicator reads as verification the platform did not perform. |
| `PP-04` | Service offerings, one per governed `Category` | `ServiceOffering` | Yes | V1 | **The offering, not the profile, is the unit of discovery eligibility.** A mariachi that also DJs is one `Business`, one `ProviderProfile`, two offerings — and the profile shows two, each with its own service area, attributes, and `RequestIntake`. |
| `PP-05` | Narrative description | `ProviderNarrative` plus `contentLocale` | Yes | V1 | **Never filterable, never taxonomy, never an attribute source.** Named explicitly so it is not quietly mined into facets. Machine-translated narrative is explicitly marked so **no surface presents a translation as the provider's own words.** |
| `PP-06` | Category-specific capabilities | `OfferingAttributeValue` bound to `CategoryAttributeDefinition` | Yes, where the definition's publication class permits | V1 | Values are language-neutral identifiers rendered as localized labels. **Governed null semantics apply: a missing value is not a matching value.** The provider supplies values; the operator governs which attributes exist. |
| `PP-07` | Images | `MediaReference` → `MediaAsset` | Yes | V1 | Only assets whose processing state is complete are rendered. Alternative text comes from the **placement**, not the asset. Metadata is stripped at re-encode. Design budget: **~200 KB served bytes per anonymous view; 400 KB is the reconsideration threshold.** |
| `PP-08` | External audio and video | `MediaReference` holding an external reference | Yes, as an embed or link-out | V1 | **Linked, never hosted** (`ADR-018` Decision rule 3). A reference known to be dead is **not rendered as a broken embed** — the element is absent and the provider is told; the dead link is a case in `OQ-08` (`UX-33`) and creates no queue of its own. Publication depends on the database fact that media exists, never on whether the vendor is currently serving it (`ADR-018` Decision rule 5). |
| `PP-09` | Coarse `Place` label | `BaseLocation` → `Place` | Yes | V1 | **Coarse only.** The label names a governed `Place`, at the granularity the archetype justifies. |
| `PP-10` | Published venue address | `PostalAddress` on `BaseLocation` | Only for a fixed-venue offering, **and only where the provider explicitly published it** | V1 | Opt-in per offering, never a default, never inferred from the archetype alone. A venue's address is a business fact the venue chose to publish; every other archetype's base address is provider-private. |
| `PP-11` | Declared coverage statement | `ServiceAreaDeclaration` | Yes | V1 | **It is a provider claim, not a platform fact — hence the name.** Rendered in words. **Units are preserved as the provider entered them:** "50 miles" and "80 km" are different provider promises, and rounding changes who is eligible. |
| `PP-12` | Languages | `contentLocale` on authored content; plus, where governed, a shared-core language attribute defined as a `CategoryAttributeDefinition` | Yes | V1 (`PROPOSED`) | `contentLocale` is a fact about the text and is always available. A *provider serves in these languages* claim is only available if a governed attribute definition exists for it; P04 recommends one at archetype scope. `Q-020` — the canonical locale for a profile — is **`OPEN`**. |
| `PP-13` | Trust and verification indicators | `VerificationFact` plus explicit `verificationBasis` | Yes, with strict wording | V1 | **V1's only honest claim is: control of a provider contact channel was proven at time T.** Never a bare `verified: true`, never a badge whose auditable event is undefined, never a boolean on the `Business` or the `Account`. |
| `PP-14` | `RequestIntake` state | `RequestIntake` on the offering | Yes | V1 | `accepting` / `paused` / `unconfirmed`, carried in **text, not color alone**, per offering. The non-claim in §4 is adjacent to it. |
| `PP-15` | Freshness indicator | `ProviderProfile` freshness state | Yes, when `Stale` | V1 | `Stale` stays **discoverable and flagged**, never hidden. §5. |
| `PP-16` | Request call to action | Entry to `UX-07` | Yes | V1 | **Anonymous.** No account is required to start a request from this surface (`WA-05`). One deliberate recipient (`WA-02`) — the CTA selects **this offering**, and never a set. |
| `PP-17` | Provider-declared price indication | `ServiceOffering` optional price indication | **`OPEN`** | `OPEN` | `domain-model.md` §1.3 permits an optional price indication on an offering; `docs/04-ux/design-canon.md` §2 forbids money-shaped language on public pages (`DB-02`). Whether a provider-declared starting price is publishable in V1 is **not resolved by any source this document may rely on.** If it ships, it is a `PriceStatement` with explicit currency, basis, inclusions, exclusions, conditions, and an as-of date — never a naked number. |
| `PP-18` | Reviews, ratings, response badges, booking counts | — | **Never** | — | §6. None of these exists as data in V1, and none could be substantiated if it did. |
| `PP-19` | `placementBasis` and ranking explanation | `PlacementSlot` | On results (`UX-04`), not on the profile | V1 | Placement is a property of a result slot, not of a profile. **No `featured` boolean exists anywhere**, so the profile has nothing to display. |

---

## 2. The hard privacy rules

| ID | Rule | Why it is structural, not editorial |
|---|---|---|
| `PP-20` | **Precise provider base location is never exposed.** Not as coordinates, not as a full address outside `PP-10`, not as a derived pin, not in a page-source attribute, not in a machine-readable block, not in analytics. | It is classified provider-private and *"never emitted to a projection, a search result, a notification, or analytics."* The coarse `Place` label and the declared coverage statement emit nothing private, which is why they are sufficient. |
| `PP-21` | **The public projection is an allowlist. Anything not on it is absent, not passed through.** Adding a field to the editor does not add it to the profile; adding it to the profile is a deliberate act against a versioned publishable field set. | A denylist fails open on every field added after it was written. The `PublishedSnapshot` records its field-set version so *"was this field public on date D?"* is answerable, and so a moderation case can prove what was public when a report was filed. |
| `PP-22` | **Providers control values; they do not control classification.** A provider selects Categories from the governed set, supplies attribute values bound to existing definitions, writes narrative, declares service area within archetype-permitted variants, and sets `RequestIntake`. A provider cannot create a taxonomy node, cannot create an attribute key, and cannot make a private field public. | Provider-created categories are exactly what degraded the legacy taxonomy. Provider input becomes a `CategoryProposal` — **never publicly visible, never searchable, never a taxonomy node, never a public URL** until an operator creates a governed Category. |
| `PP-23` | **Media metadata is stripped at re-encode, so EXIF GPS cannot reach the projection.** Uploaded bytes are never served back; only derivatives are. | Provider photographs routinely carry precise GPS. **A published photograph with intact metadata emits provider-private location to the projection** — it defeats `PP-20` through a path no policy document guards. The fix is one flag on a re-encode that must happen anyway. |
| `PP-24` | **No customer-side data appears on this surface, ever.** Not a customer name, not a contact channel, not an event address, not an event date, not a guest count, not a budget, not request free text, not conversation text, not offer amounts or terms. | The public profile is anonymous-readable. Anything visible here is visible to everyone including the adversary. The event address is the highest-harm field in V1. |
| `PP-25` | **Contact data is not an attribute of this projection.** It resolves at render time through a recorded disclosure decision, per (recipient, channel, field, request state). | The seam is what makes `A-010`/`OR-011` a policy decision rather than a rebuild. **Superola must not tell owners or users that "in-platform" means contact-protected**, because users type phone numbers into free text — which is one reason narrative is moderated content and never an analytics or notification payload. |
| `PP-26` | `ResponsivenessObservation` and `FreshnessObservation` are **computed, retained, and internal only.** The freshness indicator in `PP-15` is the publication state, not the observation. | Publishing a computed behavioural signal before policy validation is a punitive or misleading label. |

---

## 3. How the archetype changes the profile

The profile shell is the same for every archetype. What changes is the **meaning of the location elements** and which category attributes exist. No archetype adds a screen.

| ID | Archetype | Who travels | Location element on the profile | Coverage statement | Distinctive attributes |
|---|---|---|---|---|---|
| `PP-30` | **Fixed venue** | The **customer** travels to the provider. | A fixed location. The venue's `Place` label always; the published address where the provider opted in (`PP-10`). | The service area **is** the venue. There is no radius and none may be declared. | Capacity, which is a governed attribute of the venue and **not** an availability statement. |
| `PP-31` | **Mobile performer** | The **provider** travels to the customer. | Coarse `Place` label of the base only. **The base address is never published.** | Travel radius with its unit **as entered**, and/or a named-market list, plus travel conditions text. A named-market list is a first-class alternative, and it is the honest choice wherever a radius would cross a border, a mountain range, or a metro boundary. | Set length, ensemble size, equipment, indoor/outdoor suitability — all governed attribute definitions. |
| `PP-32` | **Delivery and food** | The **product** travels. | Coarse `Place` label of the production base. Address not published. | A delivery zone as radius, `Place` list, or postal-prefix list, **plus a declared lead time** stated in words on the profile. | Menu and dietary attributes; delivery versus on-site service; service window. Lead time is a coverage fact, not a date promise. |
| `PP-33` | **Mobile professional** | The **provider** travels. | As mobile performer. | As mobile performer, plus any declared setup or delivery constraint. | Session length, deliverable turnaround, setup requirements. |
| `PP-34` | **Transportation and route** | Both endpoints matter. | Depot `Place`. | Operating or permit area as a `Place` list, optionally an origin-destination corridor. | Modelled and **recommended out of the launch cohort** — route-corridor semantics do not reduce to a containment predicate. |

**The statement this section exists to make:**

> **"Filtering venues by distance from me" and "filtering performers by will travel to me" are different questions behind the same interface affordance. Conflating them is a common marketplace defect.**

Concretely, on `UX-05`: for a fixed venue, distance measures the **customer's** travel and is informational and a sort factor, **not a hard eligibility bar** — a venue thirty miles away is not ineligible, it is farther. For a mobile performer, distance is the provider's **declared willingness to travel**, and outside the declared radius or market list the offering is genuinely `ineligible`. The profile must frame the location element in the archetype's own terms so a customer never reads one as the other.

Where the archetype's resource concept is named — team, room, person, production slot, vehicle — it is **descriptive metadata only and is not evaluated in V1.** Naming it costs nothing and is precisely what makes a single universal availability boolean structurally impossible.

---

## 4. `V1 has no availability model` — where it is stated and what it distinguishes

- Evidence: `PROPOSED`. Provenance: `TECHNICAL_DISCOVERY`. `G-06` is UNSATISFIED; this section rests on `WA-01` — `DAVID WORKING ASSUMPTION — OWNER VALIDATION PENDING`.

### 4.1 Where the non-claim appears on this surface

`ADR-005` requires the non-claim to be visible in substance on **every profile and search surface**. On `UX-05` it appears in three places, in text, not only in a tooltip and not only on a help page:

| ID | Placement | Why there |
|---|---|---|
| `PP-40` | Adjacent to the `RequestIntake` indicator, per offering. | That indicator is the element a customer will otherwise read as availability. The correction belongs where the misreading happens. |
| `PP-41` | Adjacent to the request call to action (`PP-16`). | The CTA is the moment of commitment. A customer who submits believing a date is held has been misled by the surface, not by the provider. |
| `PP-42` | On the request composer's date step (`UX-07`) and on the submitted-request confirmation (`UX-10`). | The customer supplies a date; the surface must say at that moment that the date is **request context**, not a filter and not a system claim. Named here because the profile is where the expectation forms. |

### 4.2 The exact distinction

| Concept | What it is | What it is not |
|---|---|---|
| `RequestIntake` = `accepting` | The provider is currently **willing to receive requests** for this offering. | Not a statement that any date is free. |
| `RequestIntake` = `paused` | The provider has **chosen** not to receive requests right now, optionally until a stated horizon, optionally with a provider-authored reason. | **Not date unavailability, and must never be worded as either.** |
| `RequestIntake` = `unconfirmed` | A stale `accepting` that **decayed** after a governed freshness window. The offering is **surfaced with its uncertainty, not silently excluded.** | Not a judgement about the provider, not a penalty, and **not a ranking input** — see `docs/04-ux/provider-workspace.md`. |
| Date availability | **Does not exist in V1.** No date, calendar, hold, concurrency, or resource-availability field exists anywhere in the model. | — |

Date feasibility is resolved **inside the request interaction**: the customer supplies date requirements as `EventContext`, and the provider may clarify, offer, decline with an optional reason, or not respond. **No surface presents a provider as guaranteed available for a slot.**

### 4.3 What `RequestIntake` may never imply

Seven things, from `ADR-005` as recorded in `docs/02-architecture/domain-model.md` §7.1. No wording on `UX-05` may imply any of them:

1. That any **date** is free.
2. That any **time window** is free.
3. That any **resource** is free.
4. That the provider **can serve this event**.
5. That any **published price is currently valid**.
6. That a **response is guaranteed, or will arrive within any period**.
7. That the provider has **capacity, crew, vehicles, rooms, or production slots** — and, cross-cutting all of them, **anything about travel willingness beyond the `ServiceAreaDeclaration`.**

---

## 5. Freshness and `Stale`

- `Stale` is a **publication state**, reached when the freshness window elapses on a `Published` profile. Its window length is `OPEN` — `POLICY PENDING`.
- **`Stale` stays discoverable and flagged rather than hidden.** It is not a permanent hidden penalty by default. Hiding stale supply in a cold-start market removes real supply for a reason the customer cannot see and the provider never learns.
- The customer sees a **freshness indicator** carrying, in text, that the provider has not recently confirmed its details — a statement about **when the information was last confirmed**, never a statement about the provider's quality, responsiveness, or availability.
- The indicator is **status carried in text, never by color alone.**
- A `Stale` profile is still eligible to receive a `ServiceRequest`: publication state `Published` **or** `Stale` satisfies the publication-state input. `EligibilityDecision`'s named inputs are **enumerated in `docs/02-architecture/domain-model.md` §1** — publication state, category eligibility, `LocationEligibility`, `RequestIntake` state, trust state, and entitlement (constant in V1); **`ADR-006` records the ownership rule** (Discovery owns the decision point) and does not enumerate them. `RequestIntake` itself is `ADR-005`.
- The customer is not told which of `Deactivated` or `Suspended` applies to an unlisted profile, because **they are indistinguishable by design** — see `docs/04-ux/provider-onboarding.md` §5.3. A direct link to either must respond identically.

---

## 6. What must never appear on this surface

| ID | Prohibited | Why |
|---|---|---|
| `PP-50` | **Response-rate or response-time badges** | `ResponsivenessObservation` is computed and retained **internal only**. Publishing it before policy validation is a punitive or misleading label, and it would make silence a public judgement. |
| `PP-51` | **Review scores, star ratings, testimonials presented as platform-verified** | A review carries an unavoidable implicature — *this person received this service from this provider* — which Phase 1 cannot substantiate for any review. Publishing one is a **truth defect, not a scope decision**. Phase 1's only observable facts are that a request was delivered, a response occurred, messages were exchanged, and a customer self-declared an intent. **None of these is a service event.** |
| `PP-52` | **Booking counts, "N events completed", "hired N times"** | No booking, payment, or completion exists in V1. The number would be fabricated or would be a request count mislabelled as a booking count. |
| `PP-53` | **Ranking-derived status labels** — "top provider", "featured", "recommended", tiers, medals | `placementBasis` is `organic` for every slot in V1, and **no `featured` boolean exists anywhere**. A ranking-derived label on a profile is sponsored placement's shape arriving without sponsorship's disclosure. |
| `PP-54` | **Punitive markers** — "slow to respond", "rarely responds", "inactive", suspension notices | `Suspended` is never a public state, and a customer must not be able to distinguish it from `Deactivated`. Silence is never presented as a judgement. |
| `PP-55` | **Any `verified` label whose auditable event is undefined** | Every trust-flavoured fact carries an explicit `verificationBasis`. `verified` is reserved for a precisely defined auditable event. **V1 has no `verified booking` and no verified identity.** |
| `PP-56` | **Money-shaped language implying transaction protection** — "book now", "secure your date", "pay through Superola", "protected payment", "deposit" | `WA-03`: V1 Phase 1 ends at marketplace outcome. There is no booking, deposit, payment, payout, refund, or transaction-derived review. **A quote is not a booking** and no surface may imply otherwise. |
| `PP-57` | **Any availability affordance** — a calendar, a date picker that filters, a "check availability" control, an availability badge | `V1 has no availability model.` A control that appears to check availability makes a claim the platform cannot evidence, regardless of what the copy beside it says. |
| `PP-58` | **A rendered map** | §7. |

---

## 7. Geographic presentation without a rendered map

**There is no rendered map in V1.** The decision, its reasoning, and its reopening triggers are in `docs/04-ux/map-decision.md`; this section states only what `UX-05` does instead. In one line: the decision was made on **user value, not vendor capability** — no V1 journey needs a rendered map, and every rendered variant carries a misleading-precision hazard.

| ID | Rule |
|---|---|
| `PP-60` | **A coarse `Place` label**, at the granularity the archetype justifies, rendered as text from the governed `Place` list. Customer-side location resolves against that governed list with **zero vendor calls**. |
| `PP-61` | **Declared coverage in words**, from the `ServiceAreaDeclaration`: *"Serves: Houston, Katy, Sugar Land"* or *"Travels up to 50 miles from Houston"*. Units preserved as entered. Any conditions text the provider supplied is shown as the provider's words. |
| `PP-62` | **Archetype-correct framing** (§3): for a venue, *the customer travels here*; for a performer, *the provider travels to you*. The same words on the wrong archetype are a false statement. |
| `PP-63` | **A link-out to the customer's own map application**, and only from a **published** venue address (`PP-10`). **A link-out is not a rendered map:** no tiles, no vendor SDK, no dynamic-map cost line, no attribution or storage clause. It never appears for an archetype whose base address is private. |
| `PP-64` | **`undetermined` is shown as uncertainty, never hidden and never coerced.** Where the offering's `LocationEligibility` for the customer's stated location cannot be decided, the surface says so plainly and still lets the customer send a request. Coercing `undetermined` to `eligible` reproduces the legacy saturation failure; coercing it to `ineligible` hides real supply and produces false empty results. |
| `PP-65` | **No pin is drawn from a coarse `Place` centroid.** A pin tells the customer something the data does not support, and `undetermined` has no honest pin at all. |

---

## 8. Constraints this document adopts, and gates it does not satisfy

- **Every `ADR-` record cited in this document — `ADR-005`, `ADR-006`, `ADR-010`, `ADR-018` — is itself `PROPOSED` and unapproved.** This document **adopts** them as constraints; it does not treat them as requirements. `AGENTS.md` forbids promoting a `PROPOSED` record to a requirement without recorded approval.
- `G-06` (what "available" promises a customer) is **UNSATISFIED**. Everything in §4 rests on `WA-01` and is `DAVID WORKING ASSUMPTION — OWNER VALIDATION PENDING`. If the owner's answer makes "available" a reservable commitment, §4 is replaced, not amended, and contention and holds enter the model.
- **`PP-17` (publishable price indication) is `OPEN`** and is a live contradiction between `domain-model.md` §1.3 and `docs/04-ux/design-canon.md` §2 that P04 does not resolve.
- **`PP-12` (languages as a governed attribute) is `PROPOSED`**, and `Q-020` — the canonical locale for a profile and for a request conversation — is `OPEN`.
- The contact-disclosure policy (`Q-008`, `A-010`, `OR-011`) is **owner-dependent and open**. `PP-25` is written so that the surface is identical under all three candidate owner answers; only the resolved value changes.
- `Q-035` (live facet counts) is a **P04 recommendation, not a resolution**: no live facet counts in V1. Its decision owner is P04 plus David.
- The **allowlist in §1 is the constraint, not the illustration.** If an element is wanted on `UX-05` and is not in that table, the correct action is to add it to the publishable field set with a version, a rule, and a stated reason — not to render it because the editor holds it.

---

*Record dates — phase executed 2026-08-12. No usability or traffic evidence exists; `SRC-006` is NOT RECEIVED.*
