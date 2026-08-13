# Rendering Evidence — P04 UX Architecture

> Status: `PROPOSED — OWNER VALIDATION REQUIRED`. Provenance: `TECHNICAL_DISCOVERY`. This is a P04 UX architecture proposal, not an approved design and not a visual specification.
> Working assumptions: `WA-01`–`WA-05` (see `docs/04-ux/README.md`). `G-06` is UNSATISFIED.

This document supplies the per-surface evidence that `ADR-020` requires in order to be closed, and it recommends one of the three options that ADR holds open. It does not approve anything: `ADR-020` is owned by David as the technical decision owner, and nothing here changes that. It does not change `ADR-013`'s platform decision — Kotlin, Spring Boot, one deployable, and the runtime baseline are fixed, and `ADR-020` exists precisely because the rendering portion was split out of `ADR-013` and is decidable on different evidence at a different altitude. It is not a template specification, a component inventory, a library selection, or a visual design. No vendor, library, or framework is named as a recommendation anywhere in this document, and none is required to evaluate the argument.

## 1. What `ADR-020` asks for, and what P04 can honestly supply

`ADR-020` does not close on a global preference. It closes when P04 delivers four items **per surface rather than globally**. The ADR's Rationale section names all four in a single sentence; the quoted fragment in each row below is taken verbatim from that sentence, and everything outside the quotation marks is this document's gloss, not the ADR's wording.

| ID | Required item (fragment quoted verbatim from `ADR-020` Rationale) | Can P04 supply it? |
|---|---|---|
| `RQ-01` | "the interaction requirement evidenced by a named journey" — the named journey being a document in `docs/04-ux/`. | Yes. Every row in §3 names the journey document that evidences the requirement. |
| `RQ-02` | "whether client state must survive navigation" on that surface. | Yes. This is answerable from the domain model and the journey without building anything. |
| `RQ-03` | "whether progressive enhancement was attempted and at what cost". | **No measurement is available.** See below. |
| `RQ-04` | "whether the surface is public" — and, if it is, whether `ADR-020` Decision rule 4 therefore binds it. | Yes. This is a fact of the surface inventory. |

### 1.1 The honest statement about `RQ-03`, made once

**Nothing has been implemented. Superola V1 does not exist. No progressive-enhancement implementation has been attempted on any surface, so `ADR-020`'s item 3 has no measurement behind it anywhere in this document.** Every `RQ-03` entry in §3 is an argued estimate: a reasoned claim about what a server-rendered plus progressively-enhanced treatment of that surface would require, derived from the surface's stated interaction requirement, the domain objects it reads and writes, and the constraints in §7. It is not a measurement, it is not a benchmark, and it must not be cited as one.

This is stated here once, plainly, rather than repeated as a hedge on every one of the thirty-six rows in §3 that carries a progressive-enhancement judgement. The consequence is recorded in §9: `RQ-03` is the item that `ADR-020` cannot fully receive until code exists, and the ADR's own reopening trigger — "a failed progressive-enhancement attempt" — is the mechanism by which a real measurement later overrides an argued estimate here. Evidence: `PROPOSED`. Provenance: `TECHNICAL_DISCOVERY`.

### 1.2 Classification vocabulary

Used verbatim across P04, and identical to `docs/04-ux/surface-inventory.md`:

- `DOC` — mostly document and navigation. Server rendering is sufficient. No island is required **by the class**; an island may still be attached to a `DOC` surface and must then be named.
- `LOCAL` — local interactive state that does not need to survive navigation, **or bounded navigation-persistent browser-local state whose durable owner is a server-side domain object** (the one exception, `UX-07`; see below). A progressive-enhancement or island treatment, decided per surface.
- `RICH` — rich client state that must survive navigation. **Would justify Option B for that surface.**

The distinction that does the work is `RQ-02`. `LOCAL` and `RICH` differ on exactly one question: when the user navigates away and back, does the interface have to reconstruct state that lives nowhere but the client? If the answer is no — because the state is in the URL, in a form post, or in a server-persisted domain object — the surface is not `RICH`, however interactive it feels.

**One surface answers "yes, for a bounded window", and it is recorded as an exception rather than argued away.** `UX-07` holds navigation-persistent anonymous browser-local state before an `Account` exists (`WA-05`). It is still classed `LOCAL` because the exception is bounded on three conditions, all of which must hold together: a **named island** covers it (`I-2`, §7), a **named domain object** takes ownership the moment one can exist (`RequestDraft`, `ADR-003`), and the surface **still degrades to a working server-rendered path** with no script at all. A surface holding navigation-persistent client state with no domain owner and no working degradation would be `RICH`, and this exception must not be stretched to cover one. **The classification is a P04 judgement about which rendering model that requirement warrants — not a claim that the state does not exist.**

## 2. Journey evidence used in this document

Each `RQ-01` entry names one of the following. Where a journey document is still being written in P04, the reference is to the named document and its stated flow, not to an approved artifact.

| ID | Journey document | Covers |
|---|---|---|
| `J-01` | `docs/04-ux/customer-journey.md` | Need entry, discovery, results, profile evaluation, request composition, verification, request detail, conversation, outcome. |
| `J-02` | `docs/04-ux/provider-onboarding.md` + `docs/04-ux/provider-workspace.md` | Onboarding, publication, request inbox, response, conversation, offering and media maintenance, `RequestIntake` control. |
| `J-03` | `docs/04-ux/operator-surfaces.md` | **Eight domain queues plus a cross-queue index, on nine surfaces (`UX-26`–`UX-34`); `UX-29` carries two case kinds.** Audited private-content access is `UX-34`, one of the eight, and is not counted a second time. |
| `J-04` | `docs/04-ux/surface-inventory.md` | Per-surface states, empty, error and degraded cases. |

## 3. Per-surface classification

Columns answer `ADR-020`'s four items in order. **"Public?"** answers `RQ-04`; a `Yes` means the surface must render its governed content without executing script. **"State survives navigation?"** answers `RQ-02`. **"PE feasibility and argued cost"** answers `RQ-03` and is an argued estimate throughout, per §1.1.

**Coverage, stated exactly.** The register holds thirty-nine surfaces: thirty-six V1 and three `FUTURE`. All four items are recorded for **all thirty-seven classified surfaces**. `UX-37` and `UX-38` are `FUTURE`, are not designed, and are **deliberately left unclassified rather than guessed at** — a `FUTURE` surface must not be used to argue a V1 rendering model. Of the thirty-seven classified surfaces, thirty-six carry a rendering class and therefore an `RQ-03` judgement; `UX-35` is an email body, classified `n/a`, and has no rendering judgement to make.

**Scope of rule 4, and where P04 extends it.** `ADR-020` Decision rule 4 names `UX-01`–`UX-06` and `UX-16` as the public surfaces it binds. **P04 extends the same constraint to `UX-07` and `UX-08`** — this is a P04 extension, not the ADR's own scope — because under `WA-05` both are reached anonymously and `UX-08` is the last anonymous surface before submit, so a script-dependent composer or review page would break the anonymous path the acquisition hypothesis depends on. If the owner declines the extension, `UX-07` and `UX-08` revert to ordinary progressive-enhancement treatment and nothing else in §3 changes.

| ID | Surface | Public? | Interaction requirement (`RQ-01`) | State survives navigation? (`RQ-02`) | PE feasibility and argued cost (`RQ-03`, argued) | Class |
|---|---|---|---|---|---|---|
| `UX-01` | Home / need entry | Yes | Two governed selections — `Category` and `Place` — entered by browse or type-ahead over governed labels and synonyms (`J-01`). | No. Both selections leave as a request and return in the URL. | High. A pair of governed selects plus a submit button is a working no-script path. The type-ahead itself is `I-1` and exists to avoid a round trip per keystroke, not to hold state. | `DOC` + `I-1` |
| `UX-02` | Category / archetype browse | Yes | Navigate a governed taxonomy tree (`J-01`). | No. | High. A link tree over governed data. No client behaviour required at all. | `DOC` |
| `UX-03` | Market page (Category × Place) | Yes | Read a governed `Market` landing page; enter the request path (`J-01`). | No. | High. This is the acquisition surface; script-free rendering is a requirement, not a cost. | `DOC` |
| `UX-04` | Search results | Yes | Filter, relax, paginate; read a per-slot ranking explanation and the `placementBasis` disclosure (`J-01`). | No. Filter and page state live in the querystring, which is also what makes results linkable. | High. A `GET` form with querystring state; the ranking explanation is a disclosure element, not a widget. **No live facet counts in V1** (`docs/04-ux/design-canon.md` §5.6) removes the one filter feature that would need per-interaction server chatter. | `DOC` + `I-1` |
| `UX-05` | Provider profile (public) | Yes | Read identity, service, coarse location, media, the `V1 has no availability model` non-claim, `RequestIntake`, freshness; start a request (`J-01`). | No. | High. Media is a list of hosted images with pre-generated derivatives (`ADR-018`); an enlarged view is an optional enhancement over a plain anchor. | `DOC` |
| `UX-06` | Informational / trust pages | Yes | Read (`J-01`). | No. | High. Static governed content. | `DOC` |
| `UX-07` | Request composer (progressive intake) | Yes | Archetype-conditional question reveal governed by the five classes of `docs/04-ux/request-intake.md` §6; a named-stage progress indicator; an anonymous draft that survives the session (`J-01`, `docs/04-ux/design-canon.md` §5.1–§5.3). | **Yes — the one bounded exception in the register.** After an `Account` exists it is held by `RequestDraft`, a server-persisted domain aggregate (`ADR-003`). **Before one exists (`WA-05`), this surface holds navigation-persistent anonymous browser-local state that lives nowhere but the client for that window.** P04 proposes covering it with the bounded `I-2` island rather than a richer client rendering model, and classes it `LOCAL` on that basis. Stated in these same words in `docs/04-ux/surface-inventory.md` `UX-07` and in §4.1 below. | Medium. One server-rendered step per `POST` is a complete working path, and is the mandated degradation. Before an `Account` exists (`WA-05`) the anonymous draft needs a browser-local store; `I-2` buys per-answer reveal without a round trip and pre-`Account` survival. Estimated as the largest single PE cost in V1. | `LOCAL` + `I-1`, `I-2` |
| `UX-08` | Request review and send | Yes | Read the assembled draft, correct any answer, submit (`J-01`). | No. It reads the draft; it does not hold it. | High. A rendered summary with per-answer edit links back into `UX-07`. | `DOC` |
| `UX-09` | Identity and channel verification | Transitional | Supply a contact channel, receive a code, enter it, resend (`J-01`, `docs/04-ux/design-canon.md` §5.10). | No. The request is already durable as `PendingVerification`. | High. Form posts. A resend countdown is cosmetic and degrades to a plain resend button. | `DOC` |
| `UX-10` | Request submitted confirmation | No (Auth) | Read what happens next, with no delivery or response promise (`J-01`). | No. | High. | `DOC` |
| `UX-11` | My requests (customer) | No (Auth) | List requests by state, unread computed server-side and rendered on navigation (`J-01`, `docs/04-ux/design-canon.md` §5.16). | No. **No global polling** by architecture (`docs/02-architecture/system-architecture.md` §3). | High. A server-rendered list. The absence of realtime transport removes the requirement that would have made this interactive. | `DOC` |
| `UX-12` | Request detail: response + conversation (customer) | No (Auth) | Read one chronological thread with the request and each `ProviderResponse` anchored and visually distinct; post a message; report an outcome; withdraw (`J-01`, `docs/04-ux/design-canon.md` §5.16). | No. See §4.2. | High. A form post plus a server-rendered thread. `LOCAL` covers composer-text convenience only. | `LOCAL` |
| `UX-13` | My drafts | No (Auth) | List, resume, discard server-persisted `RequestDraft`s (`J-01`). | No. | High. | `DOC` |
| `UX-14` | Outcome capture | No (Auth) | Answer the governed triple `proceeding` / `not_proceeding` / `unknown`; optionally add the secondary reason (`docs/04-ux/design-canon.md` §5.16) revealed by the primary answer (`J-01`). | No. | High. A conditional reveal within one form, degrading to an always-visible optional field. | `LOCAL` |
| `UX-15` | Account and settings | No (Auth) | Locale, notification and consent forms (`J-01`, `J-02`). | No. | High. | `DOC` |
| `UX-16` | Sign in / sign up | Yes | Credential and channel forms (`J-01`). | No. | High. **A public sign-in page that requires script is an acquisition failure**, not only an accessibility one. | `DOC` |
| `UX-17` | Provider onboarding (progressive) | No (Auth) | Ordered multi-step with a live requirements checklist from the first screen, saved at every step; profile preview before completeness (`J-02`, `docs/04-ux/design-canon.md` §5.13). | No — **because an `Account` exists from step one, so every step is a server write.** This is the structural difference from `UX-07`. | High. Each step is a `POST`; the checklist re-renders from `publicationGateMet` state on navigation. `I-1` serves category and place entry; `I-3` serves media. | `LOCAL` + `I-1`, `I-3` |
| `UX-18` | Provider workspace home / request inbox | No (Auth) | List requests newest-first by state, with unread computed server-side (`J-02`). | No. | High. | `DOC` |
| `UX-19` | Provider request detail and respond | No (Auth) | Read the full `EventContext` and category answers; respond as `clarification`, `decline` (optional reason) or `offer` (optional `PriceStatement`), with archetype-gated structured fields (`J-02`, `docs/04-ux/design-canon.md` §5.16). | No. The response is one `POST`; there is no intermediate state worth preserving across navigation. | Medium-high. Three response kinds are three server-rendered forms, or one form with a governed reveal. **The identified risk on this surface is phone-width layout (`docs/04-ux/design-canon.md` §5.12), which is not a rendering-model problem** and is not solved by a client framework. | `LOCAL` |
| `UX-20` | Provider conversation | No (Auth) | Same thread and composer as `UX-12`, from the provider side with the acting `Business` stated (`J-02`, `docs/04-ux/design-canon.md` §5.15). | No. | High. Form post plus server-rendered thread. | `LOCAL` |
| `UX-21` | Profile editor | No (Auth) | Long-form narrative editing with explicit `contentLocale`; media rights acknowledgement; preview (`J-02`). | No. Every field is a server write. | High. A long form, sectioned, saved per section. | `LOCAL` |
| `UX-22` | Offering editor (category attributes, service area, `RequestIntake`) | No (Auth) | Render the governed `CategoryAttributeDefinition` set for the selected archetype; declare a `ServiceAreaDeclaration`; set `RequestIntake` (`J-02`, `docs/04-ux/design-canon.md` §5.13). | No. | High, and this is where server rendering is strongest: **the attribute set is governed data, so the form is generated from the same source that validates it.** A client framework would need the same definitions shipped twice. | `LOCAL` + `I-1` |
| `UX-23` | Media manager | No (Auth) | Multi-file selection, per-file upload progress, visible pre-generated-derivative processing state, dead external A/V link state (`J-02`, `ADR-018`). | No across navigation — the processing state is a database fact re-read on every render. Yes **within** the page for in-flight uploads only. | Medium. A multi-file `<input type="file">` `POST` is a working path and is the mandated degradation, but it is one opaque blocking request for a provider uploading eight images, with no per-file progress and no partial success. `I-3` exists for that gap. | `LOCAL` + `I-3` |
| `UX-24` | Publication state and requirements checklist | No (Auth) | Read publication state with reasons and the outstanding requirement list (`J-02`). | No. | High. | `DOC` |
| `UX-25` | Provider settings and notifications | No (Auth) | Forms (`J-02`). | No. | High. | `DOC` |
| `UX-26` | Operator case queue index | No (Auth) | Read the **eight domain queues** (`UX-27`–`UX-34`) with counts, oldest-item age and priority from the cross-queue index; `UX-29` carries two case kinds (`J-03`). | No. | High. | `DOC` |
| `UX-27` | Report / moderation case detail | No (Auth) | Read the case; take a reason-tagged auditable action; reveal private content only through an audited access (`J-03`, `docs/04-ux/design-canon.md` §5.17). | No. | High. **The private-content reveal must be a server round trip, because the audit record is a domain write** (`ADR-011`). A client-side reveal toggle would be an audit hole, so PE is not merely feasible here — it is the correct design. | `LOCAL` |
| `UX-28` | Publication review queue | No (Auth) | Table, filters, reason-tagged decision (`J-03`). | No. Filters are querystring state. | High. | `DOC` |
| `UX-29` | Duplicate suspicion and ownership claims | No (Auth) | Table, comparison view, reason-tagged decision (`J-03`, `ADR-009`). | No. | High. | `DOC` |
| `UX-30` | `CategoryProposal` review | No (Auth) | Table, governance decision (`J-03`, `ADR-007`). | No. | High. | `DOC` |
| `UX-31` | Geocode / `undetermined` / eligibility exceptions | No (Auth) | Table; resolve or route a `GeoPoint` precision exception (`J-03`, `ADR-019`). | No. | High. | `DOC` |
| `UX-32` | Delivery-attempt failure queue | No (Auth) | Table; inspect `DeliveryAttempt` failures (`J-03`, `ADR-010`). | No. | High. | `DOC` |
| `UX-33` | Staleness and `RequestIntake` decay / reactivation | No (Auth) | Table; act on `Stale` and decayed `RequestIntake` (`J-03`). | No. | High. | `DOC` |
| `UX-34` | Audited private-content access | No (Auth) | Read the audit log of private-content accesses (`J-03`, `docs/04-ux/design-canon.md` §5.17). | No. | High. | `DOC` |
| `UX-35` | Notification message (email body, allowlist-bound) | Channel | Render an allowlist-bound body with a non-guessable link to the authenticated surface (`ADR-010`). | n/a. | n/a. **Not a web rendering surface. No client execution is available in an email body under any option**, which is an independent argument that the notification allowlist must be satisfiable by server rendering alone. | n/a |
| `UX-36` | Legacy profile claim | No (Auth) | Claim submission and evidence, disclosing nothing before a grant (`ADR-009`). | No. | High. `FUTURE` / conditional; classified for completeness, not scoped for V1. | `DOC` |
| `UX-37` | Sponsored placement surfaces | — | Not designed. `FUTURE` (`ADR-008`). | — | Not classified. A `FUTURE` surface must not be used to argue a V1 rendering model. | — |
| `UX-38` | Booking / payment / payout surfaces | — | Not designed. `FUTURE` (`WA-03`, `DB-02`). | — | Not classified, for the same reason. | — |
| `UX-39` | Report intake and block confirmation | No (Auth) | Compose a `Report` against a named target with a governed reason and an optional sensitive note, or confirm a `Block`; entered from `UX-04`/`UX-05` through `UX-16`, or directly from `UX-12`/`UX-20` (`J-01`, `J-02`, `J-04`, `docs/04-ux/design-canon.md` §5.17). | No. The submitted report is a domain write; nothing is held client-side. | High. A reason form and a `POST`, with the note field revealed by the selected reason. **Report intake must not require the reported content to still be visible**, so no client-held copy of the target is needed and the surface renders from the target's identity alone. | `LOCAL` |

## 4. The headline finding — **no V1 surface is `RICH`**

Evidence: `PROPOSED`. Provenance: `TECHNICAL_DISCOVERY`.

Thirty-six surfaces are in the V1 slice. Thirty-five are rendered web surfaces — twenty-four `DOC` and eleven `LOCAL` — and `UX-35` is an email body, classified `n/a`. **None is classed `RICH`.** That is a classification finding, not a preference, and it is the main input to `ADR-020` — because `RICH` is defined as the class that would justify Option B for a surface, and `ADR-020` Option B's own stated benefit is "client state surviving navigation, optimistic interaction, and multi-step composition".

**It is not, however, a finding that no surface holds client state surviving navigation.** `UX-07` does, for the bounded anonymous window, and §4.1 records it as an exception rather than resolving it away. The finding is that no surface holds such state **without a named domain owner, a named island, and a working no-script degradation** — which is the combination that would justify a second rendering model. That is a narrower claim than "zero `RICH` surfaces" sounds, and it is the claim P04 actually supports.

A finding this convenient deserves the strongest available attack. Four surfaces are the plausible counter-cases. Each is taken seriously below.

### 4.1 The strongest counter-case — the multi-step request composer (`UX-07`)

**The case for `RICH`.** `UX-07` is the one surface where state genuinely survives navigation. A customer answers five composer questions on the discovery path — up to eight on direct arrival — across a governed progressive flow (`docs/04-ux/design-canon.md` §5.1), may leave the composer to look at a provider profile or a market page, and must come back to a draft that still holds every answer. Question reveal is conditional on the selected `Category`'s archetype. A category change drops category-specific answers with a warning and must support an immediate undo (`docs/04-ux/design-canon.md` §5.2). That is state, it is structured, and it must survive navigation. On the usual reading, that is the textbook argument for a client application on this surface.

**Why the case does not carry Option B — stated without overclaiming.** The answer to `RQ-02`, in the same words used in §3 and in `docs/04-ux/surface-inventory.md`: **yes, `UX-07` contains navigation-persistent anonymous browser-local state**, and for the pre-`Account` window that state lives nowhere but the client. P04 does not deny it and does not classify it away. What P04 argues is narrower: **that requirement is satisfiable by the bounded `I-2` island plus local draft persistence, rather than by adopting a richer client rendering model for the surface** — which is why the surface is classed `LOCAL` rather than `RICH`. After an `Account` exists, the question stops being a rendering question at all. `RequestDraft` is **a domain aggregate P02 already designed for exactly this purpose**, owned by Demand under `ADR-003`, `Account`-scoped, resumable, with defined behaviour for category change, event-type change, reuse, and abandonment measurement. Once an `Account` exists, the draft is server-persisted and survival across navigation is a database read, not a client concern. It also survives things a client store cannot: closing the browser, switching devices, and clearing site data. **A client framework would be a second, weaker copy of a mechanism the domain already owns.**

The genuine gap is narrower than the counter-case claims, and it is one window: under `WA-05` the customer is anonymous until submit, so between the first answered question and `Account` creation there is no server-side owner for the draft. Canon §5.2 names the answer — the draft "is **local and anonymous** at that point (browser-local)", and is promoted to a server `RequestDraft` when an `Account` appears. Browser-local key-value storage of an anonymous draft is a handful of lines of script with a defined degradation. **It is not a client application, it is not a state container, and it does not require a component framework, a router, or a JSON contract.**

Three further observations close it:

1. `I-2` exists precisely for this window and is budgeted for it (§6). The composer is the reason there is an island budget at all.
2. The mandated degradation is real and complete: **one server-rendered question group per `POST`**, which is a working composer with no script whatsoever. It is slower per answer. It is not broken, and it is what a customer on a hostile network gets.
3. Canon §5.11 records that dynamic question reveal is the hardest accessibility problem in P04 — the new region must be announced politely, focus must move to the region heading rather than the first input, and answered questions must not reorder. **That problem is identical under every option.** A client framework does not solve it; it makes it easier to get wrong quietly, because a server-rendered navigation between steps has a natural, well-understood focus and announcement model that a client-side reveal has to reconstruct.

```text
first answer → browser-local draft (anonymous, WA-05)
  → [ navigate away: profile, market page, results ]
  → return → draft intact from browser-local state
  → submit → Account established → local draft promoted to server `RequestDraft`
  → PendingVerification (durable, invisible to provider)
  → verified → delivered → V1 CONTINUES
```

**Conclusion, stated so it cannot be read as stronger than it is.** `UX-07` contains navigation-persistent anonymous browser-local state. P04 proposes satisfying that requirement with the bounded `I-2` island and local draft persistence, promoted to a server-persisted `RequestDraft` as soon as an `Account` exists, rather than adopting a richer client rendering model for the surface. On that basis `UX-07` is classed `LOCAL` + `I-1`, `I-2` rather than `RICH`.

Four things follow, and a fifth does not:

1. **The existence of that browser-local state does not on its own imply a single-page application, a component framework, a client router, or a JSON contract** — and it does not on its own imply Option B. What it implies is one island with a defined degradation path.
2. **Option A remains the recommendation** (§6), and this exception is the reason the island budget exists rather than a reason to abandon the budget.
3. **`ADR-020` remains `PROPOSED — DAVID APPROVAL REQUIRED`.** Nothing here approves it, and the classification of `UX-07` is P04 evidence offered to that decision, not a decision itself.
4. **This is an implementation hypothesis, not a measurement.** `RQ-03` — what a progressive-enhancement treatment of this surface actually costs — has no evidence behind it and cannot acquire any until code exists (§1.1). `ADR-020`'s reopening trigger `B-03`, a failed progressive-enhancement attempt with its cost recorded, is the mechanism by which a real measurement overturns this classification.
5. What does **not** follow is that the state is absent. **P04 must not be cited as finding that `UX-07` holds no client state surviving navigation.** It holds some; the argument is about which rendering model that warrants, and it is decided by David, not by this document.

### 4.2 The conversation surface (`UX-12`, `UX-20`)

**The case for `RICH`.** Messaging surfaces are conventionally the most client-heavy part of any marketplace: a live thread, optimistic message insertion, typing indicators, presence, read receipts, unread badges that update without a refresh.

**Why the case fails, and it fails on architecture rather than on taste.** `system-architecture.md` §3 establishes **no realtime transport and no server-initiated push** — no WebSockets, no SSE, no presence, no typing indicators, no read receipts, no push. Every feature in the conventional argument is already excluded by a decision `ADR-020` explicitly lists as one of its constraints. What remains is a form post and a server-rendered thread.

Canon §5.16 is equally specific about what the thread is: one chronological rendering with the request and each `ProviderResponse` as anchored, visually distinct entries, author role always shown, `ThreadEvent` system markers visually distinct and never counted as messages. That is a rendering task, and server rendering is the natural place to do it because the anchoring, the role labels, and the marker distinction are all derived from domain state that the client would otherwise have to be taught to interpret.

Unread is computed server-side and rendered on navigation, with **no global polling**. The figure that costs the alternative originates in `docs/03-technology/technology-evaluation.md` §2: a 30-second unread poll across 200 concurrent authenticated sessions is roughly 6.7 requests per second, comparable to all public traffic at the Growth scenario. **The one architectural feature that would make a rich client worth its cost on this surface is the one feature the architecture does not have.** `LOCAL` covers what is actually left — preserving composer text if the user navigates within the page, and the send-button state.

### 4.3 The operator queues (`UX-26`–`UX-34`)

**The case for `RICH`.** Operator tooling is where teams reach for a data-grid: sorting, multi-select, bulk actions, inline editing, saved views.

**Why the case fails.** Canon §5.17 scopes operator surfaces as minimum viable and exception-only: **no generic admin suite, no dashboard inflation, no bulk actions.** Each queue records reason, state, priority where justified, actor or owner, and one auditable action. Stripped of the features that were deliberately excluded, a queue is a filtered table plus a form that writes a reason-tagged domain event. Filter state belongs in the querystring, where it is linkable and shareable between operators — which is a functional advantage of server rendering here, not a concession.

`UX-27` sharpens the point rather than weakening it. The private-content reveal has to be a server round trip because the audit record is a domain write and `Q-026`'s auditability limb is resolved as required (`docs/04-ux/design-canon.md` §5.17). **A client-side reveal that shows content already delivered to the browser produces an unaudited access**, which is the exact failure the queue exists to prevent. Server rendering is not the cheap option on this surface; it is the correct one.

Operator surfaces are also the one cohort permitted to be desktop-first (`docs/04-ux/design-canon.md` §5.12), which removes the responsive pressure sometimes used to argue for a component framework.

### 4.4 The provider response surface (`UX-19`) and the media manager (`UX-23`)

`UX-19` is a form with archetype-conditional fields, gated by `structuredFields`, `revisionEnabled`, `expiryEnabled` and `invalidateOnAmendment`. Selecting `clarification`, `decline` or `offer` chooses one of three field sets. Nothing needs to survive navigation: an unsent response is not a domain object in V1 and is not claimed to be one. The genuine risk on this surface is that **providers are mobile-first in practice** (`DB-06`), which makes it the highest-risk mobile surface in the product — but that is a layout and input-affordance problem at phone width, identical under all three options.

`UX-23` is the only surface with a capability gap that progressive enhancement cannot close on its own: per-file progress and non-blocking multi-file upload have no HTML-form equivalent, and `ADR-018`'s explicit processing state for pre-generated derivatives needs somewhere to be shown while it is pending. That is why `I-3` exists. It is one island on one authenticated surface. **A capability gap that is answered by one island is not evidence for a second rendering model across the product.**

## 5. Options A, B and C evaluated against the evidence

Evaluated against §3 and §4, not against preference. `ADR-020` Option D is out of scope: it was already rejected by `ADR-013` on deployment-unit grounds, and A, B and C all live inside one deployable.

| ID | Criterion | Option A — server-rendered plus progressive enhancement, named islands | Option B — richer client for specific authenticated surfaces | Option C — full client application for all surfaces |
|---|---|---|---|---|
| `RC-01` | Which journeys would require it | None. `J-01`, `J-02` and `J-03` are satisfied by §3: 24 `DOC`, 11 `LOCAL`, zero `RICH`. | None identified. Its stated benefit — state surviving navigation — is claimed only by `UX-07`, which genuinely has it in the anonymous window; §4.1 argues that requirement is met by `I-2` plus `RequestDraft` rather than by a second rendering model. **Argued, not measured.** | None. No journey requires client routing, and `J-01`'s public leg argues against it. |
| `RC-02` | State complexity | Low. State lives in the URL, in form posts, or in a domain object. The one anonymous window is a browser-local draft. | Medium-high on covered surfaces: a client store plus a versioned JSON contract, alongside the server state that still exists. | High. All state modelled twice — once in the domain, once in the client — including public discovery. |
| `RC-03` | Navigation persistence | Solved where it is needed by `RequestDraft` and `PendingVerification`, both of which P02 already designed. Survives device switch and cleared storage. | Solved by a client store, which does **not** survive device switch or cleared storage and therefore still needs the server object underneath. **A duplicate mechanism, not a replacement one.** | Same as B, applied everywhere, including where no persistence is needed. |
| `RC-04` | Validation location | Domain, once. The composer, the offering editor and the response form all render from the same governed definitions that validate them (`ADR-011`). | Domain, plus a client copy for interaction feedback. **Two places to be wrong**, and governed `CategoryAttributeDefinition` data shipped twice. | Same duplication as B, on every surface. |
| `RC-05` | Accessibility | The hard problem (dynamic question reveal, `docs/04-ux/design-canon.md` §5.11) is present but bounded to `I-2`. Server-rendered navigation between steps has a natural focus and announcement model. Every island must have a keyboard-operable, no-script path. | Same hard problem, more places to get it wrong silently. Client routing adds focus and announcement management the browser would otherwise do. | Every route transition becomes a manual focus and announcement responsibility, on public surfaces too. **The largest accessibility surface area of the three.** |
| `RC-06` | Implementation complexity — one part-time developer | Lowest for form-and-list surfaces, which is all 35 rendered V1 surfaces. One rendering model plus three named islands. | Adds a component toolchain, a JSON contract per covered surface, and the client/server version-skew bug class — while the server-rendered public surfaces remain, so **the codebase carries both**. | Adds all of B, plus re-implementing `D-09`'s locale-distinct URLs and retained redirect history in client routing. |
| `RC-07` | SEO and acquisition | Public discovery renders without executing script by construction. `UX-01`–`UX-06` and `UX-16` are directly served. | Neutral if public surfaces stay server-rendered — which `ADR-020` rule 4 requires anyway. That requirement is what forces B to carry two models. | **Worst.** The surface carrying the acquisition hypothesis becomes script-dependent, and `D-09`'s URL and redirect requirements move into client routing. |
| `RC-08` | Reversal cost | LOW–MEDIUM and **per-surface**. A surface can change rendering model without touching marketplace logic, because authorization and the write path stay in the domain behind one application layer. | MEDIUM per covered surface, and asymmetric: adding a JSON contract later is additive, removing one later is not. | MEDIUM–HIGH and global. `D-09` handling would have to be moved back to the server. |

## 6. Recommendation

**Option A — server-rendered views plus progressive enhancement, with a small named set of client islands.** Evidence: `PROPOSED`. Provenance: `TECHNICAL_DISCOVERY`. `DAVID WORKING ASSUMPTION — OWNER VALIDATION PENDING` where it rests on `WA-04` (web-first, responsive) and `WA-05` (guest-then-verify), both of which shape `UX-07`'s anonymous window.

The argument in one line: **`ADR-020` says Option B is justified by a surface whose client state must survive navigation; P04 looked at all thirty-five rendered V1 surfaces and found exactly one — `UX-07`, in its anonymous pre-`Account` window — and argues that this single case is met by one bounded island plus a domain object P02 already designed for it, rather than by a second rendering model.** That last step is a judgement, not a measurement, and `RQ-03` is what would test it.

### 6.1 Why Option B is not chosen now, and exactly what would earn it later

Option B is not rejected. `ADR-020`'s Decision rule 6 records that "Option B remains available per surface" and that adopting it "requires no reversal of this record"; this document declines to reach it **on this evidence**, not in principle.

Two things make declining safe. First, `RC-08`: reversal is per-surface and LOW–MEDIUM, so choosing A does not foreclose B for any named surface later. Second, the asymmetry `ADR-020` names — adding a JSON API and a client later is additive as long as `ADR-011` holds; removing one is not. That asymmetry is a reason to **start** at A. `ADR-020` is explicit that it is not, on its own, a reason to **approve** A, and this document does not rely on it: the evidence in §3 and §4 carries the recommendation, and the asymmetry only makes the recommendation cheap to be wrong about.

Option B becomes the right answer for a surface when three things are true together — this is `ADR-020`'s own reopening trigger, restated as an acceptance test:

| ID | Requirement to adopt Option B for a surface | Why all three are required |
|---|---|---|
| `B-01` | A **named V1 surface** — a `UX-nn` ID, not a category of surface. | `ADR-020` decides per surface. A global argument for B is out of scope by construction. |
| `B-02` | A **specific journey** in `docs/04-ux/` evidencing client state that must survive navigation, which is not already held by a domain object. | Without the second clause, `UX-07` would qualify, and §4.1 shows it should not. |
| `B-03` | A **failed progressive-enhancement attempt**, with its cost recorded. | This is the item §1.1 says P04 cannot supply today. It converts an argued estimate into a measurement, and it is the only honest way to overturn §3. |

Interaction latency is the second independent route: `ADR-020` names measured p75 interaction latency on request submission above the approved budget, attributable to full-page round trips. That route needs §10's instrumentation and a shipped surface, and it is not available now.

### 6.2 Why Option C has the least support

`ADR-020`'s Option C section puts it precisely: "P04 strengthens rather than weakens the case against C", because moving the public surfaces into client routing "trades the one thing the product cannot afford to lose for a benefit no surface asked for". P04's own evidence does not improve C's position — it worsens it. `RC-07` is the decisive row: the public discovery surface carries the acquisition hypothesis, `ADR-020` rule 4 requires it to remain server-readable, and Option C makes it script-dependent. `RC-05` and `RC-06` compound that with the largest accessibility surface area and a re-implementation of `D-09`'s URL and redirect requirements in client routing, for one part-time developer. **P04 found no journey that asks for any of it.**

## 7. The island budget

Three named islands, against `ADR-020`'s reconsideration trigger of more than five. Every island is a named, justified exception to server rendering — not a general licence.

| ID | Island | Surfaces | Why progressive enhancement alone is insufficient | Required degradation path |
|---|---|---|---|---|
| `I-1` | Governed type-ahead picker (`Category` + `Place`) | `UX-01`, `UX-04`, `UX-07`, `UX-17`, `UX-22` | Per-keystroke lookup against governed labels, synonyms and folded forms with ARIA combobox semantics and a managed active descendant. A full page round trip per keystroke is both a latency problem and a read-path budget problem, and `ADR-019` rule 2 means the lookup is against the internal governed `Place` list with **zero vendor calls**. | A plain governed `<select>` (or a browse path into `UX-02`) plus submit. Keyboard-only operation is required of the island itself (`docs/04-ux/design-canon.md` §5.11), not only of the fallback. |
| `I-2` | Composer step controller and local draft persistence | `UX-07` | Anonymous pre-`Account` draft survival under `WA-05`, and archetype-conditional question reveal without a round trip per answer. Neither has an HTML-form equivalent while the customer has no `Account`. | **One server-rendered question group per `POST`, with no JavaScript.** Reveal happens between steps rather than within one. The draft is lost only in the anonymous window, and only if the browser store is unavailable — never after promotion to a server `RequestDraft`. |
| `I-3` | Media upload manager | `UX-17`, `UX-23` | Multi-file selection, per-file progress, partial success, and the visible pre-generated-derivative **processing state** required by `ADR-018` have no HTML-form equivalent. | A multi-file `<input type="file">` `POST`, with processing state read from the database on the next render. Slower and opaque during upload; complete and correct. |

Three rules bind all three islands and are not negotiable:

1. **Every island must degrade to a working server-rendered path.** An island whose fallback does not complete the journey is not an island; it is Option B adopted without approval.
2. **Every island's data access goes through the same application layer and the same single machine-access enforcement point** (`ADR-012`). An island does not get a private route, a bypass, or a convenience projection.
3. **No JSON API is built "just in case"** (`R-012`: no deliverable whose only consumer is a possible future decision). An endpoint exists because a named island consumes it today, or it does not exist.

**Headroom and ceiling.** `ADR-020`'s reconsideration trigger is a client-island count exceeding five, "which indicates the baseline has been outgrown by accumulation rather than by decision". P04 proposes three. That leaves two islands of visible headroom and a visible ceiling — which is the point: accumulation is allowed to happen, but it is countable, and the count is produced by the build (§10) rather than asserted in a document. Reaching six is not a failure to be argued away; it is `ADR-020` reopening on its own terms.

## 8. Constraints that bind every option and are not traded away

These are not tie-breakers. They hold identically under A, B and C, and an option that weakens one is not cheaper — it is disqualified.

| ID | Constraint | Source | What it forbids |
|---|---|---|---|
| `RB-01` | Authorization is decided **in the domain**, never in a route guard, a template, or a client. | `ADR-011`, `R-026` | The one genuinely expensive mistake available in this decision. It is not a rendering trade-off. |
| `RB-02` | Exactly **one machine-access enforcement point**, deny by default; the public projection is an allowlist applied inside the read. | `ADR-012`, sharpened by `ADR-013` | A second door. A richer client must consume the same application layer, not a parallel path. |
| `RB-03` | **Public discovery pages must remain server-readable** under any option, because the acquisition hypothesis depends on it. A product constraint, not a rendering preference. | `ADR-020` rule 4 | Script-dependent rendering of `UX-01`–`UX-06` and `UX-16` — the surfaces rule 4 itself names — and, **as a P04 extension rather than as the ADR's own scope** (§3), of `UX-07` and `UX-08`. |
| `RB-04` | Contact data resolves through the recorded disclosure decision at delivery or render time and never reaches a client that has not passed it; precise provider base location is never emitted to any projection. | `ADR-010`, `ADR-019` Level 3 invariant, `docs/02-architecture/security-privacy-architecture.md` §12 | Shipping a "convenient" object to a client and filtering it there. |

**The named trap, restated so it is not rediscovered later: a JSON endpoint built for an island is a machine-access surface.** It is not an internal detail, it is not exempt because an island is small, and it is not exempt because the data looks public. It sits behind the same single enforcement point as every other machine access, or `ADR-012`'s deny-by-default guarantee acquires a second door. This is the specific way Option A can fail while appearing to succeed: three well-behaved islands, and one endpoint added quietly to serve the third.

## 9. Cost framing

**Rendering moves no infrastructure line.** A, B and C all render from the same one deployable, on the same instance tier, against the same database and the same delivery layer. Fixed and variable cost in `docs/03-technology/cost-model.md` is identical across the three.

**Cost is therefore explicitly not the deciding factor here, and must not be presented as one.** `ADR-020` states the consequence directly: a rendering decision taken to save infrastructure money would be optimising the smallest quantity in the model. This document's recommendation rests on §3, §4 and §5 — journeys, state, accessibility, the acquisition constraint, and one part-time developer.

What rendering does move is two things:

1. **Developer-days.** Direction only, and informed ranges rather than measurements: A is cheapest for form-and-list surfaces, which is all thirty-five rendered V1 surfaces; B adds a component toolchain plus a versioned contract per covered surface; C additionally re-implements `D-09`'s URL and redirect requirements in client routing.
2. **Abandonment**, which `R-022` already records as a measured cost. This is the real consequence a rendering choice has, and it is measurable rather than arguable — which is why §10 exists.

**No dollar figure is stated, because none was verified and none would be honest.**

## 10. Required instrumentation once any surface ships

Taken from `ADR-020`'s Validation section. None of it is available today, and its absence is why `RQ-03` is argued rather than measured.

| ID | Instrument | Why `ADR-020` requires it |
|---|---|---|
| `IN-01` | **Per-flow interaction latency at p75 and p95, tagged with the surface name.** | Feeds the reopening trigger directly: p75 on request submission above the approved budget, attributable to full-page round trips, is one of the two routes to Option B. Untagged aggregate latency cannot support a per-surface decision. |
| `IN-02` | **A counted island inventory produced by the build.** | Makes the five-island ceiling enforceable rather than asserted. A count that has to be gathered by hand is a count nobody gathers. |
| `IN-03` | **Request-submission abandonment segmented by surface**, with composer abandonment separated from verification abandonment (`docs/04-ux/design-canon.md` §5.10). | `R-022` makes abandonment the measurable consequence a rendering choice actually has. Segmentation is load-bearing: the fixes for composer abandonment and verification abandonment are different, and an unsegmented rate would let either be blamed on rendering. |

Acceptance checks, also from `ADR-020`, restated as things that must be true of the shipped system: no authorization decision exists in a template, a route guard, or a client; every JSON endpoint added for a client passes through the single machine-access enforcement point; and every public discovery URL renders its governed content without executing script.

## 11. Constraint

**This recommendation is `PROPOSED` and requires David's approval.** `ADR-020`'s decision owner is David as technical decision owner, with P04 owning the interaction requirements that decide it. This document supplies those requirements and a recommendation. It does not close `ADR-020`, and it must not be cited as having closed it.

Three items in this document are weaker than they look, and are named so they are not read as stronger:

1. **`RQ-03` is argued, not measured**, on every one of the thirty-six rows in §3 that carries a rendering class and therefore a progressive-enhancement judgement. Nothing has been implemented.
2. **`G-06` is UNSATISFIED**, and `UX-07`'s question set — the surface that carries the entire `RICH` counter-case — depends on `WA-01`. If `G-06` resolves in a way that requires date-availability interaction on a provider surface, `UX-07`, `UX-19` and `UX-22` are re-examined before this recommendation is relied on.
3. **No usability evidence exists** (`SRC-006` NOT RECEIVED). The composer-length target for a mobile performer request is a design target, not a measured result, and the abandonment consequence in §9 is a prediction until `IN-03` reports.

What reopens this document, before `ADR-020` is approved: any journey document in `docs/04-ux/` establishing a V1 surface with client state that must survive navigation and is not held by a domain object; a fourth island proposal, which consumes headroom against the five-island ceiling; or a `G-06` resolution that changes `UX-07`.

What reopens it after `ADR-020` is approved: `ADR-020`'s own standing triggers — `B-01` plus `B-02` plus `B-03` together for a named surface; measured p75 interaction latency on request submission above the approved budget attributable to full-page round trips (`IN-01`); or a counted island inventory exceeding five (`IN-02`).

`ADR-019` Level 3 interacts with this recommendation and is decided in `docs/04-ux/map-decision.md`: a rendered map would be the only plausible `RICH` surface in the product, and approving one would weaken Option A on the one surface where the evidence in §4 does not reach.

---

*Record dates — phase executed 2026-08-12. Nothing has been implemented, so every interaction-cost judgement in this document is an argued estimate rather than a measurement.*
