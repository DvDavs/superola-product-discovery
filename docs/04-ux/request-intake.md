# Request Intake — P04 UX Architecture

> Status: `PROPOSED — OWNER VALIDATION REQUIRED`. Provenance: `TECHNICAL_DISCOVERY`. This is a P04 UX architecture proposal, not an approved design and not a visual specification.
> Working assumptions: `WA-01`–`WA-05` (see `docs/04-ux/README.md`). `G-06` is UNSATISFIED.

This document decides the **shape** of the customer's request-composition interaction: which model of intake Superola adopts, which questions exist, which of them block a submission, how a `RequestDraft` behaves from the customer's point of view, and where the boundary between an event and a service sits. It is not a screen design, not a wireframe set, not a copy deck, not a validation-rules specification, and not a database design — no table, column, index, or storage decision appears here, and none may be inferred from it. It fixes no visual treatment, no component, no color, and no string. It does not select a language model, and it does not reverse `docs/03-technology/ai-evaluation.md` §10. It satisfies no owner gate.

---

## 1. The three intake models compared

The decision is which model governs the customer's path from *"I need a mariachi"* to a delivered `ServiceRequest`.

| ID | Model | What it is | Customer cost | What it needs from the taxonomy | Failure mode | Cost of adding a new Category | Verdict |
|---|---|---|---|---|---|---|---|
| `IM-01` | Traditional guided intake | One fixed sequential question set presented to every customer regardless of what they are buying. The union of every category's needs, asked of everyone. | High and **uniform** — the customer answers the whole union, including the parts that do not apply. The observed competitor capture in §2 is this shape at **24 captured items, of which approximately twenty are presented to the customer as steps** (§2.2). | Very little. The taxonomy is a label on a dropdown; the form does not read it. This is precisely why the model looks cheap and is not. | **It asks venue questions of a cake baker.** Every question that does not apply to the selected `Category` still costs an interaction, teaches the customer that the form is not listening, and produces answers that improve no `ProviderResponse`. It also forces the customer to answer before knowing whether any supply exists at all. | **Every added Category makes the universal form worse for everyone.** The union grows; the per-customer irrelevant fraction grows with it. Cost is borne by every customer of every other category. | `REJECTED` as the primary model. |
| `IM-02` | Progressive intake | Ask only the next question the selected `Category`'s `CategoryArchetype` actually requires, against the five governed classes of §6. The question set is **data derived from `CategoryAttributeDefinition`**, not a hand-built form per category. | Low and **proportional** — the customer answers what their purchase actually needs. Design target for a mobile performer: **5 composer answers on the discovery path, up to 8 on the direct-arrival path, and 10–11 blocking items in total including identity** (§3). | Everything. It requires a governed `Category` reference, a governed `CategoryArchetype`, and operator-governed `CategoryAttributeDefinition`s with a class, a requirement level, and an answer shape. `ADR-007` already provides all three. | Question governance becomes a real operator responsibility. A `CategoryAttributeDefinition` that improves no provider decision and gates nothing is friction with a governance process attached — so §6's classification is not documentation, it is the admission test. | **Near zero, and it adds no screen.** A new Category is governed data plus governed attribute definitions. The composer renders it without a new screen and without an application-shell change. | **SELECTED.** |
| `IM-03` | Assisted intake | The customer writes free text; a model extracts known structured fields; the customer confirms or corrects every extracted field; the composer then asks only what is still missing. **The model proposes; the form disposes.** | Potentially the lowest for a customer who can express the whole request in one sentence. Unmeasurable today — there is no baseline to measure against, because `IM-02` does not exist yet. | The same governance as `IM-02`, plus a resolved lawful basis for sending customer-private free text to a third party. It is an addition to progressive intake, never a replacement for it. | Extraction is least reliable exactly where the remaining friction concentrates — free-text notes and multi-constraint event description — and that free text is `Q-033`-blocked. A model outage must degrade to typing, not to failure. | Same as `IM-02`; it inherits the governed set rather than replacing it. | **`FUTURE`, not V1.** Conditions in §9. |

**Selected: `IM-02`, progressive, deterministic, category-governed.** `IM-01` is `REJECTED` as the primary model. `IM-03` is `FUTURE`.

### 1.1 Design consequences of the selection

| ID | Consequence | Rule |
|---|---|---|
| `IC-01` | Progress must be honest | The number of remaining questions is **data-dependent**, so *"Step 3 of about 6"* is a false denominator. Use a **named-stage indicator** with the current stage announced. This is an accessibility requirement as much as an honesty one. |
| `IC-02` | Every question justifies itself | Each question must classify under §6 as `DISCOVERY`, `PRE-SUBMIT`, `QUALITY`, or `CATEGORY`. **A question that improves no decision and gates nothing is deleted, not demoted.** |
| `IC-03` | Questions are governed data | The composer renders `CategoryAttributeDefinition`s. It never contains a hand-built form per category, and it never contains a category name in a branch condition. |
| `IC-04` | Discovery answers are never re-asked | `Category`, `Place`, and the selected recipient `ServiceOffering` are established during discovery and carried into the composer. Re-asking them is a defect. |
| `IC-05` | Reveal is an accessibility problem, not a layout problem | When a category selection reveals archetype questions, the new region is announced politely, focus moves deliberately to the new region's heading rather than to its first input, and already-answered questions are never reordered. |

---

## 2. The GigSalad comparative input

### 2.1 What this input is, and what it is not

- Evidence: `OBSERVED` (competitor behavior, as supplied). Provenance: `DAVID_DIRECTIVE` (it reached this repository as David-supplied comparative input).
- It is **not an owner requirement.** No owner has asked Superola to match, approach, or diverge from this flow.
- It is **not canonical Superola behavior**, present or proposed.
- It is **not evidence that Superola must copy it**, and no Superola requirement in this document or anywhere in P04 derives from it. Where Superola's disposition happens to coincide with a captured item, the reason given in §2.2 is the reason — the coincidence is not.
- It was **not independently re-verified against first-party sources during P04.** `docs/01-product/competitive-benchmark.md` carries a separate, dated, first-party benchmark of the same platform under `GS-01`–`GS-09`; that benchmark is the citable record, and this capture is not a substitute for it.
- Its only function here is **contrast**: it makes the shape of `IM-01` concrete, so the argument in §1 is about an observable artifact rather than about a caricature.

The capture is filed at `docs/07-research/gigsalad-request-flow-capture.md`, which is the citable record of what was supplied. It is recorded at **step-shape granularity**: it asserts which items the flow contained and their approximate order. It does **not** assert exact wording, exact ordering, exact screen grouping, or the required-or-optional status of any item as presented on the live surface, and it carries **no friction, abandonment, or usability measurement**. **`OPEN`** — it remains un-re-verified against first-party sources and carries no access date; where a first-party record is needed, `docs/01-product/competitive-benchmark.md` `GS-01`–`GS-09` is the dated benchmark and this capture is not a substitute for it. **No P04 document may assert from it anything the capture itself does not assert**, and in particular no document may state which captured question is hardest for a real user.

### 2.2 Captured items and Superola's disposition

**The denominator, stated before the table so no later section quotes a figure with no basis.** The enumeration below is **24 captured items** (`GC-01`–`GC-24`). Where this document says *approximately twenty steps*, the basis is the one the filed capture states in its own terms: **several captured items are grouped or conditional, so the number of screens a customer sees is smaller than twenty-four and is not known precisely** (`docs/07-research/gigsalad-request-flow-capture.md`). **24 captured items is therefore the citable denominator**, and *approximately twenty are presented to the customer as steps* is the qualified step figure. Neither may be quoted as a measurement, and the capture asserts no exact step count.

| ID | Captured item | Superola disposition | Superola field or attribute | Class | Why |
|---|---|---|---|---|---|
| `GC-01` | Select the service or category | **Asked, earlier** | Governed `Category` reference resolved during discovery, not in the composer | `DISCOVERY` | Superola cannot produce results at all without it, so it precedes composition rather than opening it (`IC-04`). |
| `GC-02` | Select the event type or occasion | **Asked** | `EventContext` occasion type | `PRE-SUBMIT` | Universal, cheap, and materially changes what a provider offers. **Never a search filter** — filtering supply by occasion invents an eligibility rule the supply model does not have. |
| `GC-03` | Event date | **Asked, with `flexible` as a first-class answer** | `EventContext` date, window, or `flexible` | `PRE-SUBMIT` | Request context only. **Never a filter and never a system claim** — `V1 has no availability model`, and a date filter would assert one (`ADR-005`, `WA-01`). |
| `GC-04` | Event start time | **Asked for some archetypes only** | Archetype `CategoryAttributeDefinition` | `CATEGORY` | Required for mobile performer and mobile professional. Irrelevant to a cake. Asking it universally is `IM-01`'s defect in miniature. |
| `GC-05` | Event duration or end time | **Asked for some archetypes only** | Archetype `CategoryAttributeDefinition` | `CATEGORY` | Performer, professional, transportation. A fixed venue answers a window, not a duration; a cake answers neither. |
| `GC-06` | Event location — city or postal code | **Asked, earlier, and resolved against a governed list** | Governed `Place` at a stated granularity, resolved during discovery | `DISCOVERY` | `ADR-019` Level 1: customer-side location resolves against the governed internal `Place` list with **zero vendor calls**. Free-text customer geocoding is prohibited; unresolvable input is rejected and shown to the customer, never approximated. |
| `GC-07` | Venue name, or venue type such as indoor or outdoor | **Split** | Setting (indoor / outdoor / both / unknown) is a mobile-performer and mobile-professional `CategoryAttributeDefinition`. Venue **name** is not asked. | `CATEGORY` | Setting changes equipment, power, and price for a provider who travels. A venue name is free text that improves no decision the provider can act on and is a contact-and-address vector — the event address is the highest-harm field in V1 and is never publicly exposed. |
| `GC-08` | Guest count | **Asked, at a class that depends on the archetype** | `EventContext` guest-count indication | `CATEGORY` | **Blocking** for fixed venue (capacity is the venue's hard feasibility constraint) and for delivery and food (it is the quantity). `QUALITY` for a mobile performer, where it informs a quote and gates nothing. The same field with three different requirement levels is exactly what `IM-01` cannot express. |
| `GC-09` | Age range or audience description | **Not asked** | — | — | It improves no eligibility decision and no `ProviderResponse` that the occasion type does not already carry. It fails `IC-02`. A provider who needs it asks in a `clarification` response, which costs one interaction for the few requests that need it instead of one interaction for all of them. |
| `GC-10` | Budget minimum — required | **Not asked as a required numeric** | Optional band on `EventContext` | `QUALITY` | See §8. A required min/max pair anchors a negotiation before the provider has seen the request, asks most customers to invent a number for a service they have never bought, and **blocks nothing in `EligibilityDecision`** (`ADR-006`). **No friction ranking is asserted**: `docs/07-research/gigsalad-request-flow-capture.md` asserts neither wording nor required-versus-optional status and carries no usability measurement (`SRC-006` NOT RECEIVED). |
| `GC-11` | Budget maximum — required | **Not asked as a required numeric** | Optional band on `EventContext` | `QUALITY` | Same as `GC-10`. See §2.3. |
| `GC-12` | Additional details, free text | **Asked, optional, last** | `EventContext` free-text notes | `QUALITY` | Optional and never blocking. Classified as **possibly containing contact data regardless of policy** (`ADR-010`): it may never reach a notification body, an analytics export, or a search read path. |
| `GC-13` | Service specifics — sound, stage, equipment, song requests | **Asked for some archetypes only, as governed attributes** | Mobile-performer `CategoryAttributeDefinition`s, grouped, optional | `CATEGORY` / `QUALITY` | These are real and they are archetype-scoped. Governed as data, they are three optional questions for a performer request and zero questions for a cake request. |
| `GC-14` | Whether other services are already booked for the event | **Not asked** | — | — | It improves no decision this provider makes about this request. Its plausible purpose is cross-sell routing, which `WA-02` forbids. |
| `GC-15` | Planning stage — how far along the customer is | **Not asked. Deleted under `IC-02`** | — | — | It drives no eligibility, no ordering, and **no provider decision this document can name**. Its only provenance is this capture, and `IC-02` is absolute: a question that improves no decision and gates nothing is **deleted, not demoted**. Retaining it on *"it plausibly changes response priority"* was the demotion `IC-02` forbids. A provider who needs it asks in a `clarification`. |
| `GC-16` | Name | **Collected at verification, not in the composer** | `Account` display name | `PRE-SUBMIT` | `WA-05`. Composition is anonymous; identity is established at submit. Customer name is never publicly exposed. |
| `GC-17` | Email address | **Collected at verification, not in the composer** | Contact channel on the `Account` | `PRE-SUBMIT` | `WA-05` and `DB-12`. See `docs/04-ux/identity-and-verification.md`. |
| `GC-18` | Phone number — required | **Not required** | One contact channel is required; which channel is the customer's choice | `PRE-SUBMIT` | `DB-12` fixes the boundary at *a verified reachable channel*, not at *a phone number*. Requiring a second channel adds friction and a second sensitive field for no additional delivery guarantee. |
| `GC-19` | Create a password / create an account before the request is sent | **Account is established at submit, and is never required to browse, search, view a profile, or begin a draft** | `Account` (opaque, permanent, **not typed by role** — `ADR-004`) | `PRE-SUBMIT` | `WA-05`. An account wall in front of discovery destroys the acquisition hypothesis. |
| `GC-20` | "We will also send your request to similar providers" | **Superola does not do this** | — | — | See §2.3. `WA-02`: one deliberate recipient, one `ServiceRequest`, no automatic broadcast or fan-out. |
| `GC-21` | Suggested additional providers to add to the request | **Superola does not do this** | — | — | See §2.3. `WA-02` and `ADR-003`: the recipient is a **single reference, deliberately not a collection**. |
| `GC-22` | Start from a previous request, or start a new one | **Asked, in a different place and with a different meaning** | `RequestDraft` reuse, and the `eventGroupingHint` reseed path (§4, §5) | `PRE-SUBMIT` | Superola offers reuse **after** a request exists rather than as an opening question, because a first-time customer has nothing to reuse and the question costs them a step for nothing. Reuse always requires **deliberate recipient selection and fresh confirmation** (`ADR-003`) — a reused draft never becomes automatic routing. |
| `GC-23` | Preferred group or ensemble size | **Asked for one archetype only, as a governed attribute** | Mobile-performer `CategoryAttributeDefinition` | `CATEGORY` | Distinct from `GC-08` guest count: this is the size of the **provider's** ensemble, not the audience. It is genuinely load-bearing for mariachi, banda and norteño pricing, which is the owner-reported launch supply — and it is meaningless for every other archetype. It is the clearest single example of why the question set is archetype-governed data rather than one form. |
| `GC-24` | Whether the customer will cover travel or accommodation expenses | **Not asked in V1** | — | — | It is a **commercial term**, not a request constraint, and V1 has no place to put one: `EligibilityDecision` does not consider it (`ADR-006`), and a customer-asserted term with no transaction behind it is money-shaped language attached to nothing (`WA-03`, `DB-02`). Travel cost belongs in the provider's `PriceStatement` as an inclusion or exclusion, where `domain-model.md` already requires it to be stated. A provider who needs it earlier asks in a `clarification`. |

### 2.3 Three items Superola deliberately does not do, and why

These are not omissions pending a later release. They are decisions, and each has a named cost that Superola is choosing to pay.

| ID | Captured behavior | Superola's position | The argument | What Superola gives up |
|---|---|---|---|---|
| `GC-20` | Automatic fan-out of the request to additional providers | **Not in V1.** `WA-02` holds: the customer selects a provider, and exactly one `ServiceRequest` goes to that provider. | `ADR-003` rejected pre-generalizing the recipient as a collection of size one, for a reason that is not stylistic: **multi-provider matching is not reachable by relaxing the constraint.** It needs a separate routing concept carrying consent, recipient provenance, caps, response windows, closure, deduplication, and reroute. Pre-generalizing hides that entire cost inside a design that looks ready. `domain-model.md` §9 states the invariant in the same terms. Consent-based fan-out remains a preserved future branch (`DB-01`), and `RequestDraft` is the seam that keeps it cheap. | Volume. One request produces one response opportunity instead of five. **This is the single largest deliberate friction decision in the product**, and the honest counter-argument is that a customer who gets no response has one dead end instead of four live ones. The mitigation is `NoResponse` observability (`ADR-003`, `R-016`) plus a deliberate second request, not silent routing. |
| `GC-10`, `GC-11` | Budget minimum and maximum as a required pair | **Not required, and not a numeric pair.** Optional band, `QUALITY` class. | Three separate reasons, none of which is friction alone. **Eligibility:** `EligibilityDecision`'s six named inputs do not include budget, so a required budget blocks a submission on a field that changes no outcome (`ADR-006`). **Anchoring:** a number the customer invented before seeing a single price becomes the number the provider negotiates against, which is a worse outcome for the party the field was ostensibly protecting. **Honesty:** most customers do not know the price of a service they have never bought, so a required field manufactures an answer rather than collecting one. See §8. | A provider receives fewer requests carrying a budget signal, and some providers will decline to quote without one. That is a legitimate `clarification` response, and it is a per-request cost rather than a per-customer one. |
| `GC-21` | Suggesting additional providers to add to the request in-flow | **Not in V1**, and it is a distinct decision from `GC-20`. | Fan-out is a routing decision; in-flow suggestion is a **placement** decision, and V1 has exactly one placement basis. Every result slot carries `placementBasis` with the single value `organic` plus a disclosure marker and a ranking explanation. An in-composer provider suggestion is a result slot with no ranking explanation, presented at the moment of highest intent, and it is the natural first surface a sponsored inventory would colonize. `ADR-008` keeps that a reserved seam; `ADR-006` forbids `EligibilityDecision` from ever gaining a paid branch. | A plausible conversion lever. Its V1 replacement is honest and worse-converting: after a request is sent, the customer returns to results and selects again, deliberately (§5). |

---

## 3. The friction comparison — a design target, not a measured improvement

**The counting basis, stated before any figure, because the previous one was computed on an uneven basis.** A count taken net of identity is not comparable with a count taken gross of it. **The earlier "6–9 answers" figure was net of identity and is withdrawn**: it counted the two universal `PRE-SUBMIT` questions and the three blocking `CATEGORY` questions but omitted the contact channel (`IQ-12`), the display name (`GC-16`), and the proof of channel control (`DB-12`), all three of which §6.1 classifies as `PRE-SUBMIT` — the class it defines as **blocking submission**. Two figures are therefore stated separately, and neither substitutes for the other:

- **Composer figure** — the answers the composer itself asks. It measures the composition interaction, and it is the figure `FM-01` abandonment is read against.
- **Total-to-submit figure** — **every** blocking item the customer supplies before a `ServiceRequest` can be delivered, identity included. Nothing classed `PRE-SUBMIT` is excluded from it.

**Design target, mobile performer, restated on the honest basis:**

| Entry path | Carried in from discovery | Asked by the composer | Identity items at submit | **Total to submit** |
|---|---|---|---|---|
| **Discovery path** — `UX-01` → `UX-04` → `UX-05` → `UX-07`. The assumed path in every earlier count. | **3** — governed `Category`, governed `Place`, selected recipient `ServiceOffering` | **5** — 2 universal `PRE-SUBMIT` (`IQ-03` event type, `IQ-04` date) + 3 blocking `CATEGORY` (`MP-01`–`MP-03`) | **3** — contact channel (`IQ-12`), display name (`GC-16`), proof of channel control (`DB-12`, on `UX-09`) | **10–11** |
| **Direct-arrival path** — a search engine result or a shared link straight to `UX-05`, then `UX-07`. **This is the acquisition hypothesis**, not an edge case. | **0** — none of the three was ever collected | **up to 8** — the same 5, plus the governed `Place`, the recipient `ServiceOffering`, and its governed `Category` where the profile carries more than one. **Three answers longer.** | **3**, unchanged | **10–11** |

**Reading the table.** The total to submit is the same on both paths, because the request needs the same facts however the customer arrived. What changes is **how many of those facts the composer has to ask**, and that — not the total — is what composer abandonment measures. **`IC-04` still binds**: where discovery did collect an answer, re-asking it is a defect; the direct-arrival composer asks what was genuinely never collected.

**Why the total is a range and not a single number.** Eleven blocking items are supplied, of which **ten are answers** and one — the proof of channel control — is a proof rather than a question. Counted as answers only, the figure is **10**; counted as blocking interactions, **11**. Both are stated because neither is wrong and the difference is exactly the kind of accounting that produced the withdrawn figure.

Optional `QUALITY` questions are skippable and are not counted, because skipping them must not be a degraded path.

**Observed comparator, measured the same way.** The captured flow in §2 is **24 captured items, of which approximately twenty are presented to the customer as steps** (§2.2), and **four of those items are identity** — name (`GC-16`), email (`GC-17`), phone (`GC-18`), and account creation before the request is sent (`GC-19`). Superola's 10–11 likewise includes its three identity items. **Both sides are now gross of identity.** Several of the captured flow's remaining required fields are ones Superola classifies as `QUALITY` or does not ask at all.

**This is a design target. It is not a measured improvement, and it must never be written as one.**

- There is **no usability evidence** in this repository. `SRC-006` is NOT RECEIVED.
- There is **no traffic data**, so there is no funnel to compare against.
- The comparator is a **competitor's** flow observed once, not Superola's own prior baseline, and a lower step count is not by itself a better outcome — a shorter form that produces requests providers cannot answer is worse than a longer one.
- Answer count is a proxy. The outcome that matters is **completed, answerable requests**, not fewer questions.

**The measurement that would confirm it**, and which V1 must instrument before any comparative claim is made:

| ID | Measurement | Why it is the right instrument |
|---|---|---|
| `FM-01` | Per-stage composer abandonment: the named stage at which a `RequestDraft` was last edited before abandonment, **segmented by entry path** | Locates friction at a stage rather than attributing it to the composer as a whole. Required by `R-022` regardless of this comparison. **The segmentation is not optional**: the direct-arrival composer is three answers longer than the discovery-path composer, so an unsegmented abandonment figure averages two materially different interactions and hides the one the acquisition hypothesis depends on. |
| `FM-02` | Answers-to-submit distribution per `CategoryArchetype`, **composer answers and identity items counted separately**, blocking and optional counted separately | Tests the composer figure and the total-to-submit figure as the two distinct quantities §3 defines, per archetype, rather than as a single product-wide average that no customer experiences. |
| `FM-03` | Draft-created to submitted rate per archetype, **segmented by entry path** | The composer's own conversion, isolated from verification. Unsegmented, it cannot tell a composer problem from an entry-path problem. |
| `FM-04` | Verification abandonment, instrumented **separately** from composer abandonment | The fixes differ. Conflating them makes both unfixable. See `docs/04-ux/identity-and-verification.md`. |
| `FM-05` | `clarification`-response rate per archetype, and which field the clarification concerns where it is inferable | The direct test of whether the shorter form removed a question the provider actually needed. **A rising clarification rate is the failure signal for this design.** |
| `FM-06` | `NoResponse` rate per archetype (`ADR-003`, `R-016`) | Distinguishes "the request was bad" from "the provider was absent". Without it, `FM-05` is unreadable. |

Until `FM-01`–`FM-06` have run against real traffic, the only defensible statement is the one made here: **5 composer answers on the discovery path and up to 8 on the direct-arrival path, against a total to submit of 10–11 including identity, is the target the design is built to hit — and the comparator is a competitor artifact recorded for contrast, not a baseline.**

---

## 4. `RequestDraft` — the customer-visible model

This section describes what the customer experiences. It designs no storage. `RequestDraft` is a first-class aggregate owned by Demand (`ADR-003`); its persistence mechanics are P02's and are not restated, refined, or extended here.

### 4.1 When a draft begins, and where it lives

```text
first answered composer question → DRAFT EXISTS, local and anonymous
  → (customer signs in voluntarily) → promoted to server RequestDraft
  → (customer submits under WA-05) → Account established → promoted to server RequestDraft
  → submit with one chosen recipient → ServiceRequest PendingVerification → V1 CONTINUES
```

| ID | Rule | Detail |
|---|---|---|
| `RD-01` | A draft **begins at the first answered composer question** | Not at page load, not on entering the composer. An abandoned empty composer is not a draft, and counting it as one corrupts `FM-03`. |
| `RD-02` | At that moment the draft is **local and anonymous** | Browser-local. `RequestDraft` is `Account`-scoped, and under `WA-05` no `Account` exists yet. This is the reason, and it is a domain constraint rather than a performance choice. |
| `RD-03` | **Server persistence begins when an `Account` exists** | Two triggers, and only two: the customer **signs in or creates an account voluntarily** at any earlier point, or the customer **submits**, which establishes an `Account` under `WA-05`. At that moment the local draft is **promoted** to a server `RequestDraft`. There is no third trigger, no background upload, and no silent promotion. |
| `RD-04` | **Account creation is not required to create a draft, browse, search, or view a profile** | Stated positively and without qualification. A login wall in front of any of these four is a defect, not a configuration. |
| `RD-05` | The draft has **no recipient** | A `RequestDraft` is never addressed and is **never visible to any provider** — not to a provider the customer is currently viewing, not in aggregate, not as a signal. |

### 4.2 Resumability

| ID | Situation | Behavior |
|---|---|---|
| `RD-06` | Same device, anonymous, returns later | The draft resumes from local state at the stage it reached. The customer is told, plainly, that the draft is on this device only and has not been sent to anyone. |
| `RD-07` | Different device, anonymous | **The draft is not available**, and the interface says so honestly rather than presenting an empty composer as though nothing was ever started. There is no cross-device anonymous identifier, and inventing one would create a tracking identity the architecture deliberately does not have. |
| `RD-08` | Any device, signed in | Server-persisted drafts are listed on `UX-13` (My drafts) and resume anywhere the `Account` is signed in. |
| `RD-09` | Local storage is cleared or unavailable | The composer must remain usable with no local persistence: each step posts to the server and re-renders. `I-2` (the composer step controller) is an enhancement over that path, never a precondition for it. |

### 4.3 Changing the category, and changing the event type

These two behave differently, and the difference is not arbitrary.

| ID | Change | Behavior | Why |
|---|---|---|---|
| `RD-10` | **Category change** | Universal `EventContext` answers are **retained**. Category-specific answers belonging to the previous category are **dropped**. The customer sees an **explicit before-the-fact warning naming exactly what will be lost** — not "some answers may be lost", but the named questions. The dropped answers are **retained in session** so an immediate **undo** restores them. | Category-specific answers are `CategoryAttributeDefinition`s scoped to the previous archetype. Carrying a mobile-performer set-length answer into a cake request would produce a request field the recipient's archetype cannot interpret. The warning and the undo exist because the customer's mental model is *"I changed my mind about one thing"*, and silent destruction of five answers violates it. |
| `RD-11` | **Event type change** | **Nothing is dropped.** | Event type is a **universal** field on `EventContext` and **never gates a category-specific answer**. A quinceañera and a wedding ask the mobile performer the same governed questions. If a future `CategoryAttributeDefinition` were made conditional on event type, this rule would be violated, and the correct response is to reject that definition, not to relax the rule. |

### 4.4 Submitting

| ID | Rule | Detail |
|---|---|---|
| `RD-12` | Submit requires **deliberate recipient selection** | A draft becomes a `ServiceRequest` only by the customer choosing one `ServiceOffering`. No default recipient, no pre-selection, no "send to the top result". |
| `RD-13` | On submit the request enters **`PendingVerification`** | **Durable but invisible to the provider.** The provider sees nothing before `Delivered` and never sees an `Abandoned` request. This state already exists in P02 for exactly this purpose (`domain-model.md` §5.2, `DB-12`). |
| `RD-14` | **The customer's work is never lost on verification abandonment** | This is the whole reason `PendingVerification` is durable. It is also why verification abandonment is *measurable* rather than merely regrettable. |
| `RD-15` | **Repeat submission must not create a second request** | Idempotent. Re-clicking, refreshing, resending a verification message, or returning through a link resolves to the **same** `ServiceRequest` and shows its current state — never a second confirmation screen and never a duplicate in the provider's inbox (`ADR-003`, `R-013`). |
| `RD-16` | **Draft reuse requires deliberate selection and fresh confirmation per recipient** | A reused draft never becomes automatic matching (`ADR-003`). Sending the same draft to a second provider is a second deliberate act with its own review-and-send step, its own confirmation, and its own `ServiceRequest`. **There is no "send to all" affordance, and adding one would silently implement `DB-01`.** |

### 4.5 What is measured

`draft created` is a named measurable transition in the Demand family. The composer must also emit, at minimum: the named stage at abandonment (`FM-01`); draft-to-submit conversion per archetype (`FM-03`); category-change events with whether the warning was accepted and whether undo was used (which is the direct test of whether `RD-10`'s warning is working); and the answer-count distribution (`FM-02`). None of these may carry request free text, which is classified as possibly containing contact data and can never reach an analytics export (`ADR-010`, `Q-033`).

### 4.6 Local draft privacy contract

`R-050` requires the anonymous, browser-local draft to be bounded, visibly discardable, cleared on promotion, and expiring. This section makes those four properties explicit rather than leaving them implicit in `RD-01`–`RD-05`.

| ID | Rule | Detail |
|---|---|---|
| `RD-17` | **Local storage holds structured answers only** | The anonymous browser-local draft (`RD-02`) persists structured composer answers only — governed references, dates, counts, selected options. `GC-12`'s optional free-text notes field is never written to local storage while the draft is anonymous: it is held in page memory for that composer session only and is lost on navigation away or reload, exactly like any unsaved form field, until the customer signs in or submits and the whole draft — including any free-text notes present at that moment — promotes under `RD-03`, `RD-19` and the existing `ADR-010` request-free-text handling. |
| `RD-18` | **Visible discard** | Wherever a local draft exists (composer or `UX-13`), the customer sees an explicit "discard this draft" action. Discard clears the local store immediately and returns the customer to an empty composer. It is a distinct control from simply navigating away. |
| `RD-19` | **Clear-on-promotion** | The moment a local draft promotes to a server `RequestDraft` (`RD-03`), the local copy is cleared from browser storage. The server aggregate is the sole record after promotion; no local duplicate survives it. |
| `RD-20` | **Local expiry** | An anonymous local draft that is never promoted expires after a bounded window of inactivity and is cleared automatically. **The window's length is `POLICY PENDING`** — P04 requires the expiry mechanism to exist and does not set the duration, the same posture already taken for the `NoResponse` window and the `RequestIntake` decay window. |
| `RD-21` | **Whether this contract requires disclosure, and whether anonymous local storage is acceptable at all** | **`OPEN` — `Q-038`, owned by David + counsel.** `RD-17`–`RD-20` design the contract without resolving `Q-038`; P04 does not assume an answer either way. If `Q-038` is answered "no", the fallback is `RD-09`'s no-local-persistence path — the composer already works without local storage. |

---

## 5. `EventType` is not `ServiceCategory`

### 5.1 The distinction

An **event type** is an occasion: a wedding, a quinceañera, a corporate holiday party. A **service category** is something a provider sells: mariachi, catering, photography. One event routinely needs many independent services from many independent providers, and a provider sells one service across many occasions. They are orthogonal, and conflating them is the defect that produces both a bloated universal form and a taxonomy nobody can govern.

In the model: event type is an attribute of `EventContext`, a **value object owned by the request**. `Category` is a governed reference that selects an archetype and therefore a question set. Event type is `PRE-SUBMIT` and universal; category is `DISCOVERY` and is the primary discovery key.

**Event type is never a search filter.** Filtering supply by occasion would assert that a provider is eligible for weddings and ineligible for quinceañeras, which is an eligibility rule the supply model does not have and which `EligibilityDecision`'s six named inputs do not contain (`ADR-006`).

### 5.2 Why the V1 request is single-service

One `ServiceRequest` addresses exactly one `ServiceOffering`, immutably after delivery. A multi-service request would need a recipient set, and a recipient set is not a bigger version of a single recipient — it requires routing, consent, recipient provenance, caps, a shared or per-recipient response window, closure semantics for siblings, deduplication, and reroute (`ADR-003`, `WA-02`, `domain-model.md` §9). None of that is V1.

### 5.3 The V1 path for another service for the same event

```text
existing request (open or closed) → "Request another service for this event"
  → NEW RequestDraft seeded from the prior EventContext
  → carries the customer-declared eventGroupingHint
  → customer picks a NEW Category
  → answers only that archetype's CATEGORY questions
  → deliberately selects a NEW recipient
  → second ServiceRequest → V1 ENDS
```

| ID | Rule | Detail |
|---|---|---|
| `EV-01` | The action seeds a **new `RequestDraft`**, pre-filled from the previous request's `EventContext` | Date, occasion, location constraint, and guest-count indication carry across; every seeded value is visible and editable before submit, because the customer may be planning a different day of the same wedding. |
| `EV-02` | It carries the customer-declared **`eventGroupingHint`** | Already in P02's `EventContext` precisely so a future `Event` can be reconstructed without re-collecting data. |
| `EV-03` | The customer answers **only the new archetype's `CATEGORY` questions** | This is the whole payoff of `IM-02`. Requesting a cake after a mariachi asks about servings and a delivery window, and asks nothing about set length or stage power. |
| `EV-04` | The customer **selects the new recipient deliberately** | `WA-02`. **No multi-service RFQ, no fan-out, no automatic routing, no suggested bundle.** |
| `EV-05` | **This is the same aggregate P02 already designed, used twice.** No new entity. | `RequestDraft` → `ServiceRequest`, run a second time. |

### 5.4 The grouping is presentational

Requests sharing an `eventGroupingHint` may be **displayed together** on `UX-11` (My requests) under a customer-supplied label. That is the entire feature.

**This is a light presentational grouping, not an `Event` aggregate. No `Event` box is drawn in any P04 diagram, and no P04 document may introduce one.** An `Event` aggregate would import an unsolvable *"are these two requests the same wedding?"* identity problem with no V1 payoff (`domain-model.md` §1.6). The grouping carries **no** behavior: it does not close sibling requests, does not share a response window, does not aggregate offers, does not produce a combined total, and does not route anything. The point at which an `Event` aggregate becomes justified is a future booking under `DB-02`, which is `FUTURE` (`WA-03`).

---

## 6. Classification of intake questions

### 6.1 The five classes

| Class | Definition |
|---|---|
| `DISCOVERY` | Needed to produce results at all. Without it there is nothing to show and no provider to address. Collected before the composer opens. |
| `PRE-SUBMIT` | Needed before a `ServiceRequest` may be delivered. Universal across archetypes. Blocks submission. |
| `QUALITY` | Optional. Improves the `ProviderResponse` but **blocks nothing**. Skipping it must never produce a degraded or warned path. |
| `CATEGORY` | Required, optional, or absent **depending on the `CategoryArchetype`**. Governed as a `CategoryAttributeDefinition`. This is the class that makes `IM-02` possible and `IM-01` impossible. |
| `NOT-V1` | Not V1. Named so that its absence is a decision rather than an oversight. **Deliberately not called `FUTURE`:** `FUTURE` is a governed *evidence label* (`AGENTS.md`), and reusing it as a question *class* in the same backticked styling makes it impossible to tell whether a row is labelled `FUTURE` or classed `FUTURE`. |

### 6.2 The master table

| ID | Field | Class | Note | Justification |
|---|---|---|---|---|
| `IQ-01` | Service need (`Category` / archetype) | `DISCOVERY` | Governed reference, **never free text as the key**. | It selects the archetype, and therefore the entire remaining question set. Free text as the key would make eligibility non-deterministic and language-dependent; free text remains available as optional **secondary** matching over governed labels and synonyms. |
| `IQ-02` | Location (`Place` at a stated granularity) | `DISCOVERY` | Resolved against the governed `Place` list; **zero vendor calls**. | It is a direct input to `LocationEligibility`, which is one of `EligibilityDecision`'s six named inputs. `ADR-019` Level 1 forbids per-search geocoding and forbids free-text customer geocoding. |
| `IQ-03` | Event type (occasion) | `PRE-SUBMIT` | Universal; part of `EventContext`; **never a search filter**. | Cheap, universal, and materially changes what a provider proposes. It is not a filter because no eligibility input corresponds to it (§5.1). |
| `IQ-04` | Event date / window / `flexible` | `PRE-SUBMIT` | `flexible` is a first-class valid answer. **Request context only, never a filter, never a system claim.** | `V1 has no availability model` (`ADR-005`). Filtering on date would present the platform as knowing who is free, which is exactly the claim `WA-01` and `G-06` forbid. `flexible` is first-class because forcing a date on an undecided customer manufactures a false fact that then drives `Lapsed`. |
| `IQ-05` | Approximate start time | `CATEGORY` | Required for performer and professional; irrelevant for a cake. | It changes staffing, travel, and price for archetypes where the provider is physically present for a bounded window. |
| `IQ-06` | Duration | `CATEGORY` | Performer, professional, transportation. | It is the quantity being purchased for those archetypes. A fixed venue expresses this as an access window; delivery and food does not have it. |
| `IQ-07` | Guest count | `CATEGORY` | **Blocking** for fixed venue and delivery and food; `QUALITY` for performer. | Venue capacity and food quantity are hard feasibility constraints. For a performer it changes the quote and gates nothing — which is the clearest single illustration of why one universal requirement level is wrong. |
| `IQ-08` | Budget indication | `QUALITY` | Optional. **Never required.** See §8. | Not an `EligibilityDecision` input, and requiring it anchors a negotiation against the customer who was made to guess. |
| `IQ-09` | Planning intent (how far along) | **Deleted** | **Not asked in V1.** The ID is retained so its absence is a recorded decision, not an oversight. | **`IC-02` applied as written.** No provider decision it changes could be named, and *"it plausibly changes response priority"* is a demotion, not a justification. Its only provenance was the competitor capture (`GC-15`), which asserts nothing about wording or requirement level. If a named provider decision is ever supplied, it returns as a new row with that decision stated and `FM-05` committed **before** launch, not as a post-hoc check. |
| `IQ-10` | Free-text notes | `QUALITY` | **Classified as possibly containing contact data.** | Optional and last. It may never appear in a notification body, an analytics export, or a search read path (`ADR-010`). It is also the field whose export is `Q-033`-blocked, which is why it is the field assisted intake cannot touch today (§9). |
| `IQ-11` | Lead time feasibility | `CATEGORY` | Delivery-and-food eligibility input; **derived from the date, not asked**. | The customer already answered the date. Asking "is this enough notice?" asks the customer to evaluate the provider's constraint on the provider's behalf. Note the consequence: a `flexible` date makes lead time — and therefore eligibility — `undetermined` for this archetype, and that uncertainty must be **surfaced, never coerced** (§7.3). |
| `IQ-12` | Contact channel | `PRE-SUBMIT` | **Collected at verification, not in the composer** (`WA-05`). | Composition is anonymous. `DB-12` requires a verified reachable channel before delivery, not before composition. See `docs/04-ux/identity-and-verification.md`. |
| `IQ-13` | Indoor/outdoor, stage area, equipment, song requests | `CATEGORY` | Mobile-performer attributes; **governed `CategoryAttributeDefinition`s, not hand-coded**. | Each changes what the performer brings or charges. They exist as governed rows so a new performer-shaped category inherits them without code. |
| `IQ-14` | Menu / dietary, delivery versus full service, service window | `CATEGORY` | Delivery and food. | Fulfilment mode changes the archetype's own service-area semantics — a drop-off and a staffed service are different products with different eligibility. |
| `IQ-15` | Origin / destination, passenger count, itinerary | `CATEGORY` | Transportation — **and see §7.5**. | Required by the archetype's two-endpoint eligibility, which is exactly why the archetype is recommended out of the launch cohort. |
| `IQ-16` | Multi-service in one request ("also need a DJ") | `NOT-V1` | Not in one request. **The V1 path is §5.3.** | A recipient set is a routing concept, not a larger recipient (`ADR-003`, `WA-02`). |
| `IQ-17` | Fan-out to similar providers | `NOT-V1` | `WA-02`, `DB-01`. | Consent-based fan-out is a preserved branch, and `RequestDraft` is the seam that keeps its reversal cost low. |

---

## 7. Category-specific question sets by archetype

**The governing rule, stated before the archetypes so that no section below reads as a form specification:**

> Every question in §7.1–§7.5 is a **governed `CategoryAttributeDefinition`** — operator-governed data with an identifier, a class, a requirement level, an answer shape, and governed labels. **None of them is a hand-built form.** The composer reads the selected `Category`'s archetype and renders its definitions; it contains no per-category branch and no category name in a condition. Adding a Category is a **governance act** — governed data plus governed attribute definitions — and it **adds no screen and requires no application-shell redesign**. Adding an *archetype* is an engineering act, and there are five (`ADR-007`). Provider input on categories arrives as a `CategoryProposal` reviewed on `UX-30`; a provider is **never** able to write a `CategoryAttributeDefinition`.

The sets below are **representative proposals** showing what the composer would ask, not the governed content itself. The governed content is a P05 authoring task under operator governance.

Counts follow §3's convention and **are stated on the discovery path**, where 3 answers are carried in (`Category`, `Place`, selected recipient `ServiceOffering`). Composer answers are counted separately as **blocking** and **optional**. **Every count below adds the 3 identity items** — contact channel, display name, and proof of channel control — to reach the total to submit, because §6.1 classes all three `PRE-SUBMIT` and `PRE-SUBMIT` blocks submission. **On the direct-arrival path the composer asks up to 3 more** and the total is unchanged (§3).

### 7.1 Mobile performer

Mariachi, band, DJ. **The modal launch archetype.**

**Service-area and eligibility semantics that make these questions necessary.** The provider travels to the customer. The `ServiceAreaDeclaration` is a base plus a travel radius with an explicit unit, and/or a named-market list, plus travel-conditions text. `LocationEligibility` is `eligible` when the service location resolves within the radius of the base **or** its `Place` is in the declared markets; it is `undetermined` when the base coordinate precision is insufficient, or when the customer supplied only a `Place` with no usable representative point. This is why `IQ-02` must be a governed `Place` at a usable granularity and cannot be free text — and why the publication gate is archetype-aware about base-coordinate precision (`domain-model.md` §5.1). Because the provider travels for a bounded window, **time and duration are what is being purchased**, and because the provider brings equipment into an unknown physical space, **setting is a feasibility question and not a preference**.

| ID | Question | Blocking or quality | Why at that level |
|---|---|---|---|
| `MP-01` | Approximate start time | **Blocking** | Travel, staffing, and overlap with other engagements all depend on it. A performer cannot form an offer without it. |
| `MP-02` | Duration or set length | **Blocking** | It is the quantity purchased. |
| `MP-03` | Setting — indoor / outdoor / both / unknown | **Blocking** | Outdoor changes power, weather contingency, equipment protection, and price. `unknown` is an accepted answer and is **not** the same as leaving it blank. |
| `MP-04` | Performance area and power availability | Quality | Improves the offer; a provider who needs certainty asks a `clarification`. Grouped as one question with two parts, not two questions. |
| `MP-05` | Who provides sound / PA | Quality | Materially changes price when answered; commonly unknown to the customer, so requiring it would manufacture an answer. |
| `MP-06` | Song or repertoire requests | Quality | Free text. Optional, last, and classified as possibly containing contact data. |
| `MP-07` | Guest count | Quality | `IQ-07`. Informs scale and volume; gates nothing. |

**Answer count.** Composer: 2 universal `PRE-SUBMIT` (`IQ-03` event type, `IQ-04` date) + 3 blocking `CATEGORY` (`MP-01`–`MP-03`) = **5 composer answers**. Plus 3 carried in from discovery and 3 identity items at submit = **10–11 blocking items to a complete submittable request** (§3). `MP-04`–`MP-07` and the budget band are optional and skippable, and skipping them produces no warning and no degraded path.

### 7.2 Fixed venue

Salón, hall, event space. **The archetype where the customer travels to the provider** — the inverse of every other one.

**Service-area and eligibility semantics.** The service area **is** the venue: `fixed-at-base`. `LocationEligibility` is `eligible` when the customer's location constraint resolves to a `Place` containing or adjacent to the venue. Critically, **distance here measures the customer's travel, so it is an informational and sort factor and not a hard eligibility bar** — a customer will drive forty minutes to the right room and will not drive four minutes to the wrong one. `undetermined` arises when the venue `Place` is unresolved or its coordinates are imprecise. The venue's binding constraint is not geography but **capacity**, which is why guest count is blocking here and quality for a performer, and why the composer's questions concentrate on physical fit rather than on travel.

| ID | Question | Blocking or quality | Why at that level |
|---|---|---|---|
| `FV-01` | Guest count | **Blocking** | Capacity is a hard feasibility constraint. A venue that cannot hold the party cannot make an offer of any kind. |
| `FV-02` | Access window — arrival through departure, including setup and teardown | **Blocking** | A venue sells a block of time, not a performance duration. This is `IQ-06` expressed in the archetype's own terms. |
| `FV-03` | Seated, standing, or mixed | **Blocking** | Capacity is not one number. A room that holds 250 standing may hold 140 seated, so the answer to `FV-01` is uninterpretable without it. |
| `FV-04` | Catering arrangement — in-house, external, or none | Quality | Many venues constrain this; asking early avoids a wasted exchange, but a venue can and does answer it in a `clarification`. |
| `FV-05` | Alcohol service | Quality | Licensing and policy vary; it changes price and permissibility but rarely eligibility. |
| `FV-06` | Accessibility or access needs | Quality | Optional, and must never be phrased so that leaving it blank implies no needs. |

**Answer count.** 2 universal + 3 blocking (`FV-01`–`FV-03`) = **5 composer answers**; plus 3 carried in and 3 identity items = **10–11 blocking items in total**. Same shape as the mobile performer, different questions.

**Archetype-correct framing is a UX requirement, not copy.** The venue result and profile must present *where it is and how far it is from you*; the performer profile must present *how far it will travel to you*. `domain-model.md` §4.2 names conflating these two as a common marketplace defect, and it is a defect that lives entirely in the interface, since the predicate underneath is already correct.

### 7.3 Delivery and food

Catering, cake, dessert tables.

**Service-area and eligibility semantics.** A production base plus a delivery zone expressed as a radius, a `Place` list, or a postal-prefix list, **plus a declared lead time**. `LocationEligibility` is `eligible` when the service location is inside the delivery zone **and** the requested date is at or beyond the declared lead time. `undetermined` when the zone is expressed only as free text, when lead time is undeclared, or **when the event date is `flexible`** — the one archetype where a first-class, entirely reasonable universal answer produces uncertainty. This is a two-part predicate over geography **and** time, which is what makes fulfilment mode and service window blocking rather than descriptive.

| ID | Question | Blocking or quality | Why at that level |
|---|---|---|---|
| `DF-01` | Guest count or servings | **Blocking** | It is the quantity. No offer exists without it. |
| `DF-02` | Fulfilment mode — delivery, drop-off, or staffed service | **Blocking** | It changes the archetype's own service-area meaning: a drop-off and a staffed service are different products with different zones, different staffing, and different prices. |
| `DF-03` | Required delivery or service window | **Blocking** | Distinct from the event start time. Food arrives before the event, and the window is the operational constraint. |
| `DF-04` | Menu preferences and dietary restrictions | Quality | Free text or a governed multi-select. Optional; a caterer resolves it in conversation. |
| `DF-05` | Serving needs — equipment, staff, on-site setup | Quality | Refines `DF-02` where the customer knows the answer. |

**Lead time is never asked.** It is derived from `IQ-04` (`IQ-11`).

**The `flexible`-date consequence must be surfaced, not coerced.** When the customer answers `flexible` and the recipient archetype is delivery and food, the resulting `LocationEligibility` is `undetermined`. The customer is shown that uncertainty in words — that the provider will need to confirm whether the timing works — and is **never** silently reclassified as ineligible, never silently dropped from results, and never presented as a confirmed match. Coercing `undetermined` into either certainty is prohibited (`ADR-019`, `ADR-006`).

**Why this does not reopen `G-06`.** `IQ-04` states the general rule: the requested date is request context, never a filter and never a system claim, because `V1 has no availability model` (`ADR-005`) and a date filter would assert one — exactly the claim `WA-01` and `G-06` forbid. The delivery-and-food `LocationEligibility` predicate above does not violate that rule; it is a **narrower, different computation**. It tests whether the provider's own declared production-and-delivery constraint (lead time) is logistically satisfiable by the requested date — a deterministic fact about the provider's declared process, not a claim about whether the provider is free. Clearing this predicate means only that the request is logistically eligible to reach this provider; it says nothing about whether the provider will accept, and the provider still resolves actual feasibility inside the request exchange, exactly as every other archetype does under `WA-01`. **`flexible` producing `undetermined` — rather than the predicate being skipped or defaulted to `eligible` — is what keeps this a logistics filter and not an availability claim**: an unanswered date cannot be run through a deterministic feasibility test, so the honest result is uncertainty, shown to the customer, not a guess.

**Answer count.** 2 universal + 3 blocking (`DF-01`–`DF-03`) = **5 composer answers**; plus 3 carried in and 3 identity items = **10–11 blocking items in total**.

### 7.4 Mobile professional

Photographer, decorator, makeup artist, planner.

**Service-area and eligibility semantics.** As mobile performer — base plus radius and/or named markets — **plus an optional setup or delivery constraint**, and eligibility adds **setup-window feasibility where the provider declared one**. That addition is the whole difference: a decorator who needs four hours of access before guests arrive is not eligible for a request whose only access window is thirty minutes, regardless of distance. This is why the number of service locations is blocking here and does not exist for the performer: a photographer covering a getting-ready location, a ceremony, and a reception is buying travel between them, and the setup-window predicate has to be evaluated against each.

| ID | Question | Blocking or quality | Why at that level |
|---|---|---|---|
| `PR-01` | Approximate start time | **Blocking** | As `MP-01`. |
| `PR-02` | Coverage duration | **Blocking** | The quantity purchased. |
| `PR-03` | Number of service locations, and whether they differ from the main event location | **Blocking** | It is the direct input to setup-window and travel feasibility, which is this archetype's added eligibility term. |
| `PR-04` | Setting — indoor / outdoor / both / unknown | Quality | Relevant to lighting and equipment; rarely disqualifying. |
| `PR-05` | Expected deliverables or scope | Quality | Governed multi-select where the category supports one, free text otherwise. |
| `PR-06` | Guest count | Quality | Informs scale; gates nothing. |

**Answer count.** 2 universal + 3 blocking (`PR-01`–`PR-03`) = **5 composer answers**; plus 3 carried in and 3 identity items = **10–11 blocking items in total**.

### 7.5 Transportation and route

Party bus, shuttle, limousine. **Modelled, and recommended out of the launch cohort.**

**Service-area and eligibility semantics.** A depot `Place` plus an operating or permit area as a `Place` list, optionally with an origin-destination corridor. `LocationEligibility` is `eligible` when **both** endpoints resolve inside the operating area, or when the origin is inside and the destination lies in the corridor; it is `undetermined` when only one endpoint was supplied, or when permit jurisdiction is unknown.

| ID | Question | Blocking or quality | Why at that level |
|---|---|---|---|
| `TR-01` | Origin `Place` | **Blocking** | Half of a two-endpoint predicate. |
| `TR-02` | Destination `Place` | **Blocking** | The other half. Supplying only one leaves eligibility `undetermined` by construction. |
| `TR-03` | Pickup time | **Blocking** | The operational commitment. |
| `TR-04` | Passenger count | **Blocking** | Vehicle capacity is a hard constraint. |
| `TR-05` | One-way, return, or hourly charter | **Blocking** | It changes which predicate applies and what is being priced. |
| `TR-06` | Intermediate stops or itinerary | Quality | Refines the corridor; free text or repeated `Place` entries. |
| `TR-07` | Duration, for hourly charter | `CATEGORY`, conditional on `TR-05` | The one genuinely conditional attribute in this set. |

**Answer count. 2 universal + 5 blocking = 7 composer answers, against 5 for every other archetype — and that is a finding, not a rounding error.** Plus 3 carried in and 3 identity items, the total is **12–13 blocking items**, two above every other archetype.

**§5.3.1 — the transportation caveat, and P02's argument.**

P02 states it directly: **transportation's route-corridor semantics do not reduce to a containment predicate** (`domain-model.md` §4.2). Every other archetype's eligibility is *"does this point fall inside this declared area"* — one point, one area, one boolean, evaluated identically for a performer's radius, a venue's place, a caterer's zone, and a professional's radius. Transportation needs **two** endpoints plus a **path between them**, and a corridor is not an area a customer can be inside. P02 argues this is a reason to **exclude transportation from the launch archetype set**, not a reason to adopt heavier search machinery. **P04 agrees, and records three independent UX confirmations of the same conclusion:**

1. **The discovery step itself is ambiguous.** Superola's primary discovery axis is one governed `Category` and one governed `Place`. A route has two places and no single one of them is *the* place, so the customer's very first interaction — the one that produces results at all — does not express what they need. Fixing that means a second location control on the primary search surface, for one archetype, on the highest-traffic public page.
2. **The composer carries two more blocking answers than any other archetype — 7 against 5** — and the excess is entirely blocking. There is no `QUALITY` question to demote and no reasonable consolidation, because each of `TR-01`–`TR-05` is an input to the predicate or to capacity.
3. **`undetermined` is the common case rather than the exception**, because permit jurisdiction is frequently unknown and a partially specified route is a normal way for a customer to start. An archetype whose usual eligibility answer is *"we cannot tell"* is the worst possible archetype to launch a marketplace's eligibility model on.

**Disposition: transportation is modelled in `ADR-007` and stays modelled. It is recommended out of the launch cohort, and the UX is validated against the other four archetypes.** Removing it from launch removes zero model entities and zero engineering work already done — it removes only governed category rows.

### 7.6 What the archetype set proves

**Four is a P04 recommendation, and the approved baseline is one.** `docs/05-roadmap/mvp-scope.md` fixes the bounded envelope at **one owner-approved geography, one production locale, and "the smallest coherent category archetype"**, with cohorts added only through explicit gates. P04 designs against four archetypes and **recommends** four; it does not inherit four, and it does not treat four as approved. **`G-04` is PARTIAL** in `docs/02-architecture/decision-branches.md` — the archetype set is settled, the launch cohort is not. If the owner holds the cohort at one archetype, the governed category-question system is over-built against the approved envelope and must be re-argued before it is built.

The **archetype validation set** for P04 is representative, not exhaustive: **Mobile performer · Fixed venue · Delivery and food · Mobile professional.** These four were chosen because they exercise the interesting variation rather than because they are the four largest: the provider travels (performer, professional), the customer travels (venue), the product travels (delivery and food); guest count is blocking in two and quality in one; eligibility is one-part in three and two-part in one; and `undetermined` arises from three different causes across the set.

**All four land at 5 composer answers, and at 10–11 blocking items to a submittable request, with different questions.** That is the claim `IM-02` is making: not that the composer is short, but that it is *proportional*, and that proportionality survives archetypes with genuinely different constraint shapes.

**The extensibility test, stated as an obligation:** the UX must accept a new Category with **no shell redesign**. A new Category is governed data plus governed `CategoryAttributeDefinition`s; the composer, the profile, and the results surfaces render it without a new screen, a new route, a new layout, or a code change. **If a proposed new Category cannot be added without a screen, the correct conclusion is that it needs a sixth archetype — an engineering act with an ADR — not that the composer needs a special case.**

---

## 8. Budget

| ID | Rule | Detail |
|---|---|---|
| `BU-01` | Budget is **optional** | It is `QUALITY` (`IQ-08`). It never blocks a submission, and skipping it produces no warning, no reduced-quality label, and no different path. |
| `BU-02` | Budget is expressed as a **band**, never a required numeric pair | A governed set of ranges with an explicit currency, plus an explicit *"I'm not sure yet"* option that is a real answer rather than an empty field. |
| `BU-03` | **Rationale, stated in full** | Three structural arguments, and **no friction claim**. A required minimum and maximum **produces anchoring that the provider then negotiates against** — a number the customer invented before seeing any price becomes the ceiling. And it **blocks nothing in the eligibility model**: budget is not among `EligibilityDecision`'s six named inputs, and `ADR-006` forbids that set from growing a paid or price branch. A required field that changes no outcome is friction with no compensating benefit. **What is deliberately not claimed:** that this is the highest-friction question anywhere, in the captured flow or in Superola. `docs/07-research/gigsalad-request-flow-capture.md` is recorded at **step-shape granularity** and states in its own terms that it asserts neither the wording nor the required-versus-optional status of any item (`GC-10`, `GC-11`) and carries **no friction, abandonment, or usability measurement**; no usability test has been run and `SRC-006` is NOT RECEIVED. Calling any captured question the highest-friction one would be a fabricated usability finding, which §7.12 of `docs/04-ux/design-canon.md` and `AGENTS.md` both forbid. The three arguments above stand without a friction ranking. |
| `BU-04` | Where a customer supplies it, it reaches the provider **inside the authenticated request surface only** | **Never in a notification body.** Budget is on `ADR-010`'s forbidden list alongside event address, event date, guest count, offer amounts, request free text, and any conversation content. A notification body escapes the platform's access control permanently. |
| `BU-05` | Currency is **always explicit** | Every monetary value carries its currency (`PriceStatement`, `domain-model.md` §1.6). No implicit currency, ever, and no bare `$` on an owner-facing artifact. |
| `BU-06` | Budget is **never publicly exposed** | It appears on no public profile, no result slot, and no search surface. |

---

## 9. AI-assisted intake — product disposition

**This section is P04's answer to `docs/03-technology/ai-evaluation.md` §8.1, which explicitly assigned the decision to P04. It reverses nothing in §10 of that document, and it selects no model, no provider, and no vendor.**

### 9.1 The verdict

> **The experiment is UX-worthy in principle, and it is not worth running in V1.**
> Disposition: `FUTURE`. Provenance: `TECHNICAL_DISCOVERY`.

`ai-evaluation.md` §8.1 states the condition plainly: *"If P04 does not find an interaction problem worth testing, the experiment does not happen."* **P04 did not find one.**

### 9.2 The reasoning, stated honestly

| ID | Reason | Detail |
|---|---|---|
| `AI-01` | **There is no baseline to measure against** | The value of prefill is measured against a guided composer, and the guided progressive composer **does not exist yet**. `ai-evaluation.md` §8.1 precondition 4 makes the guided structured entry flow a **prerequisite, not an alternative**: you cannot measure whether prefill helps against a baseline that does not exist. |
| `AI-02` | **Progressive intake already removes most of the friction assisted intake would target** | `IM-03`'s pitch is *"you would have answered twenty questions; instead write one sentence."* Against `IM-01` that is a strong pitch. Against `IM-02` at 5 composer answers, all of them single-choice on the discovery path, the addressable friction is much smaller — and the customer still has to read and confirm every extracted field, which is itself an interaction. |
| `AI-03` | **The remaining friction sits exactly where extraction is least reliable and most blocked** | What remains is **free-text notes and multi-constraint event description**. That is the hardest extraction target, and it is `IQ-10` — classified as **possibly containing contact data regardless of policy**, which means it may not leave Superola without a resolved lawful basis. `Q-033` is unresolved and blocks this exactly as it blocks the search-path mapper. |
| `AI-04` | **No V1 surface has an interaction problem for which this is the cheapest fix** | P04 examined the full surface inventory. The candidate problems — dynamic question reveal, `undetermined` comprehension, archetype-correct service-area framing, zero results, verification abandonment — are each cheapest to fix with governed content, honest wording, or a synonym table. **None of them is an extraction problem.** |

### 9.3 The four conditions — all four required

If **all four** later hold, the experiment becomes worth running. Any three are not enough, and the fourth is not a formality in any ordering.

| ID | Condition | Why it is necessary |
|---|---|---|
| `AC-01` | The guided composer has shipped **and request-composition abandonment is measured per step** (`R-022`, `FM-01`) | Without per-step measurement there is no baseline and no way to tell whether prefill helped. |
| `AC-02` | Abandonment **concentrates in the free-text and context stage** rather than in verification | If abandonment concentrates at verification, this is the wrong intervention entirely, and the right one is in `docs/04-ux/identity-and-verification.md`. **This is why `FM-04` must be instrumented separately — it is the condition that decides which problem is real.** |
| `AC-03` | **`Q-033`'s privacy gate is resolved**, with a lawful basis and a **verified zero-retention term** | Request free text is customer-private, assume-contains-contact-data. Not a preference and not a policy Superola may set unilaterally. |
| `AC-04` | An **application-enforced hard spend ceiling** with a **kill switch to the structured form** | A vendor console alert is not a ceiling. The kill switch is what makes the failure mode degradation rather than outage. |

**Cost anchor — parametric only. No figure here is committed, and none appears in any total in `cost-model.md`.** At **USD $0.01 per submitted request**, across the three modelled volume scenarios: **USD $3 / USD $30 / USD $200 per month**. The reason the exposure is bounded at all is structural rather than a discount: assisted intake attaches to **submitted requests**, which are authenticated, rate-limited, intentional actions, whereas the search-path mapper attaches to **anonymous searches** — roughly thirty times more of them at Growth, reachable by the anonymous public traffic that *is* the acquisition hypothesis.

### 9.4 Operating rules if it ever ships

Binding on any future implementation, and each is a rejection condition rather than a preference.

| ID | Rule |
|---|---|
| `AO-01` | **The model proposes; the form disposes.** Extracted fields are shown to the customer and **every one is user-correctable** before anything is submitted. |
| `AO-02` | **Nothing depends on model output.** A model outage degrades the flow to typing, never to failure. The structured form remains primary and **must be sufficient alone** (`DB-08`). |
| `AO-03` | **It never ranks and never decides eligibility.** `EligibilityDecision`'s six named inputs are unchanged, and ordering stays deterministic. |
| `AO-04` | **It never silently infers a critical fact.** Any field it proposes is visibly proposed. An unproposed field is asked, not guessed. |
| `AO-05` | **It is optional and skippable**, presented as an alternative entry point and never as the default path. |
| `AO-06` | **The UX must never imply AI is the marketplace source of truth.** In V1 there is no AI on the critical path at all, and that must remain legible to the customer under any later addition. |

---

## 10. What this document does not resolve

| ID | Open item | Owner | Why P04 cannot close it |
|---|---|---|---|
| `RI-O1` | **`G-06` / `Q-007`** — what "available" promises a customer | Owner | The gate that formally blocks P04. It is **UNSATISFIED**. P04 proceeds on `WA-01` under explicit David authorization: a provider may be discoverable and `accepting`, the customer supplies date and time as **request context**, and the provider determines feasibility inside the request interaction. **Every date and time question in §6 and §7 rests on this and would need re-reading if the owner answers differently.** |
| `RI-O2` | **`Q-033`** — ownership, lawful basis, and retention for the de-identified zero-result and abandoned-query corpus | Owner + qualified counsel + David | It blocks `AC-03` and therefore §9 entirely. It also blocks the abandoned-query instrumentation that `FM-01` depends on wherever those logs would carry customer-typed free text — so it is **not only an AI question**, it is a measurement question for this document's own design target. |
| `RI-O3` | **The archetype launch cohort** | Owner + David | P04 recommends four — **mobile performer, fixed venue, delivery and food, mobile professional** — and recommends transportation **out** of launch on §7.5's three grounds. This is a **recommendation against a one-archetype baseline, not a resolution**: `docs/05-roadmap/mvp-scope.md` fixes the bounded envelope at one owner-approved geography, one production locale, and **the smallest coherent category archetype**, with cohorts added only through explicit gates, and **`G-04` is PARTIAL** in `docs/02-architecture/decision-branches.md` — archetype set yes, launch cohort no. The cohort determines which governed categories and `CategoryAttributeDefinition`s must be authored before launch, and that authoring is a P05 governance workload this document does not size. |
| `RI-O4` | **`Q-020`** — the canonical locale for a provider profile and for a request conversation | Owner + P04 + content operations + compliance | The composer's governed labels, the `EventContext` values a provider reads, and the free-text notes in `IQ-10` all carry a locale, and `contentLocale` must be explicit on provider narrative and messages. **P04 records the recommendation and does not claim it resolved.** Spanish strings run materially longer than English, so no fixed-width control in the composer may depend on English string length, and machine-translated content must be explicitly marked so that no surface presents a translation as the provider's own words. |
| `RI-O5` | **`Q-035`** — live facet counts on the results page | P04 + David | Adjacent, and it constrains the discovery step that feeds this composer. **P04 recommends no live facet counts in V1**; David decides. Recorded in the discovery documents, not resolved here. |

**Nothing in this document is approved. It satisfies no owner gate, and `G-06` remains the gate that must be answered before the date and time questions in §6 and §7 can be treated as settled.**
