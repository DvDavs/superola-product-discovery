# Structural Wireframes — P04 UX Architecture

> Status: `PROPOSED — OWNER VALIDATION REQUIRED`. Provenance: `TECHNICAL_DISCOVERY`. This is a P04 UX architecture proposal, not an approved design and not a visual specification.
> Working assumptions: `WA-01`–`WA-05` (see `docs/04-ux/README.md`). `G-06` is UNSATISFIED.

These are **product-structure wireframes**. They record what information exists on a surface, which region owns it, what states that region can be in, and what the empty, error, and degraded variants do. They are drawn in monospace characters precisely so that nothing in them can be mistaken for a visual proposal.

This document is **not** a visual design, not a style guide, not a component library, not a layout specification, and not an instruction to any future implementer about proportion, spacing, hierarchy weight, or ordering beyond the structural ordering each annotation states explicitly. **No branding, color, typography, iconography, or component decision is implied by anything drawn here, and none may be inferred from it.** Box sizes are an artefact of a character grid. Region order in a drawing is a structural claim only where the annotation says so. P04 does not decide any color, font, or brand (`§5.18` of the P04 design canon (`docs/04-ux/design-canon.md`)).

Every drawing here must be read together with `docs/04-ux/surface-inventory.md`, which is the register of record for surface IDs, states, and rendering classification. Where a drawing and the inventory disagree, the inventory wins and the drawing is the defect.

---

## 1. Notation legend

The notation is deliberately small. It has no visual vocabulary — no weight, no emphasis, no color — because it is not permitted to express one.

| Mark | Meaning |
|---|---|
| `+---+` `\|` | Region boundary. A region is a structural grouping, not a card, panel, box, or component. |
| `====` | Region heading rule. Marks a labelled sub-region inside a region. |
| `(________)` | Free-text input. |
| `[v_______]` | Governed picker — selection from an operator-governed list. Where island `I-1` applies this is a type-ahead over governed labels and synonyms; it must have a plain-select fallback. |
| `( ) / (x)` | Single-choice control, unselected / selected. |
| `[ ] / [x]` | Multiple-choice control, unselected / selected. |
| `[ Label ]` | Action. A submit, a navigation, or a state-changing command. |
| `• text` | List item. |
| `«text»` | State chip. **Always rendered as text.** No state in this product is communicated by color, position, or shape alone (`§5.11`). |
| `> Label` | Disclosure, collapsed. |
| `v Label` | Disclosure, expanded. |
| `~ text ~` | Secondary or supporting text — explanation, provenance, uncertainty, non-claim. |
| `!! text` | Error or warning text. Always programmatically associated with the field it concerns and announced, never only styled. |
| `{n}` | Annotation anchor, keyed to the numbered list under each drawing. |

Two conventions apply everywhere and are not repeated in every annotation list:

- **Nothing is drawn that the platform cannot evidence.** There are no rating stars, no review counts, no response-time or response-rate badges, no "usually replies in" text, no availability calendar, no rendered map, no accept-quote button, no fan-out control, and no price anywhere a `PriceStatement` does not exist.
- **`RequestIntake` is an intake state, not availability.** It appears as `«Accepting requests»`, `«Paused»`, or `«Not confirmed»` in text, with the non-claim beside it. "Not accepting requests" is never worded as date unavailability.

---

## 2. `UX-01` — Home / need entry

Rendering `DOC` + `I-1`. Public, anonymous.

### 2.1 Desktop width

```text
+--------------------------------------------------------------------------+
| {1} SUPEROLA     [ Browse categories ] [ For providers ] [ Sign in ] [EN] |
+--------------------------------------------------------------------------+
| {2} FIND A PROVIDER FOR YOUR EVENT                                       |
|     ~ Tell us what you need and where. No account needed to search,      |
|       to view a provider, or to start a request. ~                       |
|                                                                          |
|     What do you need                    Where                            |
|     [v______________________] {3}       [v______________________] {4}    |
|     ~ Category or service type ~        ~ City or area ~                 |
|                                                                          |
|     [ Find providers ] {5}                                               |
|                                                                          |
|     !! "band for my party next month" is not a category we recognise.    |
|        Pick a category from the list, or browse below. {6}               |
+--------------------------------------------------------------------------+
| {7} BROWSE BY CATEGORY                                                   |
|     • Mobile performer        • Fixed venue                              |
|     • Delivery and food       • Mobile professional                      |
|     ~ Each category opens a list of places where providers are listed. ~ |
+--------------------------------------------------------------------------+
| {8} HOW A REQUEST WORKS                                                  |
|     1 Choose a category and a place.                                     |
|     2 Choose one provider.                                               |
|     3 Send one request to that provider.                                 |
|     4 The provider may ask a question, decline, send an offer,           |
|       or not reply.                                                      |
|     ~ V1 has no availability model. Superola does not know and does not  |
|       claim whether a provider is free on your date. The provider tells  |
|       you inside the request. ~ {9}                                      |
+--------------------------------------------------------------------------+
| {10} How Superola works · Trust and safety · Terms · Privacy · Language  |
+--------------------------------------------------------------------------+
```

### 2.2 Phone width

```text
+--------------------------------------+
| {1} SUPEROLA        [ Menu ] [ EN ]  |
+--------------------------------------+
| {2} FIND A PROVIDER                  |
|     FOR YOUR EVENT                   |
|  ~ No account needed to search. ~    |
|                                      |
|  What do you need                    |
|  [v______________________] {3}       |
|                                      |
|  Where                               |
|  [v______________________] {4}       |
|                                      |
|  [ Find providers ] {5}              |
+--------------------------------------+
| {7} BROWSE BY CATEGORY               |
|  • Mobile performer                  |
|  • Fixed venue                       |
|  • Delivery and food                 |
|  • Mobile professional               |
+--------------------------------------+
| {8} HOW A REQUEST WORKS              |
|  1 Category and place                |
|  2 One provider                      |
|  3 One request                       |
|  4 Question, decline, offer, or      |
|    no reply                          |
|  ~ V1 has no availability model.     |
|    Superola does not claim a         |
|    provider is free on your date. ~  |
|                                  {9} |
+--------------------------------------+
| {10} Footer links · Language         |
+--------------------------------------+
```

### 2.3 Annotations

1. **Global header.** Navigation and session state. Because `ADR-004` allows one `Account` to be both customer and provider, the header never advertises an account *type*; `[ For providers ]` is an informational entry point, not an account-type selector. Signed in, this region carries the two persistent contexts (`§5.15`): **Hiring** and **My business**, the latter only when a `BusinessMembership` exists.
2. **Need entry region.** The single primary purpose of `UX-01`. Its structural claim is that category and place are the two `DISCOVERY` fields (`§5.3`) and nothing else is asked here.
3. **Category picker.** Governed `Category` / archetype reference, island `I-1`. Free text is secondary matching over governed labels and synonyms only, never the key. Degraded state: with no JavaScript this is a plain select of governed categories and the page still submits.
4. **Place picker.** Governed `Place` list, resolved with zero vendor calls (`ADR-019` Level 1). Empty state: no place chosen is valid input to browse but not to search; the search action asks for a `Place` at a usable granularity rather than guessing.
5. **Primary action.** Navigates to `UX-04`. It is not a request and creates no `RequestDraft`.
6. **Error variant of {3}/{4}.** Unresolvable free text is **rejected and shown**, never approximated into a nearest guess. The message names the input, is associated with the field, and offers the governed alternatives. This region is absent in the normal state.
7. **Browse region.** The non-search path into `UX-02` / `UX-03`. It exists so a customer who cannot name their need can still reach supply. Transportation is modelled but recommended out of the launch cohort (`§5.3.1`), so it does not appear in the launch browse set.
8. **Expectation region.** States the actual mechanic: one deliberate recipient (`WA-02`), four possible provider outcomes including no reply. This is the surface that prevents the customer from forming a broadcast or booking expectation.
9. **Non-claim.** `ADR-005` requires the `V1 has no availability model` non-claim to be visible in substance on every profile and search surface; `UX-01` carries it too because this is where the date expectation forms. Never rendered as a disclaimer that can be collapsed away on phone width.
10. **Footer.** `UX-06` trust pages and locale control. Locale control is structural: Spanish strings run materially longer than English and no control here may depend on English string length (`§5.11`).

---

## 3. `UX-07` — Request composer (progressive intake)

Rendering `LOCAL` + `I-1`, `I-2`. Public and anonymous; the draft is browser-local until an `Account` exists (`§5.2`). Three consecutive stages are drawn, including the category-conditional reveal.

### 3.1 Stage A — Your event (universal `PRE-SUBMIT`) — desktop width

```text
+--------------------------------------------------------------------------+
| SUPEROLA                                          [ Save and exit ] {1}  |
+--------------------------------------------------------------------------+
| {2} YOUR REQUEST      Your need > YOUR EVENT > Performer details >        |
|                       Optional details > Review                          |
|     ~ Stages depend on the service you chose. ~                          |
+--------------------------------------------------------------------------+
| {3} REQUESTING FROM                                                      |
|     Mariachi Estrella del Norte · Mobile performer · Houston area        |
|     «Accepting requests»  ~ Intake state, not availability. ~            |
+--------------------------------------------------------------------------+
| {4} YOUR EVENT                                                           |
|     What is the occasion                                                 |
|     [v______________________]  ~ Quinceanera, wedding, corporate, ... ~  |
|                                                                          |
|     When is it                                                     {5}   |
|     ( ) A specific date   (________)                                     |
|     ( ) A date range      (________) to (________)                       |
|     (x) Not decided yet — flexible                                       |
|     ~ The date is context for the provider. It is not a filter and       |
|       Superola makes no claim about the provider being free. ~     {6}   |
|                                                                          |
|     [ Back ]                                        [ Continue ]   {7}   |
+--------------------------------------------------------------------------+
| {8} ~ Your answers are kept on this device. Nothing is sent to the       |
|      provider until you review and send. ~                              |
+--------------------------------------------------------------------------+
```

### 3.2 Stage B — Category-conditional reveal (`CATEGORY`) — desktop width

```text
+--------------------------------------------------------------------------+
| SUPEROLA                                          [ Save and exit ]      |
+--------------------------------------------------------------------------+
|     YOUR REQUEST      Your need > Your event > PERFORMER DETAILS >        |
|                       Optional details > Review                          |
+--------------------------------------------------------------------------+
| {9} PERFORMER DETAILS                                                    |
|     ~ These questions apply to mobile performers. They appeared          |
|       because of the service you chose. ~                                |
|                                                                          |
|     Approximate start time      (________)               `CATEGORY`      |
|     How long do you need        (________) hours         `CATEGORY`      |
|     Indoor or outdoor           ( ) Indoor ( ) Outdoor ( ) Both ( ) Not  |
|                                     sure                                 |
|     Is there a stage or         ( ) Yes ( ) No ( ) Not sure              |
|     performance area                                                     |
|     Sound equipment             ( ) Provider brings it                   |
|                                 ( ) Venue provides it                    |
|                                 ( ) Not sure                             |
|     > Song or repertoire requests (optional)                       {10}  |
|                                                                          |
|     [ Back ]                                        [ Continue ]         |
+--------------------------------------------------------------------------+
| {11} ~ Changing the service you asked for will remove the answers on     |
|       this stage. Your event answers are kept. ~                        |
+--------------------------------------------------------------------------+
```

Category-change confirmation, shown **before** the change is applied:

```text
+--------------------------------------------------------------------------+
| {12} CHANGE THE SERVICE                                                  |
|      You are changing from Mobile performer to Delivery and food.        |
|      These answers will be removed:                                      |
|        • Approximate start time                                          |
|        • How long do you need                                            |
|        • Indoor or outdoor                                               |
|        • Stage or performance area                                       |
|        • Sound equipment                                                 |
|      These answers are kept: occasion, date, place.                      |
|      [ Keep mobile performer ]        [ Change the service ]             |
+--------------------------------------------------------------------------+
```

### 3.3 Stage C — Optional details (`QUALITY`) — desktop width

```text
+--------------------------------------------------------------------------+
|     YOUR REQUEST      Your need > Your event > Performer details >        |
|                       OPTIONAL DETAILS > Review                          |
+--------------------------------------------------------------------------+
| {13} OPTIONAL DETAILS   ~ All optional. Skipping does not block your      |
|                           request. ~                                     |
|                                                                          |
|      About how many guests       (________)     ~ Helps the provider ~   |
|      Budget range                ( ) Under band A                  {14}  |
|                                  ( ) Band A to band B                    |
|                                  ( ) Band B to band C                    |
|                                  ( ) Above band C                        |
|                                  (x) Prefer not to say                   |
|      How far along are you       ( ) Just exploring                {15}  |
|                                  ( ) Comparing providers                 |
|                                  ( ) Ready to decide                     |
|      Anything else the           (______________________________)  {16}  |
|      provider should know        (______________________________)        |
|                                  ~ Please do not put your phone or       |
|                                    email here. You will add a contact    |
|                                    channel when you send. ~              |
|                                                                          |
|      [ Back ]                          [ Skip ]     [ Review request ]   |
+--------------------------------------------------------------------------+
```

### 3.4 Phone width — the same three stages, one question group per screen

```text
Stage A                        Stage B                        Stage C
+----------------------------+ +----------------------------+ +----------------------------+
| YOUR REQUEST               | | YOUR REQUEST               | | YOUR REQUEST               |
| Stage: YOUR EVENT     {17} | | Stage: PERFORMER DETAILS   | | Stage: OPTIONAL DETAILS    |
| ~ 1 of the stages for this | | ~ These appeared because   | | ~ All optional. ~          |
|   service are done. ~      | |   of the service chosen. ~ | |                            |
+----------------------------+ +----------------------------+ +----------------------------+
| Requesting from            | | Approximate start time     | | About how many guests      |
| Mariachi Estrella          | | (__________)               | | (__________)               |
| «Accepting requests»       | |                            | |                            |
| ~ Intake state, not        | | How long do you need       | | Budget range               |
|   availability. ~          | | (__________) hours         | | ( ) Under band A           |
+----------------------------+ |                            | | ( ) Band A to band B       |
| What is the occasion       | | Indoor or outdoor          | | ( ) Band B to band C       |
| [v________________]        | | ( ) Indoor                 | | ( ) Above band C           |
|                            | | ( ) Outdoor                | | (x) Prefer not to say      |
| When is it                 | | ( ) Both                   | |                            |
| ( ) A specific date        | | ( ) Not sure               | | How far along are you      |
| ( ) A date range           | |                            | | ( ) Just exploring         |
| (x) Not decided — flexible | | Stage or performance area  | | ( ) Comparing providers    |
|                            | | ( ) Yes ( ) No ( ) Not sure| | ( ) Ready to decide        |
| ~ Context for the provider,| |                            | |                            |
|   not a filter, and not a  | | Sound equipment            | | Anything else              |
|   claim about the provider.| | ( ) Provider brings it     | | (__________________)       |
+----------------------------+ | ( ) Venue provides it      | | ~ No phone or email here. ~|
| [ Continue ]          {18} | | ( ) Not sure               | |                            |
| [ Back ]                   | |                            | | [ Review request ]         |
+----------------------------+ | > Song requests (optional) | | [ Skip ]  [ Back ]         |
                               +----------------------------+ +----------------------------+
                               | [ Continue ]   [ Back ]    |
                               +----------------------------+
```

### 3.5 Annotations

1. **Exit action.** Leaves the composer without discarding. The draft is browser-local and anonymous at this point (`§5.2`); the wording never promises cross-device resume, because cross-device resume exists only after server persistence.
2. **Named-stage indicator.** Stage names, never `Step 3 of about 6`. The stage set is data derived from the `CategoryAttributeDefinition`s of the selected archetype, so a numeric denominator would be dishonest and is prohibited (`§5.1`, `§5.11`). The current stage is announced.
3. **Recipient region.** One deliberate recipient (`WA-02`). Carries `RequestIntake` as text plus the non-claim. There is no "add more providers" control and no "send to similar providers" control anywhere in the composer.
4. **Universal `EventContext` region.** Occasion and date are `PRE-SUBMIT` universal fields and are never category-conditional. Changing the occasion drops nothing (`§5.2`).
5. **Date control.** `flexible` is a first-class valid answer, not a skip. Error variant: an end date before a start date is an inline field-associated error; there is no calendar of provider availability anywhere near this control.
6. **Non-claim, request-context form.** States that the date is context and not a filter. Required here because this is where a customer would otherwise infer a booking.
7. **Persistent primary action.** On phone width the primary action is persistent per `§5.12`.
8. **Draft provenance notice.** Explains local-only storage honestly and states that nothing reaches the provider before send. Empty variant: once the customer signs in voluntarily, this region instead states that the draft is saved to the account and resumable on another device.
9. **Category-conditional region — the reveal.** This is the hardest accessibility problem in P04 (`§5.11`). Structural requirements: the region is announced politely on reveal; focus moves to the region heading, not to its first input; already-answered stages are not reordered; the region states *why* it appeared. Empty variant: an archetype with no required `CATEGORY` attributes skips this stage entirely and the stage indicator never shows it — no empty stage is rendered.
10. **Optional disclosure inside a required stage.** Collapsed by default. Its content is `QUALITY`, so collapsing it blocks nothing.
11. **Loss warning, before the fact.** Names what a category change would remove.
12. **Category-change confirmation.** Explicit, names each field that will be dropped, names what is kept, and the dropped answers are retained in session so an immediate undo restores them (`§5.2`).
13. **`QUALITY` stage.** Every field here improves the `ProviderResponse` and gates nothing. The stage is skippable as a whole.
14. **Budget as a band.** Never a required numeric min/max pair (`§5.4`). Band labels are governed values; no currency figure is invented in this document. Where supplied it reaches the provider only inside the authenticated request surface, never in a notification body (`ADR-010`).
15. **Planning intent.** One optional question, drives no eligibility.
16. **Free-text notes.** Classified as possibly containing contact data (`ADR-010`). The helper text asks the customer not to place contact data here; it does **not** claim the platform protects or strips it, because Superola must not tell users that in-platform means contact-protected.
17. **Phone stage header.** Same named-stage rule. The completion hint counts completed stages, never a total that does not exist.
18. **Phone primary action placement.** One question group per screen with a persistent primary action (`§5.12`). Drawn last in the character grid; the ordering claim is "persistent and reachable", not a visual position.

---

## 4. `UX-04` — Search results, including the zero-result variant

Rendering `DOC` + `I-1`. Public, anonymous.

### 4.1 Desktop width — results present

```text
+--------------------------------------------------------------------------+
| SUPEROLA        [v Mariachi ] [v Houston ]          [ Search ]      {1}  |
+--------------------------------------------------------------------------+
| {2} FILTERS                | {3} MARIACHI IN HOUSTON                      |
| ~ No result counts are     |     ~ Market: this governed Category in      |
|   shown next to filters. ~ |       this governed Place. ~                 |
|                            |     Showing 1-10                             |
| Service details            |     ~ V1 has no availability model. These    |
| ~ Governed category        |       providers are not shown as free on any |
|   attributes. They narrow  |       date. The date you enter is context    |
|   within the category and  |       for the provider only. ~          {4}  |
|   are never the search     |                                              |
|   key. ~                   | +------------------------------------------+ |
| Ensemble size              | | {5} Mariachi Estrella del Norte          | |
| [ ] 3-5                    | |     Mariachi · Mariachi ensemble         | |
| [ ] 6-10                   | |     Serves Houston, Katy, Sugar Land     | |
| [ ] 11 or more             | |     «Accepting requests» «Published»      | |
|                            | |     ~ Intake state, not availability. ~  | |
| Languages                  | |     > Why this result appears       {6}  | |
| [ ] Spanish                | |     ~ Listed organically. Superola has   | |
| [ ] English                | |       no paid placement. ~          {7}  | |
|                            | |     [ View profile ] [ Send a request ]  | |
| Travels to                 | +------------------------------------------+ |
| [ ] Houston                | +------------------------------------------+ |
| [ ] Katy                   | | {8} Trio Aurora                          | |
| [ ] Sugar Land             | |     Mariachi · Mariachi ensemble         | |
|                            | |     Serves Houston                       | |
| [ Apply ]  [ Clear ]       | |     «Accepting requests» «Not updated    | |
|                            | |      recently»                           | |
|                            | |     ~ This provider has not confirmed    | |
|                            | |       their details recently. ~          | |
|                            | |     > Why this result appears            | |
|                            | |     [ View profile ] [ Send a request ]  | |
|                            | +------------------------------------------+ |
|                            | +------------------------------------------+ |
|                            | | {9} Sonora del Golfo                     | |
|                            | |     Mariachi · Mariachi ensemble         | |
|                            | |     Coverage for Houston could not be    | |
|                            | |     determined                           | |
|                            | |     «Paused — not taking requests now»    | |
|                            | |     ~ This is not a statement about any  | |
|                            | |       date. ~                            | |
|                            | |     > Why this result appears            | |
|                            | |     [ View profile ]                     | |
|                            | +------------------------------------------+ |
|                            |     [ Previous ]  Page 1  [ Next ]     {10} |
+--------------------------------------------------------------------------+
```

Ranking explanation, expanded (`{6}`):

```text
| v Why this result appears                                                |
|   • Listed in the governed category Mariachi.                            |
|   • Declares coverage that includes Houston.                             |
|   • Profile is published and was confirmed recently.                     |
|   • Placement basis: organic. Superola has no paid placement in V1.      |
|   ~ This order is not a quality ranking and not a recommendation. ~      |
```

### 4.2 Desktop width — zero-result variant

```text
+--------------------------------------------------------------------------+
| SUPEROLA        [v Banda ] [v Rockport ]            [ Search ]           |
+--------------------------------------------------------------------------+
| {11} NO PROVIDERS MATCH THIS SEARCH                                      |
|      We list Banda providers, but none of them declare coverage for      |
|      Rockport. This is a coverage gap in this area, not a category we    |
|      do not support.                                              {12}   |
|                                                                          |
|      TRY A WIDER SEARCH                                           {13}   |
|      • Banda providers who travel to Corpus Christi                      |
|        ~ Relaxed constraint: the place, widened to a coarser governed    |
|          area. The Banda category is unchanged. ~                        |
|      • Banda providers in Rockport with the ensemble-size filter         |
|        removed                                                           |
|        ~ Relaxed constraint: one governed attribute filter. Category     |
|          and place unchanged. Offered only when a filter is applied. ~   |
|      ~ Relaxation never swaps the Category the customer picked. ~        |
|                                                                          |
|      CHANGE YOUR SEARCH                                                  |
|      [v Banda ] [v Rockport ]                       [ Search again ]     |
|                                                                          |
|      TELL US WHAT YOU WERE LOOKING FOR (optional)                 {14}   |
|      (____________________________________________)                      |
|      [ Send ]                                                            |
|      ~ This helps us decide where to add providers. We will not          |
|        notify you and there is nothing to sign up for. ~                 |
|                                                                          |
|      ~ Your search is not sent to any provider. Superola never turns a   |
|        search into a message. ~                                   {15}   |
+--------------------------------------------------------------------------+
```

Second zero-result shape — unsupported category:

```text
| {16} WE DO NOT LIST THIS SERVICE YET                                     |
|      Superola does not have a category for "ice sculpture" in Houston.   |
|      • Browse the categories we do list                                  |
|      • Tell us what you were looking for (optional)                      |
```

### 4.3 Phone width

```text
+--------------------------------------+ +--------------------------------------+
| [v Mariachi ]                        | | NO PROVIDERS MATCH             {11}  |
| [v Houston ]        [ Search ]       | | We list Banda providers, but none    |
+--------------------------------------+ | declare coverage for Rockport.       |
| MARIACHI IN HOUSTON                  | |                                      |
| ~ Market: Category × Place. ~        | | TRY A WIDER SEARCH             {13}  |
| Showing 1-10       [ Filters ]  {17} | | • Banda providers who travel to      |
| ~ V1 has no availability model.      | |   Corpus Christi                     |
|   Providers are not shown as free    | |   ~ Relaxed: the place, widened. ~   |
|   on any date. ~                     | | • Banda providers in Rockport with   |
+--------------------------------------+ |   the ensemble-size filter removed   |
| Mariachi Estrella del Norte          | |   ~ Relaxed: one filter. ~           |
| Mariachi · Mariachi ensemble         | | ~ The Category is never swapped. ~   |
| Serves Houston, Katy, Sugar Land     | |                                      |
| «Accepting requests» «Published»      | | CHANGE YOUR SEARCH                   |
| ~ Intake state, not availability. ~  | | [v Banda ] [v Rockport ]             |
| > Why this result appears       {18} | | [ Search again ]                     |
| [ View profile ]                     | |                                      |
| [ Send a request ]                   | | TELL US WHAT YOU WERE          {14}  |
+--------------------------------------+ | LOOKING FOR (optional)               |
| Trio Aurora                          | | (__________________)  [ Send ]       |
| Mariachi · Mariachi ensemble         | | ~ No notification, nothing to sign   |
| Serves Houston                       | |   up for. ~                          |
| «Accepting requests»                  | |                                      |
| «Not updated recently»                | | ~ Your search is never sent to a     |
| > Why this result appears            | |   provider. ~                  {15}  |
| [ View profile ] [ Send a request ]  | +--------------------------------------+
+--------------------------------------+
| [ Previous ]  Page 1  [ Next ]       |
+--------------------------------------+
```

### 4.4 Annotations

1. **Query region.** Governed category and governed place, island `I-1`. Desired date is **not present** — it is request context only, never a filter (`§5.3`).
2. **Filter region.** Every filter here is bound to a governed `CategoryAttributeDefinition` marked filterable for this `Market` (`DR-04`). **A filter narrows within the governed key and is never the key itself**, so no filter may name a `Category` — a "sub-type" list whose values are themselves governed Categories would make the search key ambiguous and is not drawn. Filterability is governed, per-`Market`, and revocable. Filters are listed **without counts**: `Q-035` facet counts, P04 recommends none in V1 (`§5.6`), which removes an entire class of "0 results (3)" contradictions. Filters operate on language-neutral governed identifiers.
3. **Result region header.** The `Market` — the governed `Category` the customer picked × the governed `Place` — stated in words so the query is legible without reading the controls. The header never substitutes the `CategoryArchetype` for the `Category`: the archetype is an engineering grouping, and a customer who picked Mariachi must be able to verify the match against Mariachi.
4. **Non-claim on a search surface.** Required verbatim in substance by `ADR-005`. It is placed above the first result so it cannot be scrolled past on phone width before a customer reads an intake chip.
5. **Result slot — normal.** Contains identity, the **governed `Category` label** followed by the matched `ServiceOffering` (`RC-03`, `RC-02`), declared coverage in words, publication state and `RequestIntake` as text chips, a ranking explanation disclosure, and actions. Every slot displays the same `Category` the customer picked and the header names, not the `CategoryArchetype`. The action is `[ Send a request ]`: the label names what the customer does, never what they will get back, because the outcome may be a `clarification`, a `decline`, an `offer`, or `NoResponse`. It does **not** contain: a rating, a review count, a price, a response-time or response-rate badge, a distance in miles from the customer, a map pin, or a photo requirement. Declared coverage is a provider **claim** (`ServiceAreaDeclaration`) and is worded as one.
6. **Ranking explanation, per slot.** Required on every result slot. Collapsed on phone to one line (`§5.12`).
7. **Placement disclosure, per slot.** `placementBasis` is `organic` for every V1 slot and is disclosed per slot, not once per page. There is no `featured` or `sponsored` marker because there is no such value in V1.
8. **`Stale` result slot.** `Stale` stays discoverable and flagged, never hidden (`§4.15`). The freshness indicator is text. Customers see only `Published` and `Stale`; `Suspended` and `Deactivated` are not distinguishable from each other or from absence.
9. **`undetermined` and `paused` slot.** `LocationEligibility: undetermined` is surfaced with its uncertainty and never coerced or silently dropped. `«Paused»` is shown with its own non-claim: it is an intake state, not a statement about any date. The request action is withheld on a paused offering; the profile remains reachable.
10. **Pagination.** A too-broad query ranks and paginates; it never refuses (`§5.6`).
11. **Zero-result region — no supply in this area.** Recorded separately from *no supply in this category*, because they are different demand signals.
12. **Honest coverage explanation.** Names which of the two gaps this is, in words.
13. **Progressive relaxation with the relaxed constraint named.** Each option states exactly which constraint it relaxes, and exactly one is relaxed per option. **Relaxation never crosses the governed key** (`docs/04-ux/discovery-and-results.md` §3.3): the `Place` may be widened to a coarser governed granularity and a filter may be dropped, but the `Category` the customer picked is never swapped for a different one — moving Banda to Mobile performer would answer a question the customer did not ask. The filter option is offered only where a filter is actually applied; where none is, only the place option appears. A relaxation is a new search the customer chooses, never an automatic widening.
14. **Unmet-demand capture.** Optional and explicitly no-promise. **It must not promise a notification**: durable `DemandWatch` / saved searches / alerts are `Q-031`, unratified and out of V1 search scope (`§5.6`). Error variant: submission failure says so plainly and does not claim the demand was recorded.
15. **Anti-broadcast statement.** A search is never converted into a message to providers (`WA-02`). Structural, not decorative — it is the sentence that prevents a customer from expecting a fan-out.
16. **Zero-result region — unsupported category.** Says so plainly and captures the demand signal.
17. **Phone filter entry.** Filters collapse behind an action; the result list is a single column (`§5.12`).
18. **Phone ranking explanation.** Collapsed to one line, expandable in place. Collapsing never removes the placement disclosure — the disclosure line remains outside the disclosure.

---

## 5. `UX-05` — Provider profile (public)

Rendering `DOC`. Public, anonymous.

### 5.1 Desktop width

```text
+--------------------------------------------------------------------------+
| SUPEROLA                              [ Search ] [ For providers ] [ EN ] |
+--------------------------------------------------------------------------+
| {1} MARIACHI ESTRELLA DEL NORTE                                          |
|     Mobile performer                                                     |
|     «Published»  «Accepting requests»                                     |
|     ~ Accepting requests means this provider is currently taking new     |
|       requests. It is not a statement about any date. ~            {2}   |
|     [ Send a request ]                                             {3}   |
+--------------------------------------------------------------------------+
| {4} WHERE THEY WORK                       | {8} SERVICES OFFERED         |
|     Based in the Houston area             |     • Mariachi ensemble      |
|     Serves: Houston, Katy, Sugar Land,    |       «Accepting requests»    |
|     Pearland                              |       [ Send a request ]     |
|     Travels up to 50 miles from Houston   |     • Solo guitar and vocals |
|     ~ Coverage is declared by the         |       «Paused — not taking    |
|       provider. Superola does not         |        requests now»          |
|       verify travel distance. ~     {5}   |       ~ Intake state, not    |
|     ~ No map, and no map-app link:  {6}   |         availability. ~      |
|       this base is a declared area,       |                              |
|       not a published venue address. ~    | {9} CATEGORY DETAILS         |
|                                           |     Ensemble size: 6-10      |
| {7} ABOUT                                 |     Languages: Spanish,      |
|     Narrative written by the provider.    |       English                |
|     ~ Written in Spanish. Shown in        |     Brings own sound system: |
|       Spanish. ~                          |       Yes                    |
|                                           |     ~ Provider-declared. ~   |
+-------------------------------------------+------------------------------+
| {10} PHOTOS                                                              |
|      [ image ] [ image ] [ image ]   ~ Each image has alternative text. ~ |
| {11} AUDIO AND VIDEO                                                     |
|      • Live set 2024 — opens on the provider's external link             |
|      • Wedding demo — link unavailable                                   |
|        ~ This link is not working right now. ~                           |
+--------------------------------------------------------------------------+
| {12} WHAT SUPEROLA DOES AND DOES NOT KNOW                                |
|      • Superola confirmed this provider controls a contact channel.      |
|      • V1 has no availability model. Superola does not know whether      |
|        this provider is free on your date; ask them in a request.        |
|      • Superola does not verify prices, licences, insurance, or          |
|        performance quality.                                              |
|      ~ Profile details were last confirmed by the provider recently. ~   |
+--------------------------------------------------------------------------+
| {13} [ Send a request ]         [ Report this profile ]                  |
+--------------------------------------------------------------------------+
```

### 5.2 Phone width

```text
+--------------------------------------+
| {1} MARIACHI ESTRELLA DEL NORTE      |
|     Mobile performer                 |
|     Houston area                     |
|     «Published» «Accepting requests»   |
|     ~ Intake state, not a statement  |
|       about any date. ~         {2}  |
|     [ Send a request ]          {3}  |
+--------------------------------------+   <- identity, service, coarse
| {8} SERVICES OFFERED                 |      location and the request
|     • Mariachi ensemble              |      action are above the fold
|       «Accepting requests»             |      (§5.12)                {14}
|       [ Send a request ]             |
|     • Solo guitar and vocals         |
|       «Paused — not taking requests»   |
+--------------------------------------+
| {4} WHERE THEY WORK                  |
|     Serves: Houston, Katy,           |
|     Sugar Land, Pearland             |
|     Travels up to 50 miles           |
|     ~ Declared by the provider. ~    |
|     ~ No map-app link: base is a     |
|       declared area, not a venue. ~  |
+--------------------------------------+
| {7} > About this provider            |
| {9} > Category details               |
| {10}> Photos                         |
| {11}> Audio and video                |
+--------------------------------------+
| {12} WHAT SUPEROLA DOES AND DOES     |
|      NOT KNOW                        |
|      • Contact channel confirmed.    |
|      • V1 has no availability model. |
|        Ask them in a request.        |
|      • Prices, licences, insurance,  |
|        and quality are not verified. |
+--------------------------------------+
| {13} [ Send a request ]              |
|      [ Report this profile ]         |
+--------------------------------------+
```

### 5.3 Annotations

1. **Identity region.** Display name, archetype, publication state and `RequestIntake` as text. No rating, no review count, no "top rated", no response-time badge, no "member since" that implies quality.
2. **`RequestIntake` explanation.** Text, adjacent to the chip. `«Paused»` is worded "not taking requests now" and never as date unavailability.
3. **Primary action, and its wording.** Enters `UX-07` with the recipient already selected and the discovery answers (category, place) carried forward — three of the blocking items are already answered before the composer opens (`docs/04-ux/request-intake.md` §3; the earlier "6–9 answers" target was net of identity and is withdrawn). **The label names the customer's action, never the provider's reply.** `Quote` is retired as an entity name (`§2` of `docs/04-ux/design-canon.md`), and a button reading *request a quote* would promise one of four outcomes on the very surface whose job is to prevent outcome expectations: a customer who receives a `clarification`, a `decline`, or `NoResponse` was misled by the control they pressed. `RFQ` remains acceptable only in explicitly owner-facing text, never here. Where the shorter label does not fit, *Request this provider* is the permitted variant; *request a quote* is not. Disabled variant: on an offering in `«Paused»` the action is replaced by an explanation, not by a greyed control with no text.
4. **Location region — words, not a map.** Coarse `Place` labels plus a declared-coverage statement in the provider's own units (`§5.9`). Precise provider base location is never exposed. Fixed-venue variant: where the provider has chosen to publish a venue address, the published address appears here; where they have not, only the coarse `Place` does.
5. **Claim framing.** `ServiceAreaDeclaration` is a provider claim, worded as one. `verificationBasis` language is reserved for facts with a defined auditable event.
6. **Map link-out — mobile performer variant: absent.** `PP-63` permits a link-out to the customer's own map application only from a **published fixed venue address**. This provider's base is a declared area ("Houston area"), not a published address, so no map-app link appears here; the coverage statement in region 4 is the only location detail this archetype gets. Where a link-out does appear (a fixed-venue provider with a published address), it is **not a rendered map**: no tiles, no vendor SDK, no pin drawn from a centroid.
7. **Provider narrative.** `contentLocale` is explicit. If a machine translation is ever offered it is marked as such so no surface presents a translation as the provider's own words (`§5.11`, `Q-020` open).
8. **Offerings region.** Eligibility and `RequestIntake` are per `ServiceOffering`, never per `Business` (`ADR-006`). Two offerings of the same business may carry different intake states, which is why the chip is repeated per offering rather than stated once at the top.
9. **Governed category attributes.** Rendered from `CategoryAttributeDefinition`s. A new Category renders here with no new screen (`§5.3`). Empty variant: an attribute the provider has not answered is omitted, never shown as "unknown" in a way that reads as a defect.
10. **Images.** Hosted, from a fixed enumerated derivative set, with a processing state upstream (`ADR-018`). Alternative text is a property of the `MediaReference` placement and is a publication-quality item.
11. **Audio and video.** Linked out, never uploaded (`ADR-018`). A dead external link is a real, visible state.
12. **Trust region — the honest-limits statement.** States what was actually verified (control of a contact channel, with `verificationBasis` semantics), states the availability non-claim required by `ADR-005`, and states plainly what is not verified. Freshness appears here in text. `Suspended` never appears; a suspended and a deactivated profile are indistinguishable to a customer, which in practice means neither is reachable.
13. **Repeated primary action plus report.** Report intake must not require the reported content to still be visible (`§5.16`).
14. **Phone above-the-fold ordering.** `§5.12` names this as a structural constraint: identity, service, coarse location, and the request action lead. Secondary regions become disclosures. This is the one place in this document where region order is a binding structural claim rather than a drawing artefact.

---

## 6. `UX-08` request review and send, and `UX-09` verification

`UX-08` rendering `DOC`, public. `UX-09` rendering `DOC`, transitional auth. `WA-05` guest-then-verify.

### 6.1 `UX-08` — desktop width

```text
+--------------------------------------------------------------------------+
| {1} REVIEW YOUR REQUEST                                                  |
|     ~ Nothing has been sent yet. ~                                       |
+--------------------------------------------------------------------------+
| {2} GOING TO ONE PROVIDER                                                |
|     Mariachi Estrella del Norte · Mariachi ensemble                      |
|     «Accepting requests»                                                  |
|     ~ Only this provider will receive this request. Superola does not    |
|       send it to anyone else. ~                                    {3}   |
+--------------------------------------------------------------------------+
| {4} WHAT YOU ARE SENDING                                     [ Edit ]    |
|     Service            Mariachi ensemble                     [ Edit ]    |
|     Place              Houston                               [ Edit ]    |
|     Occasion           Quinceanera                           [ Edit ]    |
|     Date               Not decided yet — flexible            [ Edit ]    |
|     Start time         6:00 PM (approximate)                 [ Edit ]    |
|     Duration           2 hours                               [ Edit ]    |
|     Indoor or outdoor  Outdoor                               [ Edit ]    |
|     Stage area         Not sure                              [ Edit ]    |
|     Sound equipment    Provider brings it                    [ Edit ]    |
|     Guests             About 120                             [ Edit ]    |
|     Budget             Prefer not to say                     [ Edit ]    |
|     Notes              "Looking for a 45-minute set..."      [ Edit ]    |
+--------------------------------------------------------------------------+
| {5} WHAT HAPPENS NEXT                                                    |
|     • We will ask you to confirm an email address or phone number.       |
|     • Your request is saved while you confirm. You will not lose it.     |
|     • After you confirm, the provider receives it.                       |
|     • The provider may ask a question, decline, send an offer, or        |
|       not reply. Superola cannot promise a reply.                  {6}   |
|     ~ Sending a request is not a booking and creates no obligation for   |
|       anyone. ~                                                    {7}   |
+--------------------------------------------------------------------------+
| {8} [ Back ]                            [ Continue to confirm and send ] |
+--------------------------------------------------------------------------+
```

### 6.2 `UX-09` — desktop width

```text
+--------------------------------------------------------------------------+
| {9} CONFIRM HOW THE PROVIDER CAN REACH YOU                               |
|     ~ Your request is saved. Confirming does not send anything to the    |
|       provider until it succeeds. ~                               {10}   |
+--------------------------------------------------------------------------+
| {11} Where should replies go                                             |
|      ( ) Email      (_______________________)                            |
|      ( ) Phone      (_______________________)                            |
|      ~ The provider does not see this until Superola's disclosure rules  |
|        apply. Your name and contact details are not part of the          |
|        request itself. ~                                          {12}   |
|      [ Send me a code ]                                                  |
+--------------------------------------------------------------------------+
| {13} Enter the code we sent                                              |
|      (______)                                                            |
|      !! That code is not correct or has expired. [ Send a new code ]     |
|      [ Confirm and send request ]                                        |
+--------------------------------------------------------------------------+
| {14} ~ We could not send the code to that address. Try a different       |
|        email or phone. Your request is still saved. ~                   |
+--------------------------------------------------------------------------+
| {15} ~ This confirms you control this email or phone. It is not an       |
|        identity check and Superola does not verify who you are. ~       |
+--------------------------------------------------------------------------+
```

`UX-10` request submitted confirmation, drawn for continuity. **`surface-inventory.md` gives this surface two states and both are drawn**, because only one of them may say the request reached anyone.

State 1 — delivered:

```text
+--------------------------------------------------------------------------+
| {16} YOUR REQUEST REACHED THE PROVIDER                                   |
|      Mariachi Estrella del Norte · Mariachi ensemble                     |
|      «Delivered — waiting for a reply»                                    |
|      • The provider may ask a question, decline, send an offer, or       |
|        not reply.                                                        |
|      • We will try to notify you at the address you confirmed. This      |
|        page and your requests list are the record.                       |
|      ~ This is not a booking. ~                                          |
|      [ View this request ]  [ Request another service for this event ]   |
|                                                                   {17}   |
+--------------------------------------------------------------------------+
```

State 2 — submitted and pending verification:

```text
+--------------------------------------------------------------------------+
| {18} YOUR REQUEST IS SAVED. IT HAS NOT REACHED THE PROVIDER YET          |
|      For Mariachi Estrella del Norte · Mariachi ensemble                 |
|      «Saved — waiting for you to confirm your contact»                    |
|      • Nothing has been delivered. This provider cannot see this         |
|        request and does not know it exists.                              |
|      • We will try to deliver it once you confirm the email or phone     |
|        you gave us, and only if the recipient is still eligible at       |
|        that time. Your answers are kept until then.                      |
|      • We will try to send the code to that contact. This page and       |
|        your requests list are the record.                                |
|      ~ This is not a booking. ~                                          |
|      [ Confirm my contact ]  [ Use a different contact ]                 |
|      [ View this request ]                                        {19}   |
+--------------------------------------------------------------------------+
```

### 6.3 Phone width

```text
UX-08                                  UX-09                            UX-10 state 1
+----------------------------+ +----------------------------+ +----------------------------+
| REVIEW YOUR REQUEST        | | CONFIRM HOW THE PROVIDER   | | YOUR REQUEST REACHED THE   |
| ~ Nothing sent yet. ~      | | CAN REACH YOU              | | PROVIDER                   |
+----------------------------+ | ~ Your request is saved. ~ | | To Mariachi Estrella       |
| GOING TO ONE PROVIDER      | +----------------------------+ | «Delivered — waiting for a  |
| Mariachi Estrella          | | ( ) Email                  | |  reply»                     |
| «Accepting requests»         | | (__________________)       | +----------------------------+
| ~ Only this provider will  | | ( ) Phone                  | | • The provider may ask a   |
|   receive it. ~            | | (__________________)       | |   question, decline, send  |
+----------------------------+ | [ Send me a code ]         | |   an offer, or not reply.  |
| WHAT YOU ARE SENDING       | +----------------------------+ | • We will try to notify    |
| Service  Mariachi ensemble | | Enter the code             | |   you at the address you   |
| Place    Houston   [ Edit ]| | (______)                   | |   confirmed. This page and |
| Occasion Quinceanera       | | !! Code incorrect or       | |   your requests list are   |
| Date     Flexible          | |    expired.                | |   the record.              |
| ...      ...       [ Edit ]| | [ Send a new code ]        | | ~ This is not a booking. ~ |
+----------------------------+ | [ Confirm and send ]       | +----------------------------+
| WHAT HAPPENS NEXT          | +----------------------------+ | [ View this request ]      |
| • Confirm an email/phone   | | ~ Confirms you control     | | [ Request another service  |
| • Request is saved         | |   this channel. Not an     | |   for this event ]         |
| • Then the provider gets it| |   identity check. ~        | +----------------------------+
| • They may not reply       | +----------------------------+
| ~ Not a booking. ~         |
+----------------------------+
| [ Continue to confirm ]    |
| [ Back ]                   |
+----------------------------+
```

`UX-10` state 2 at phone width — submitted and pending verification:

```text
+----------------------------+
| YOUR REQUEST IS SAVED.     |
| IT HAS NOT REACHED THE     |
| PROVIDER YET               |
| For Mariachi Estrella      |
| «Saved — waiting for you    |
|  to confirm your contact»   |
+----------------------------+
| • Nothing has been         |
|   delivered. This provider |
|   cannot see it and does   |
|   not know it exists.      |
| • We will try to deliver   |
|   it once you confirm the  |
|   email or phone you gave  |
|   us, and only if you're   |
|   still an eligible        |
|   recipient at that time.  |
| • We will try to send the  |
|   code to that contact.    |
|   This page and your       |
|   requests list are the    |
|   record.                  |
| ~ This is not a booking. ~ |
+----------------------------+
| [ Confirm my contact ]     |
| [ Use a different contact ]|
| [ View this request ]      |
+----------------------------+
```

### 6.4 Annotations

1. **Review region.** States plainly that nothing has been sent. `UX-08` is the last anonymous surface.
2. **Recipient region.** One deliberate recipient (`WA-02`). There is no recipient list, no "add another provider", no fan-out control anywhere on this surface.
3. **Anti-fan-out statement.** Required because "send to similar providers" is a preserved future branch (`DB-01`) and its absence must be legible now.
4. **Answer review region.** Every answer editable in place, returning to its composer stage without losing later answers. Empty variant: an unanswered `QUALITY` field shows as "Not provided" and is not an error.
5. **Expectation region.** Names the verification step before it happens, so the ask is not a surprise, and names `PendingVerification`'s guarantee in plain words.
6. **`NoResponse` disclosure, pre-send.** Not responding is a real fourth outcome and is system-observed, not a provider action (`§5.16`). Setting the expectation before send is the only honest place to do it.
7. **Not a booking.** A request is not a booking, an offer is not a booking, and no surface in V1 may imply transaction protection (`WA-03`).
8. **Primary action.** Leads to `UX-09`. On submit the request becomes `PendingVerification` — durable and invisible to the provider (`§5.2`).
9. **`UX-09` purpose.** Contact-channel verification, `WA-05`. This is the identity step, and it is the only one.
10. **Work-preservation statement.** `PendingVerification` guarantees the customer's work survives verification abandonment. Verification abandonment is instrumented **separately** from composer abandonment because the fixes differ (`§5.10`, `R-022`).
11. **Channel entry.** One channel is required. `DB-12` is fixed: a request must have a verified reachable customer contact channel before delivery.
12. **Disclosure-seam wording.** Contact data is never an attribute of the request (`ADR-010`); it resolves at delivery/render time through a recorded disclosure decision. The wording is deliberately identical under all three possible owner answers to the contact-disclosure question (`Q-008`, `A-010`, `OR-011`) — only the resolved value changes, not this surface (`§5.10`).
13. **Code entry with its error state.** Field-associated, announced, and recoverable without re-entering the request.
14. **Delivery-failure variant.** A `DeliveryAttempt` failure is visible to its own party and never presented as anyone else's non-response (`§5.16`). It states that the request is still saved.
15. **Honest verification claim.** `verificationBasis` = control of this contact channel was proven at time T. **No government identity verification and no "verified user" badge** (`§5.10`).
16. **`UX-10` state 1 — delivered.** Confirms that the request reached the provider, restates the four outcomes, restates the non-booking claim. It shows no estimated reply time because none exists. **The notification promise is qualified.** `surface-inventory.md` makes the on-screen confirmation authoritative and the notification a convenience, never the record; a bare *we will notify you* promises a delivery this architecture cannot guarantee, so the wording is *we will try*, and the surface names what the record actually is — this page and the customer's requests list.
17. **Multi-service path.** *Request another service for this event* seeds a **new `RequestDraft`** pre-filled from this request's `EventContext` carrying the customer-declared `eventGroupingHint` (`§5.5`). It is not a multi-service RFQ and not a routing action. No `Event` aggregate is drawn.
18. **`UX-10` state 2 — submitted and pending verification.** The second of the two states `surface-inventory.md` records for this surface, and the one that was previously missing. **"Sent" is a prohibited word here** (`docs/04-ux/identity-and-verification.md` `PV-02`): presenting an undelivered request as sent is the single most damaging honesty failure available on this surface, because the customer then waits for a reply that was never possible. The two states are worded so they cannot be confused — *reached the provider* versus *saved, has not reached the provider yet* — and state 2 says plainly that the provider cannot see the request and does not know it exists. `PV-01`'s promise is kept in the same breath: the request is durable and the customer's work is not lost.
19. **Recovery actions on state 2.** Confirming is the one action that changes the state, and correcting a mistyped address must not require re-composing the request (`PV-03`). No countdown, no "waiting…" animation, no polled status: there is no realtime transport (`PV-05`, `system-architecture.md` §3), and state refreshes on navigation or on an explicit user action. The same qualified notification wording as state 1 applies to the code itself.

---

## 7. `UX-18` provider request inbox and `UX-19` request detail

`UX-18` rendering `DOC`, `UX-19` rendering `LOCAL`. Authenticated provider context.

### 7.1 `UX-18` — desktop width

```text
+--------------------------------------------------------------------------+
| SUPEROLA    Hiring | MY BUSINESS: Mariachi Estrella           [ Account ] |
|             ~ You are in your business workspace. ~                 {1}  |
+--------------------------------------------------------------------------+
| {2} WORKSPACE      | {3} REQUESTS                                        |
| • Requests    (2)  |     All | New | Answered | Declined | No reply      |
| • Conversations    |     ~ Newest first. ~                          {4}  |
| • Offerings        | +-------------------------------------------------+ |
| • Profile          | | {5} Quinceanera · Houston · Mariachi ensemble   | |
| • Media            | |     Received 2 hours ago    «New»                | |
| • Publication      | |     Date: flexible · About 120 guests           | |
| • Settings         | |     [ Open ]                                    | |
|                    | +-------------------------------------------------+ |
| {6} TAKING         | | Wedding · Katy · Mariachi ensemble              | |
|     REQUESTS       | |     Received yesterday      «You sent an offer»   | |
| (x) Accepting      | |     [ Open ]                                    | |
| ( ) Paused         | +-------------------------------------------------+ |
| ( ) Not confirmed  | | Corporate · Sugar Land · Solo guitar            | |
| ~ This is an       | |     Received 9 days ago     «No reply from you»   | |
|   intake setting.  | |     ~ You have not replied. This is not a       | |
|   It is not a      | |       penalty and the request is still open. ~  | |
|   calendar and it  | |     [ Open ]                                {7} | |
|   is not date      | +-------------------------------------------------+ |
|   availability. ~  |                                                     |
| [ Save ]           | {8} ~ No requests yet. Requests appear here when a  |
|                    |       customer sends one. Nothing is required from  |
| {9} «Published»     |       you to receive them beyond staying published  |
|     Confirmed      |       and accepting requests. ~                     |
|     recently       |                                                     |
+--------------------------------------------------------------------------+
```

### 7.2 `UX-19` request detail — desktop width

```text
+--------------------------------------------------------------------------+
| {10} REQUEST · QUINCEANERA · HOUSTON                                     |
|      For your offering: Mariachi ensemble                                |
|      «New»  Received 2 hours ago                                          |
|      ~ You are viewing this as Mariachi Estrella del Norte. ~      {11}  |
+--------------------------------------------------------------------------+
| {12} WHAT THE CUSTOMER ASKED FOR                                         |
|      Occasion            Quinceanera                                     |
|      Place               Houston                                         |
|      Date                Not decided yet — flexible                {13}  |
|      Start time          6:00 PM (approximate)                           |
|      Duration            2 hours                                         |
|      Indoor or outdoor   Outdoor                                         |
|      Stage area          Not sure                                        |
|      Sound equipment     Customer expects you to bring it                |
|      Guests              About 120                                       |
|      Budget              Prefer not to say                         {14}  |
|      Planning stage      Not provided                                    |
|      Notes               "Looking for a 45-minute set, mostly            |
|                          traditional..."                                 |
|      ~ The event address is not part of this request. Ask the customer   |
|        if you need it. ~                                           {15}  |
+--------------------------------------------------------------------------+
| {16} HOW TO REPLY                                                        |
|      [ Ask a question ]  [ Send an offer ]  [ Decline ]                  |
|      ~ You can also do nothing. That is recorded as no reply and it is   |
|        not a penalty. ~                                            {17}  |
+--------------------------------------------------------------------------+
| {18} [ Report this request ]   [ Block this customer ]                   |
+--------------------------------------------------------------------------+
```

### 7.3 Phone width

```text
UX-18                                  UX-19
+----------------------------------+ +----------------------------------+
| MY BUSINESS: Mariachi Estrella   | | REQUEST · QUINCEANERA · HOUSTON  |
| ~ You are in your business       | | Offering: Mariachi ensemble      |
|   workspace. ~              {1}  | | «New» · 2 hours ago               |
| [ Switch to Hiring ]             | | ~ Viewing as Mariachi Estrella ~ |
+----------------------------------+ +----------------------------------+
| REQUESTS                    (2)  | | WHAT THE CUSTOMER ASKED FOR      |
| [ All ][ New ][ Answered ]       | | Occasion  Quinceanera            |
| [ Declined ][ No reply ]         | | Place     Houston                |
+----------------------------------+ | Date      Flexible               |
| Quinceanera · Houston            | | Start     6:00 PM (approx.)      |
| Mariachi ensemble                | | Duration  2 hours                |
| «New» · 2 hours ago               | | Indoor/outdoor  Outdoor          |
| Flexible date · ~120 guests      | | Stage     Not sure               |
| [ Open ]                         | | Sound     You bring it           |
+----------------------------------+ | Guests    About 120              |
| Wedding · Katy                   | | Budget    Prefer not to say      |
| «You sent an offer» · yesterday   | | Notes     "45-minute set..."     |
| [ Open ]                         | | ~ No event address in this       |
+----------------------------------+ |   request. ~                     |
| Corporate · Sugar Land           | +----------------------------------+
| «No reply from you» · 9 days      | | [ Ask a question ]          {19} |
| ~ Not a penalty. Still open. ~   | | [ Send an offer ]                |
| [ Open ]                         | | [ Decline ]                      |
+----------------------------------+ | ~ Doing nothing is recorded as   |
| TAKING REQUESTS             {6}  | |   no reply. Not a penalty. ~     |
| (x) Accepting ( ) Paused         | +----------------------------------+
| ( ) Not confirmed                |
| ~ Intake setting, not a calendar |
|   and not date availability. ~   |
+----------------------------------+
```

### 7.4 Annotations

1. **Dual-role context bar.** One `Account`, one session, no mode toggle that changes global state (`§5.15`, `ADR-004`). **Hiring** and **My business** are two persistent contexts shown side by side. The bar states the current capacity in words on every provider surface. No authorization decision depends on which context is displayed (`ADR-011`).
2. **Workspace navigation.** The full `§5.14` surface set and nothing more. **Not a CRM**: no pipeline stages, no custom fields, no tags, no bulk actions, no reporting builder, no calendar.
3. **Request list region.** States, newest first, unread. Filters are request states, not pipeline stages.
4. **Ordering statement.** Newest first is stated so the provider does not read the order as a priority score.
5. **Request row.** Enough `EventContext` to triage — occasion, place, offering, coarse date, coarse guest count. It does **not** contain the customer's name, contact channel, event address, or budget, because a row is a summary and the disclosure seam applies at render time (`ADR-010`).
6. **`RequestIntake` control.** `accepting` / `paused` (with an optional horizon and an optional provider-authored reason) / `unconfirmed`. The explanatory text is mandatory: this is the single highest-risk mislabelling in the product. It is **not** a calendar, **not** date availability, and its `paused` value is never worded as unavailability.
7. **`NoResponse` row.** System-observed, non-punitive, non-terminal (`§5.16`). The row says so. The window length is `POLICY PENDING` and no countdown is drawn.
8. **Empty variant.** Explains why the list is empty and what governs receiving requests (published plus accepting), without implying a ranking or effort obligation. No response-rate metric is shown here or anywhere.
9. **Publication and freshness region.** Provider-visible publication state with reasons (`§5.13`). This is where `Stale` → `reconfirm` is reachable — the highest-frequency provider maintenance action in the product.
10. **Request detail header.** Names the offering, because eligibility and intake are per `ServiceOffering` (`ADR-006`).
11. **Capacity statement.** Required on every surface where capacity is ambiguous (`§5.15`).
12. **Full `EventContext` and category answers.** This is the authenticated surface, so budget and free-text notes may appear here — and only here. Neither may ever appear in a notification body (`ADR-010`).
13. **`flexible` rendered as an answer.** Not as a blank and not as a missing value.
14. **An explicit "prefer not to say" is an answer, not a blank.** The customer selected it on `UX-07`, so this surface renders it as the answer it is. **"Not provided" is reserved for a field the customer genuinely skipped** — drawn here on *Planning stage*, so both renderings appear side by side. Collapsing the two would repeat the exact defect the repository forbids elsewhere: an explicit answer is not silence, `unknown` is never `Unreported` (`§2` of `docs/04-ux/design-canon.md`, `WF-C-15`), and absence must stay distinguishable from negation. Budget is optional either way and neither state is a defect or a signal about the customer.
15. **Event-address non-presence.** The event address is the highest-harm field in V1 and is never publicly exposed; it is not part of the request. The surface says so rather than leaving a silent gap.
16. **Response entry region.** Three provider actions, user-visibly distinct (`§5.16`). There is no "accept" and no counter-offer machinery beyond a new response.
17. **Fourth outcome stated.** Silence is never presented as a judgement (`Q-032`).
18. **Report and block.** A blocked party sees that posting is disabled and a policy-level reason only — never who reported and never the report content (`§5.16`).
19. **Phone response actions.** `§5.12` names the provider response surface as the single highest-risk mobile surface in the product because providers are mobile-first in practice (`DB-06`). The response actions must be reachable and completable without a desktop.

---

## 8. `UX-19` — provider response: clarify, decline, offer

Rendering `LOCAL`. Three forms on one surface, one active at a time.

### 8.1 Desktop width — clarify

```text
+--------------------------------------------------------------------------+
| {1} REPLY TO THIS REQUEST                                                |
|     ( ) Ask a question   ( ) Send an offer   ( ) Decline                 |
|     ~ Replying as Mariachi Estrella del Norte. ~                   {2}   |
+--------------------------------------------------------------------------+
| {3} ASK A QUESTION                                                       |
|     (______________________________________________________)             |
|     (______________________________________________________)             |
|     ~ This continues the conversation. It does not include a price and   |
|       it is not an offer. ~                                        {4}   |
|     [ Send question ]                                                    |
+--------------------------------------------------------------------------+
```

### 8.2 Desktop width — decline

```text
+--------------------------------------------------------------------------+
| {5} DECLINE THIS REQUEST                                                 |
|     Reason (optional, shown to the customer)                             |
|     ( ) Not available for that date                                {6}   |
|     ( ) Outside the area I travel to                                     |
|     ( ) Not the kind of event I take                                     |
|     ( ) Too short notice                                                 |
|     ( ) Other  (_________________________________)                       |
|     ~ Optional. Declining is a normal outcome and Superola does not      |
|       penalise it. ~                                               {7}   |
|     ~ Once sent, a decline cannot be edited. You can still reply in      |
|       the conversation. ~                                          {8}   |
|     [ Send decline ]     [ Cancel ]                                      |
+--------------------------------------------------------------------------+
```

### 8.3 Desktop width — offer

```text
+--------------------------------------------------------------------------+
| {9} SEND AN OFFER                                                        |
|     ~ An offer is a statement of what you would charge and what it       |
|       includes. It is not a booking and Superola does not process        |
|       payment. ~                                                  {10}   |
|                                                                          |
|     PRICE (optional)                                              {11}   |
|     [ ] I want to include a price                                        |
|     Amount    (_________)   Currency [v USD ]                            |
|     Basis     ( ) Total  ( ) Per hour  ( ) Per person  ( ) Starting from |
|     As of     (_________)  ~ The date this price applies from. ~         |
|     ~ If you do not include a price, the customer sees no price. We do   |
|       not estimate one. ~                                         {12}   |
|                                                                          |
|     WHAT IS INCLUDED                                                     |
|     (______________________________________________________)             |
|     WHAT IS NOT INCLUDED                                                 |
|     (______________________________________________________)             |
|     CONDITIONS                                                           |
|     (______________________________________________________)             |
|                                                                          |
| {13} > Offer expiry             ~ Not enabled for this category. ~       |
| {14} > Revise a previous offer  ~ Not enabled for this category. ~       |
|                                                                          |
|     [ Send offer ]      [ Cancel ]                                       |
+--------------------------------------------------------------------------+
```

### 8.4 Phone width — offer

```text
+----------------------------------+
| REPLY TO THIS REQUEST            |
| ( ) Ask a question               |
| (x) Send an offer                |
| ( ) Decline                      |
| ~ Replying as Mariachi Estrella ~|
+----------------------------------+
| SEND AN OFFER                    |
| ~ An offer is not a booking.     |
|   Superola does not process      |
|   payment. ~               {10}  |
+----------------------------------+
| PRICE (optional)           {11}  |
| [x] Include a price              |
| Amount   (_________)             |
| Currency [v USD ]                |
| Basis    ( ) Total               |
|          ( ) Per hour            |
|          ( ) Per person          |
|          ( ) Starting from       |
| As of    (_________)             |
| ~ No price means the customer    |
|   sees no price. ~         {12}  |
+----------------------------------+
| WHAT IS INCLUDED                 |
| (__________________)             |
| WHAT IS NOT INCLUDED             |
| (__________________)             |
| CONDITIONS                       |
| (__________________)             |
+----------------------------------+
| [ Send offer ]                   |
| [ Cancel ]                  {15} |
+----------------------------------+
```

### 8.5 Annotations

1. **Response-kind selector.** The three kinds are user-visibly distinct (`§5.16`) because they mean different things to the customer: `clarification` continues the thread with no price, `decline` closes this provider's participation, `offer` states terms. There is no fourth control, because the fourth outcome (`NoResponse`) is not a provider action.
2. **Capacity statement.** Repeated on the response form (`§5.15`).
3. **Clarification form.** Free text only.
4. **No-price statement on clarification.** Prevents a clarification from being read as an implicit quote.
5. **Decline form.** Reason is **optional, provider-authored, and never operator-derived** (`ADR-005`).
6. **Decline reason list.** Governed values, plus free text. "Not available for that date" is a **provider statement inside a request**, which is exactly where `WA-01` places feasibility. It is not an availability model, is not stored as availability, and never becomes a public claim on any surface.
7. **Non-punitive statement.** Declining is a normal outcome. No response-rate metric exists anywhere in the product to be damaged by it.
8. **Immutability statement.** A decline is immutable (`§5.16`). Stated before sending, not discovered afterwards.
9. **Offer form.** `ProviderResponse` with kind `offer` and an **optional** `PriceStatement`. `Quote` is not an entity.
10. **Non-booking statement.** No transaction language, no deposit, no payment, no protection claim (`WA-03`, `DB-02`).
11. **`PriceStatement` fields.** Currency explicit (`USD` baseline, never a bare symbol), basis, as-of date, inclusions, exclusions, conditions. Structured fields are archetype-gated (`structuredFields`).
12. **No-price honesty rule.** Where no `PriceStatement` exists, **no price is shown anywhere** — not a range, not an estimate, not a category average, not a placeholder. This is the load-bearing prohibition on this surface.
13. **Expiry disclosure.** `expiryEnabled` is archetype-gated. Where not enabled the control states so rather than being silently absent.
14. **Revision disclosure.** `revisionEnabled` is archetype-gated. Where not enabled, "current offer" is simply the most recent by recency with no acknowledgement machinery; a `Superseded` response stays visible to both parties (`§5.16`).
15. **Phone completability.** The whole offer form must be completable on a phone. Error variant: a validation error is field-associated and announced; validation itself lives in the domain (`ADR-011`), so the client form never becomes the authority.

---

## 9. `UX-12` / `UX-20` — conversation

Rendering `LOCAL` both sides. `UX-12` is the customer's request detail with response and conversation; `UX-20` is the provider's conversation.

### 9.1 `UX-12` customer view — desktop width

```text
+--------------------------------------------------------------------------+
| {1} QUINCEANERA · HOUSTON · MARIACHI ENSEMBLE                            |
|     With Mariachi Estrella del Norte     «They sent an offer»             |
|     ~ You are writing as yourself. ~                               {2}   |
+--------------------------------------------------------------------------+
| {3} ---- YOUR REQUEST · sent 14 March ----------------------------- {4}  |
|     Quinceanera · Houston · flexible date · 2 hours · outdoor ·          |
|     about 120 guests                                                     |
|     > See everything you sent                                            |
|                                                                          |
| {5} ---- SYSTEM · The provider opened your request -----------------     |
|     ~ System note. Not a message. ~                                      |
|                                                                          |
| {6} Mariachi Estrella del Norte · 14 March                               |
|     "Is the outdoor area covered? We need shade for the                  |
|      instruments."                                                       |
|                                                                          |
| {7} You · 14 March                                                       |
|     "Yes, there is a covered patio."                                     |
|                                                                          |
| {8} ==== OFFER FROM MARIACHI ESTRELLA DEL NORTE · 15 March ========      |
|     Price       USD $650 total                                           |
|     As of       15 March                                                 |
|     Includes    6 musicians, 2 hours, own sound system                   |
|     Not included Travel beyond 30 miles, extra hours                     |
|     Conditions  Covered performance area required                        |
|     ~ This is what the provider says they would charge. It is not a      |
|       booking, Superola does not process payment, and nothing is         |
|       reserved. ~                                                  {9}   |
|     [ Reply ]   [ Ask a question ]   [ Tell us what happened ]    {10}   |
|     ~ There is no accept button. If you agree, arrange it with the       |
|       provider. ~                                                 {11}   |
+--------------------------------------------------------------------------+
| {12} WRITE A MESSAGE                                                     |
|      (______________________________________________________)            |
|      ~ Do not send payment details here. ~                               |
|      [ Send ]                                                     {13}   |
+--------------------------------------------------------------------------+
| {14} [ Withdraw this request ]  [ Report ]  [ Block this provider ]      |
+--------------------------------------------------------------------------+
```

### 9.2 `UX-20` provider view — desktop width

```text
+--------------------------------------------------------------------------+
| {15} QUINCEANERA · HOUSTON · MARIACHI ENSEMBLE                           |
|      With a customer          «You sent an offer»                         |
|      ~ You are replying as Mariachi Estrella del Norte. ~                |
+--------------------------------------------------------------------------+
|      ---- THE REQUEST · received 14 March ----                           |
|      > See the full request                                              |
|      You · 14 March    "Is the outdoor area covered?..."                  |
|      Customer · 14 March  "Yes, there is a covered patio."               |
|      ==== YOUR OFFER · 15 March ====                                     |
|      USD $650 total · 6 musicians, 2 hours, own sound system             |
|      «Current offer»                                                      |
|      ---- SYSTEM · Delivery of your last notification failed ----        |
|      ~ We could not deliver your email notification. This does not       |
|        mean the customer did not reply. ~                         {16}   |
+--------------------------------------------------------------------------+
|      WRITE A MESSAGE   (____________________________)  [ Send ]          |
+--------------------------------------------------------------------------+
```

### 9.3 Phone width

```text
UX-12                                  UX-20
+----------------------------------+ +----------------------------------+
| QUINCEANERA · HOUSTON            | | QUINCEANERA · HOUSTON            |
| Mariachi Estrella del Norte      | | With a customer                  |
| «They sent an offer»               | | «You sent an offer»                |
| ~ You are writing as yourself. ~ | | ~ Replying as Mariachi           |
+----------------------------------+ |   Estrella del Norte. ~          |
| ---- YOUR REQUEST · 14 Mar ----  | +----------------------------------+
| > See everything you sent        | | ---- THE REQUEST · 14 Mar ----   |
| ---- SYSTEM · provider opened -- | | > See the full request           |
| ~ System note, not a message. ~  | | You · 14 Mar                     |
| Mariachi Estrella · 14 Mar       | | "Is the outdoor area covered?"   |
| "Is the outdoor area covered?"   | | Customer · 14 Mar                |
| You · 14 Mar                     | | "Yes, there is a covered patio." |
| "Yes, there is a covered patio." | | ==== YOUR OFFER · 15 Mar ====    |
| ==== OFFER · 15 Mar ====         | | USD $650 total  «Current offer»    |
| USD $650 total                   | | ---- SYSTEM · notification       |
| Includes 6 musicians, 2 hours    | |      delivery failed ----        |
| Not included travel beyond 30 mi | | ~ Does not mean the customer     |
| Conditions covered area required | |   did not reply. ~               |
| ~ Not a booking. Nothing is      | +----------------------------------+
|   reserved. ~                    | | (________________)  [ Send ]     |
| [ Reply ] [ Tell us what         | +----------------------------------+
|   happened ]                     |
| ~ There is no accept button. ~   |
+----------------------------------+
| (________________)   [ Send ]    |
+----------------------------------+
| [ Withdraw ] [ Report ] [ Block ]|
+----------------------------------+
```

### 9.4 Annotations

1. **Thread header.** Names the request, the counterparty, and the current state in text.
2. **Capacity statement.** *You are writing as yourself* on the customer side; *You are replying as {Business}* on the provider side (`§5.15`).
3. **One chronological thread.** Demand owns the request and each `ProviderResponse`; Conversation owns the clarifying messages. Rendering them as one thread is a presentation choice explicitly permitted by P02, and P04 takes it (`§5.16`).
4. **Request anchor.** The original request is an anchored, visually distinct entry, not a message.
5. **`ThreadEvent` system marker.** Visually distinct, labelled as a system note, and **never counted as a message** in any unread count.
6. **Message entry.** Author role always shown.
7. **Customer message.** Same rendering rules; the author label is the differentiator, not position or color.
8. **Offer anchor.** A `ProviderResponse` of kind `offer`, anchored and distinct from messages.
9. **Non-booking statement on the offer itself.** Placed inside the offer entry so it cannot be separated from it.
10. **Customer forward actions.** Reply, ask for clarification, report an outcome, withdraw (`§5.16`). That is the complete set.
11. **The absent accept button, stated.** **No accept button that creates an obligation exists anywhere in V1.** The absence is stated rather than left as a silent gap, because a customer who expects one will otherwise assume the flow is broken.
12. **Message composer.** A form post plus a server-rendered thread. **No realtime affordances**: no typing indicator, no presence dot, no read receipt, no "seen at", no live-updating badge (`system-architecture.md` §3).
13. **Unread and refresh.** Unread is computed server-side and rendered on navigation. **No global polling.** If a refresh is ever added it is on the open conversation surface only, is stated with its query cost, and is not the default (`§5.16`).
14. **Withdraw, report, block.** Withdraw is a customer action on the request. A blocked party sees that posting is disabled with a policy-level reason only.
15. **Provider mirror.** Structurally identical. The provider does not see the customer's name or contact channel here unless a recorded disclosure decision has resolved it (`ADR-010`).
16. **Notification failure marker.** Visible to its own party and explicitly **not** presented as the counterparty's non-response (`§5.16`). This is a `ThreadEvent`, not a message.

---

## 10. `UX-14` — customer outcome capture

Rendering `LOCAL`. Authenticated customer. Prompted on a governed trigger.

### 10.1 Desktop width

```text
+--------------------------------------------------------------------------+
| {1} WHAT HAPPENED WITH THIS REQUEST                                      |
|     Quinceanera · Houston · Mariachi Estrella del Norte                  |
|     ~ Answering helps us understand whether Superola is useful. It does  |
|       not change anything for the provider and it is not a review. ~     |
|                                                                    {2}   |
+--------------------------------------------------------------------------+
| {3} ( ) I am going ahead with this provider                              |
|     ( ) I am not going ahead with this provider                          |
|     ( ) I do not know yet                                                |
|     ~ "I do not know yet" is a real answer. Leaving this unanswered is   |
|       different and is fine. ~                                     {4}   |
+--------------------------------------------------------------------------+
| {5} v Why (optional)                                                     |
|     ( ) I chose another provider                                         |
|     ( ) The provider did not respond                                     |
|     ( ) The price was too high                                           |
|     ( ) My plans changed                                                 |
|     ( ) I am still deciding                                              |
|     ( ) Other  (_______________________________)                         |
|     ~ Optional. This is not shown to the provider as a rating. ~   {6}   |
+--------------------------------------------------------------------------+
| {7} ~ This is what you tell us. Superola does not process payment and    |
|      does not confirm bookings, so we have no other way to know. ~       |
|     [ Save ]     [ Skip for now ]                                        |
+--------------------------------------------------------------------------+
```

### 10.2 Phone width

```text
+----------------------------------+
| WHAT HAPPENED WITH THIS REQUEST  |
| Quinceanera · Houston            |
| Mariachi Estrella del Norte      |
| ~ Not a review. Nothing changes  |
|   for the provider. ~       {2}  |
+----------------------------------+
| ( ) Going ahead with them        |
| ( ) Not going ahead with them    |
| ( ) I do not know yet            |
| ~ "I do not know yet" is a real  |
|   answer. ~                 {4}  |
+----------------------------------+
| > Why (optional)            {5}  |
+----------------------------------+
| ~ This is what you tell us.      |
|   Superola does not process      |
|   payment or confirm bookings. ~ |
| [ Save ]   [ Skip for now ] {7}  |
+----------------------------------+
```

### 10.3 Annotations

1. **Outcome region.** Customer-only. A provider never reports an outcome and never sees one as a score.
2. **Not-a-review statement.** There are **no rating stars, no review text, no score, and no public reputation artefact anywhere in V1**. This statement is what keeps the surface from being read as one.
3. **The governed triple.** `ReportedOutcome` = `proceeding` / `not_proceeding` / `unknown`, basis `self_declared` (`ADR-003`). The wording avoids "booked", "hired", "completed", and "paid" entirely.
4. **`unknown` is not `Unreported`.** An explicit "I do not know yet" is a recorded answer; silence is `Unreported`. **They are never collapsed** and the surface says so, because a customer who thinks skipping and "do not know" are the same will pick either at random and destroy the distinction.
5. **Optional secondary reason.** A `PROPOSED` P04 extension to `ADR-003`, **alongside** the governed triple and never a replacement enum (`§5.16`). Argued by symmetry with the decline reason: the decline reason turns "do we need availability?" into a measurement; the outcome reason turns "why did this marketplace fail this customer?" into the same. Flagged here as an extension, not as existing P02.
   **Reveal contract.** This region appears in response to the governed-triple answer, which makes it a conditional reveal, and it obeys **the same announcement and focus contract as the composer** (`docs/04-ux/accessibility-and-responsive.md` §4): inserted in DOM order immediately after the control that caused it, announced **politely** and never assertively, **focus moves to the new region's heading rather than to its first input**, and **the reveal never reorders the already-answered outcome options**. The heading is programmatically focusable for that purpose only and is not in the tab order; on the no-JavaScript path the reveal is a server-rendered continuation using the same authored words. The same contract binds `UX-27`'s per-action-type reason reveal, which is not drawn in this document — the operator surfaces are recorded in `docs/04-ux/operator-surfaces.md` — and it is stated here so no reveal in the product is implemented without it.
6. **Non-punitive framing.** "The provider did not respond" is a demand-side signal, never a provider-facing score. No response-rate badge exists to display it on.
7. **Provenance statement plus skip.** States honestly that this is self-declared because there is no transaction to observe (`WA-03`). Skipping yields `Unreported`, which is a valid state, not an error.

---

## 11. Provider onboarding, publication checklist, and editing

`UX-17` rendering `LOCAL` + `I-1`, `I-3`; `UX-24` `DOC`; `UX-21` `LOCAL`; `UX-22` `LOCAL` + `I-1`.

### 11.1 `UX-17` + `UX-24` — desktop width

```text
+--------------------------------------------------------------------------+
| {1} SET UP YOUR BUSINESS ON SUPEROLA                                     |
|     Account > Business > YOUR FIRST SERVICE > Where you work >           |
|     Service details > About you > Photos and links > Preview > Publish   |
|     ~ Progress is saved at every step. ~                                 |
+--------------------------------------------------------------------------+
| {2} YOUR FIRST SERVICE          | {6} WHAT YOU STILL NEED TO APPEAR      |
|                                 |     IN SEARCH                          |
| What service do you offer       |     ~ Live checklist. Updates as you   |
| [v_________________________]{3} |       go. ~                       {7}  |
| ~ Choose from our list. ~       |                                        |
|                                 |     [x] Confirm your contact channel   |
| Cannot find your service?       |         ~ Why: so customers can reach  |
| [ Suggest a service ]      {4}  |           you. Unlocks: receiving      |
| ~ We review suggestions. It     |           requests. ~                  |
|   does not create a category    |     [x] Add one service                |
|   and it does not publish. ~    |         ~ Why: search is by service.   |
|                                 |           Unlocks: appearing in that   |
| ~ Your business can have more   |           service's results. ~         |
|   than one service. Each one    |     [ ] Say where you work        {8}  |
|   is listed separately. ~  {5}  |         ~ Why: we match by area.       |
|                                 |           Unlocks: appearing in that   |
|                                 |           area's results. ~            |
|                                 |     [ ] Answer the required details    |
|                                 |         for this service               |
|                                 |         ~ Why: customers filter on     |
|                                 |           them. ~                      |
|                                 |     [ ] Confirm you have the rights    |
|                                 |         to the photos you upload  {9}  |
|                                 |     [ ] Base address with enough       |
|                                 |         precision for this service     |
|                                 |         ~ Your exact address is never  |
|                                 |           shown publicly. ~      {10}  |
|                                 |                                        |
|                                 | {11} «Draft — not visible to customers» |
|                                 |      [ Preview my public profile ]     |
|                                 |      ~ You can preview before you      |
|                                 |        finish. ~                       |
+--------------------------------------------------------------------------+
| {12} [ Back ]                                           [ Continue ]     |
+--------------------------------------------------------------------------+
```

`UX-24` publication state and requirements, after publishing:

```text
+--------------------------------------------------------------------------+
| {13} PUBLICATION                                                         |
|      «Published»   Confirmed 12 days ago                                  |
|      ~ Customers can find you. ~                                         |
|      [ Confirm my details are current ]                           {14}   |
|                                                                          |
|      Other states you may see, and what they mean:                {15}   |
|      «Draft»           Not visible. You are still setting up.             |
|      «Pending review»  We are reviewing this service category.            |
|      «Stale»           Still findable, shown as not recently confirmed.   |
|      «Deactivated»     You turned it off. Turn it back on any time.       |
|      «Suspended»       We paused it. Reason and next step shown here.     |
|      «Rejected»        Not approved. Reason and what to change shown.     |
|      «Closed»          Ended.                                             |
|      ~ Customers only ever see published or not recently confirmed. ~    |
+--------------------------------------------------------------------------+
```

### 11.2 `UX-21` + `UX-22` editing — desktop width

```text
+--------------------------------------------------------------------------+
| {16} EDIT: MARIACHI ENSEMBLE                     «Published»              |
|      ~ Changes are saved as a draft of this service until you publish    |
|        them. Your current public version stays up. ~              {17}   |
+--------------------------------------------------------------------------+
| {18} WHERE YOU WORK                                                      |
|      How do you work    (x) I travel to the customer                     |
|                         ( ) The customer comes to me                {19}  |
|      Based in           [v Houston ]                                     |
|      I travel up to     (____) [v miles ]                          {20}  |
|      Also serving       [v Katy ] [v Sugar Land ] [ + Add a place ]      |
|      ~ Customers see these place names and your travel distance. They    |
|        never see your street address. ~                                  |
|      !! We could not place "Sugar Land, TX 77478" on our list. Pick a    |
|         place from the list or leave it out. Your service still works    |
|         everywhere else you listed.                                {21}   |
+--------------------------------------------------------------------------+
| {22} SERVICE DETAILS                                                     |
|      Ensemble size        [v 6-10 ]                       required       |
|      Languages            [x] Spanish [x] English         required       |
|      Brings own sound     (x) Yes ( ) No                  required       |
|      Typical set length   (____) minutes                  optional       |
|      ~ These questions come from the service you chose. Choosing a       |
|        different service changes them. ~                          {23}   |
+--------------------------------------------------------------------------+
| {24} TAKING REQUESTS FOR THIS SERVICE                                    |
|      (x) Accepting requests                                              |
|      ( ) Paused    until (________)  reason (__________________)         |
|      ( ) Not confirmed                                                   |
|      ~ This controls whether customers can send you a request. It is     |
|        not a calendar, it is not your availability on any date, and      |
|        customers are told so. ~                                   {25}   |
+--------------------------------------------------------------------------+
| {26} PHOTOS, AUDIO AND VIDEO                                             |
|      [ Choose photos ]                                                   |
|      • rehearsal.jpg   «Processing»                                       |
|      • stage-2024.jpg  «Ready»   Alt text (__________________)     {27}   |
|      • quartet.jpg     «Could not be processed»  [ Remove ]               |
|      Audio or video link  (________________________)  [ Add ]     {28}   |
|      • Live set 2024   «Link is not working»  [ Fix ] [ Remove ]          |
|      ~ Audio and video are links to your own pages. Superola does not    |
|        host them. ~                                                      |
+--------------------------------------------------------------------------+
| {29} [ Preview ]     [ Save draft ]     [ Publish changes ]              |
+--------------------------------------------------------------------------+
```

### 11.3 Phone width

```text
UX-17 + UX-24                          UX-22
+----------------------------------+ +----------------------------------+
| SET UP YOUR BUSINESS             | | EDIT: MARIACHI ENSEMBLE          |
| Stage: YOUR FIRST SERVICE        | | «Published»                        |
| ~ Saved at every step. ~         | | ~ Your current public version    |
+----------------------------------+ |   stays up while you edit. ~     |
| What service do you offer        | +----------------------------------+
| [v____________________]          | | WHERE YOU WORK                   |
| [ Suggest a service ]            | | (x) I travel to the customer     |
| ~ Suggesting does not create a   | | ( ) The customer comes to me     |
|   category or publish you. ~     | | Based in  [v Houston ]           |
+----------------------------------+ | Travel up to (____) [v miles ]   |
| v WHAT YOU STILL NEED       {30} | | Also serving                     |
|   [x] Confirm contact channel    | | [v Katy ] [v Sugar Land ]        |
|   [x] Add one service            | | [ + Add a place ]                |
|   [ ] Say where you work         | | ~ Street address never shown. ~  |
|       ~ Unlocks: appearing in    | +----------------------------------+
|         that area. ~             | | SERVICE DETAILS                  |
|   [ ] Required service details   | | Ensemble size [v 6-10 ] required |
|   [ ] Photo rights               | | Languages [x] ES [x] EN required |
|   [ ] Base address precision     | | Own sound (x) Yes ( ) No required|
+----------------------------------+ +----------------------------------+
| «Draft — not visible»              | | TAKING REQUESTS             {25} |
| [ Preview my public profile ]    | | (x) Accepting                    |
+----------------------------------+ | ( ) Paused until (______)        |
| [ Continue ]   [ Back ]          | | ( ) Not confirmed                |
+----------------------------------+ | ~ Not a calendar. Not your       |
                                     |   availability on any date. ~    |
                                     +----------------------------------+
                                     | PHOTOS, AUDIO AND VIDEO          |
                                     | [ Choose photos ]                |
                                     | • rehearsal.jpg «Processing»       |
                                     | • stage-2024.jpg «Ready»           |
                                     |   Alt text (___________)         |
                                     | Link (___________)  [ Add ]      |
                                     | • Live set 2024                  |
                                     |   «Link is not working»            |
                                     +----------------------------------+
                                     | [ Preview ] [ Save draft ]       |
                                     | [ Publish changes ]              |
                                     +----------------------------------+
```

### 11.4 Annotations

1. **Onboarding stage indicator.** The `§5.13` order: Account → Business → first `ServiceOffering` → service area → category attributes → narrative → media → preview → publish. Named stages, not a numeric denominator, for the same reason as the composer.
2. **Offering creation.** The first `ServiceOffering` is where publication requirements attach, because eligibility is per offering, never per `Business` (`ADR-006`).
3. **Governed category picker.** Island `I-1`, plain-select fallback.
4. **`CategoryProposal` entry.** Provider category input becomes a `CategoryProposal`, **never a node** (`ADR-007`). The wording states plainly that suggesting neither creates a category nor publishes anything, so the provider does not wait on it.
5. **Multiple offerings statement.** Sets the correct mental model early: the business is not the unit of listing.
6. **Live requirements checklist — the load-bearing region of `UX-17`.** Visible **from the first screen** (`§5.13`), not at the end. It separates *required to create an account* (a contact channel and proven control of it) from *required to appear in discovery*.
7. **Why plus unlocks.** Each item states why it is required and what it unlocks. A checklist that only lists gates teaches nothing and produces support load.
8. **Unchecked items are not errors.** An incomplete checklist is a normal state during onboarding, not a validation failure, and is never rendered as one.
9. **Media rights acknowledgement.** A publication gate item (`§5.13`).
10. **Base location precision.** `BaseLocation` coordinate precision sufficient for the archetype's eligibility predicate (`GeoPoint` carries precision and provenance). The privacy invariant is stated inline: the precise base location is never publicly exposed.
11. **Preview before completeness.** **Value before completeness** (`§5.13`): the provider can preview the public profile before satisfying every requirement, with the checklist naming exactly what remains. What must not happen is silent publication of an incomplete profile, so the state chip states `«Draft — not visible to customers»` at the same time.
12. **Step navigation.** Progress is saved at every step; nothing is lost.
13. **`UX-24` publication state.** Provider-visible with reasons.
14. **Reconfirm.** `Stale` → `reconfirm` is a **one-action path from a notification link** (`§5.13`) — the highest-frequency provider maintenance action in the product. It appears here and is reachable directly from `UX-35`.
15. **State glossary with the asymmetry stated.** All eight publication states are provider-visible with reasons. Customers see only `Published` and `Stale`; `Suspended` and `Deactivated` are indistinguishable to a customer (`§4.15`), and the provider-facing copy says which states customers can see so no provider assumes a suspension is publicly labelled.
16. **Editing an already-published offering.** State chip present.
17. **Edit-safety statement.** Editing does not unpublish. Related rule: raising a publication requirement must not retroactively unpublish (`§5.13`).
18. **Service-area region (`UX-22`).** `ServiceAreaDeclaration`, archetype-valid.
19. **Archetype framing.** Venue = the customer travels; performer = the provider travels (`§5.9`). The same field means different things per archetype, so the framing question comes first.
20. **Units preserved as entered.** Miles or kilometres as the provider chose; no silent conversion.
21. **Unresolved place — `undetermined`.** Surfaced with its consequence, never coerced into a nearest match. Free text that will not resolve goes to the operator geocode queue (`UX-31`) rather than being approximated.
22. **Category attributes.** Rendered from operator-governed `CategoryAttributeDefinition`s, **never provider-writable as definitions** (`ADR-007`). Required and optional are labelled in text. A new Category renders here with no new screen.
23. **Change consequence stated.** Same before-the-fact discipline as the composer's category change.
24. **`RequestIntake` control, per offering.** `accepting` / `paused` with an optional horizon and an optional provider-authored reason / `unconfirmed`.
25. **The non-claim on the control itself.** Placed on the control, not in a help page, and it states that customers are told the same thing. This is the exact point where a provider would otherwise form the belief that pausing is a calendar block.
26. **Media region.** Island `I-3`.
27. **Processing state.** `ADR-018` requires an explicit processing state with derivatives pre-generated at upload from a small fixed enumerated set. Alt text is captured per placement and is a publication-quality item. Failure state is visible and recoverable.
28. **Audio and video are links, never uploads** (`ADR-018`). A dead external link is a real state with a provider-visible marker and an operator sweep behind it.
29. **Preview, save, publish.** Three distinct actions. Publication gates on the database fact that media exists, never on whether a vendor is currently serving it.
30. **Phone checklist.** Collapsible but present on every stage, never deferred to a final screen.

---

## 12. Constraints these wireframes are bound by

Any drawing, prototype, or later visual work that breaks one of these is a defect against P04, not a style disagreement.

| ID | Constraint | Applies to |
|---|---|---|
| `WF-C-01` | **No rendered map on any surface.** Coarse `Place` labels and declared coverage in words everywhere; a link out to the customer's own map application only where a **published fixed venue address** exists (`PP-63`, `§5.9`) — never from a declared base area. | All |
| `WF-C-02` | **No calendar, no availability picker, no availability claim, and no availability boolean.** `V1 has no availability model` appears in substance on every profile and search surface (`ADR-005`). | `UX-01`, `UX-04`, `UX-05`, `UX-07`, `UX-22` |
| `WF-C-03` | **No accept-quote button and no control that creates an obligation.** Customer forward actions are reply, clarify, report an outcome, withdraw (`§5.16`). | `UX-12`, `UX-20` |
| `WF-C-04` | **No fan-out, no multi-recipient control, no "send to similar providers", and no conversion of a search into a message** (`WA-02`). | `UX-04`, `UX-07`, `UX-08` |
| `WF-C-05` | **No rating stars, review counts, scores, response-rate or response-time badges, or any public reputation artefact.** `ResponsivenessObservation` and `FreshnessObservation` are never publicly exposed (`§4.16`). | All |
| `WF-C-06` | **No price where no `PriceStatement` exists.** No estimate, range, average, or placeholder. Currency always explicit. | `UX-04`, `UX-05`, `UX-12`, `UX-19` |
| `WF-C-07` | **`RequestIntake` is rendered as text**, never as color, position, or shape alone, and `paused` is never worded as date unavailability (`§5.11`). | `UX-04`, `UX-05`, `UX-18`, `UX-22` |
| `WF-C-08` | **`placementBasis` `organic` disclosed per result slot, with a per-slot ranking explanation.** No `featured` or `sponsored` marker exists in V1 (`§4.14`). | `UX-04` |
| `WF-C-09` | **No live facet counts.** P04 recommendation on `Q-035`; David decides (`§5.6`). | `UX-04` |
| `WF-C-10` | **No realtime affordance**: no typing indicator, presence, read receipt, live badge, or push. No global polling (`system-architecture.md` §3). | `UX-12`, `UX-18`, `UX-20` |
| `WF-C-11` | **Named-stage progress only.** No `step N of M` where `M` is data-dependent (`§5.1`, `§5.11`). | `UX-07`, `UX-17` |
| `WF-C-12` | **Never publicly exposed:** precise provider base location, customer name or contact channel, event address, event date, guest count, budget, request free text, conversation text, offer amount and terms, `Suspended` as a state, report contents, reporter identity (`§4.16`). | All |
| `WF-C-13` | **No notification body may carry** counterparty contact data, request free text, event address or date, guest count, budget, offer amounts or terms, or conversation content (`ADR-010`). | `UX-35` |
| `WF-C-14` | **No surface may state or imply that in-platform contact is protected** (`ADR-010`). Free text is classified as possibly containing contact data regardless of policy. | `UX-07`, `UX-12`, `UX-20` |
| `WF-C-15` | **`unknown` and `Unreported` are never collapsed**, and no outcome wording implies booking, payment, or completion (`ADR-003`, `WA-03`). | `UX-14` |
| `WF-C-16` | **Every state carries text**; every input carries a programmatically associated label; every error is associated with its field and announced; dynamic reveal announces politely and moves focus to the region heading (`§5.11`). | All |
| `WF-C-17` | **No visual decision is expressed or implied here.** Color, typography, iconography, spacing, component choice, and brand remain undecided and outside P04 (`§5.18`). | All |

`G-06` — what "available" promises a customer — is UNSATISFIED. Every drawing in this document rests on `WA-01` `DAVID WORKING ASSUMPTION — OWNER VALIDATION PENDING`. If the owner answers `G-06` differently, `WF-C-02` changes and `UX-04`, `UX-05`, `UX-07`, and `UX-22` are all re-drawn.
