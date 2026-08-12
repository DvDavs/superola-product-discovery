# Customer Journey — P04 UX Architecture

> Status: `PROPOSED — OWNER VALIDATION REQUIRED`. Provenance: `TECHNICAL_DISCOVERY`. This is a P04 UX architecture proposal, not an approved design and not a visual specification.
> Working assumptions: `WA-01`–`WA-05` (see `docs/04-ux/README.md`). `G-06` is UNSATISFIED.

This document is the canonical P04 customer journey. It is **not** a screen design, a wireframe set, a flow diagram for production, a copy deck, or a component specification. It contains no visual design, no colors, no typography, and no component names. It does not resolve any owner gate, and it does not decide anything listed in the P04 non-decision set (§7). It describes what the customer is trying to do at each point, which surface carries it, which `ServiceRequest` lifecycle state the platform is in, what is recorded, and what happens when the journey does not go the way the happy path assumes.

The journey is stated against the P02 domain model. Where this document names a state, that state already exists in `docs/02-architecture/domain-model.md` §5.2. P04 invents no lifecycle state and renames none.

---

## 1. The primary journey

`ServiceRequest` is single-service and single-recipient (`WA-02`). One journey produces one request addressed to exactly one `ServiceOffering`. Everything in this document that looks like breadth — a second provider, a second service, a second event — is the customer deliberately running this journey again.

### 1.1 Surface chain

```text
Need
  → Intake · DISCOVERY tier (governed `Category` + governed `Place`)   `UX-01` `UX-02` `UX-03`
  → Results                                                            `UX-04`
  → Provider profile                                                   `UX-05`
  → Intake · PRE-SUBMIT, CATEGORY, QUALITY tiers                       `UX-07`
  → RFQ review and send                                                `UX-08`
  → Identity and channel verification                                  `UX-09`
  → Request submitted confirmation                                     `UX-10`
  → Provider response                                                  `UX-11` `UX-12`
  → Conversation                                                       `UX-12`
  → Customer-reported outcome                                          `UX-14`
  → V1 ENDS
```

**Intake is split, and the split is deliberate.** The composer is progressive and category-governed (`§5.1` of the P04 design canon (`docs/04-ux/design-canon.md`)): only the `DISCOVERY`-class answers — governed `Category` or archetype, and governed `Place` at a stated granularity — are needed to produce results at all. They are answered before results exist. The `PRE-SUBMIT`, `CATEGORY`, and `QUALITY` answers are asked after the customer has chosen a recipient, because until then the platform does not know which archetype's questions apply and the customer does not know whether any supply exists. This is the structural difference from a fixed sequential intake, which asks everything before showing anything. Design target: a mobile performer request completes in **5 composer answers on the discovery path** — up to 8 when the customer arrives directly at a provider profile and the `DISCOVERY` answers have not been given — and **10–11 total blocking items to submit** once identity is counted. **The earlier "6–9 answers" figure was net of identity and is withdrawn.** See `docs/04-ux/request-intake.md` §3. This is a design target, not a measured improvement — there is no usability evidence (`SRC-006` NOT RECEIVED). The measurement that would confirm it is per-stage composer completion instrumented under `R-022`.

### 1.2 Lifecycle chain

```text
no lifecycle state — local `RequestDraft`, browser-local and anonymous
  → `PendingVerification`   durable, invisible to the provider
  → `Delivered`             visible in the provider inbox
  → `InDiscussion`          provider responded `clarification` or `offer`
  → `Closed`                customer recorded a `ReportedOutcome`
  → V1 ENDS
```

### 1.3 Every lifecycle state, mapped

| ID | State | Reached by | Customer surface | Terminal | Note |
|---|---|---|---|---|---|
| `CS-01` | *(none — local `RequestDraft`)* | first answered composer question | `UX-07` `UX-13` | no | Browser-local and anonymous under `WA-05`; promoted to a server `RequestDraft` when an `Account` exists. **Never visible to any provider.** |
| `CS-02` | `PendingVerification` | submit from draft with one chosen offering | `UX-09` `UX-10` `UX-11` | no | Durable but invisible to the provider. This state exists so the customer's work is not lost on verification abandonment. |
| `CS-03` | `Abandoned` | `verificationWindowElapsed` OR `customerAbandoned` | `UX-11` `UX-13` | yes | The measurable friction cost of `WA-05`. The provider never learns the request existed. Window length is `OPEN`. |
| `CS-04` | `VerificationFailed` | `verificationRejected` | `UX-09` `UX-11` | yes | Contact-channel control was not proven. Not a judgement about the customer. |
| `CS-05` | `RecipientIneligible` | `recipientNoLongerEligible` | `UX-11` | yes | The recipient `ServiceOffering` stopped satisfying `EligibilityDecision` between submit and delivery. |
| `CS-06` | `Delivered` | `contactControlVerified` AND `recipientStillEligible` | `UX-10` `UX-11` `UX-12` | no | Means visible in the provider's in-platform inbox. Notification success or failure **never changes request state**. |
| `CS-07` | `InDiscussion` | `ProviderResponse` kind `clarification` or `offer` | `UX-11` `UX-12` | no | An offer is not a booking. There is no accept transition that creates an obligation. |
| `CS-08` | `Declined` | `ProviderResponse` kind `decline` | `UX-11` `UX-12` | no | Decline reason is optional, provider-authored, immutable, and **never punitive**. |
| `CS-09` | `NoResponse` | `noResponseWindowElapsed` (system) | `UX-11` `UX-12` | no | **A system-observed state, not a provider action.** Non-punitive, non-terminal; a late response still moves to `InDiscussion`. Window is `POLICY PENDING` (`Q-032`). |
| `CS-10` | `Withdrawn` | `withdraw` (customer) from `Delivered`, `InDiscussion`, or `NoResponse` | `UX-11` `UX-12` | yes | The customer's own act. Does not close, penalise, or notify beyond the allowlist. |
| `CS-11` | `Lapsed` | `eventDateDefinite` AND `eventDatePassed` | `UX-11` `UX-14` | no | A fact about the world, not a platform deadline. **A flexible-date request never lapses.** |
| `CS-12` | `Quarantined` | `abuseConfirmed` (operator) | `UX-11` | yes | The customer sees that posting is disabled and a policy-level reason only. |
| `CS-13` | `Closed` | `close` with `ReportedOutcome` from `InDiscussion` or `Lapsed`; `acknowledge` from `Declined` | `UX-14` `UX-11` | yes | `ReportedOutcome` is `self_declared` and customer-only. It is **not** a booking, a conversion, a payment, or a completion. |

**`unknown` is not `Unreported`.** `unknown` is an explicit customer answer recorded on a `ReportedOutcome`; `Unreported` is silence. No surface, prompt, or measurement may collapse them.

---

## 2. Stage by stage

| ID | Stage | Customer goal | Surface(s) | What the customer sees | What is recorded | Exit conditions | Alternate and exception paths |
|---|---|---|---|---|---|---|---|
| `CJ-01` | Need | Turn an intention into something the platform can act on. | `UX-01` `UX-02` `UX-06` | Governed `Category` and archetype entry, governed `Place` entry, and browsable categories. The type-ahead (`I-1`) resolves against governed labels and synonyms. **`V1 has no availability model`** is stated here in substance, per `ADR-005`. | Nothing durable. No `Account`, no draft, no server record beyond the anonymous read. | A governed `Category` or archetype reference **and** a governed `Place` reference both resolve. | Input does not resolve to a governed row → **rejected and shown**, never approximated (see `discovery-and-results.md`). Unsupported category or market → say so plainly and capture the demand signal. |
| `CJ-02` | Intake · `DISCOVERY` tier | Answer only what is needed to see whether supply exists. | `UX-07` (`I-1`, `I-2`) | Two answers: service need and location. A named-stage indicator, never a false "step N of M" — the count is data-dependent. | **`draft created`** is a named Demand-family measurable transition, fired at the first answered question. The draft is browser-local and anonymous. | Both `DISCOVERY` answers present. | Abandonment here is composer abandonment and is instrumented **separately** from verification abandonment, because the fixes differ. |
| `CJ-03` | Results | Compare credible candidates and understand the market honestly. | `UX-04` `UX-03` | Ranked results, each slot carrying `placementBasis` = `organic`, a disclosure marker, and a ranking explanation. `RequestIntake` state is surfaced, not silently excluded. `Stale` providers remain discoverable with a freshness indicator. `undetermined` candidates are shown **with their uncertainty**. | Impression per slot with `placementBasis` **and position**. Constraint shape, result count, `undetermined` count, and `Market`. | The customer opens a `ProviderProfile`. | Zero results (`CJX-01`); too broad, too narrow, insufficient location granularity — all covered in `discovery-and-results.md`. **A query is never converted into a broadcast.** |
| `CJ-04` | Provider profile | Judge fit: service, area, media, trust facts, freshness, and whether a request is even accepted. | `UX-05` | Identity, `ServiceOffering`s, coarse `Place` label, the `ServiceAreaDeclaration` **as a provider claim in words**, media, `verificationBasis` on every trust-flavoured fact, publication freshness, `RequestIntake` state, and the `V1 has no availability model` non-claim in substance. Customers see only `Published` and `Stale`. | Profile view; the entry point from which a request was composed. | The customer starts or resumes a request for one `ServiceOffering`. | `RequestIntake` = `paused` (`CJX-02`). `RequestIntake` = `unconfirmed` → shown honestly; it does **not** demote the provider in ranking. **Customers must not be able to distinguish `Suspended` from `Deactivated`** — both are simply absent. |
| `CJ-05` | Intake · remaining tiers | Give the provider enough context to answer well, and nothing more. | `UX-07` (`I-1`, `I-2`) | Only the questions this `CategoryArchetype` requires, revealed progressively. `PRE-SUBMIT`: event type, event date or window or `flexible`. `CATEGORY`: archetype attributes from governed `CategoryAttributeDefinition`s. `QUALITY`: budget as an optional band, planning intent, free-text notes. Every revealed region is announced politely and receives deliberate focus. | Per-stage progression and abandonment. Draft content stays local until an `Account` exists. | The customer reaches review. | Category change → universal `EventContext` answers retained, category-specific answers dropped **with an explicit before-the-fact warning naming what will be lost**, retained in session for immediate undo. Event type change → nothing dropped. `flexible` date is a first-class valid answer, never a lesser one. |
| `CJ-06` | RFQ review and send | Confirm exactly what is being sent, to exactly whom. | `UX-08` | The full request as the provider will see it, the single named recipient, and consent. Free text is labelled as **possibly containing contact data regardless of policy** (`ADR-010`). | Consent reference and recipient provenance at submit. | Submit. | Editing returns to `UX-07` with answers intact. **No recipient set, no fan-out, no "send to similar providers".** |
| `CJ-07` | Identity and channel verification | Prove a reachable contact channel without having lost the work. | `UX-09` | A contact channel is collected here, not in the composer. The customer is told the request is already saved. Only `verificationBasis` = control of this contact channel was proven at time T is ever claimed. **No government identity verification exists in V1.** | Request is durably stored as `PendingVerification`. `VerificationFact`. Verification-step abandonment is instrumented separately (`R-022`). | Contact control proven → `Delivered`, if the recipient is still eligible. | `CJX-06` verification abandonment or failure. `CJX-07` recipient became ineligible. |
| `CJ-08` | Request submitted | Know what happens next, and what does not. | `UX-10` | Confirmation that the request reached the provider's inbox, what a response may be (`clarification`, `decline`, `offer`), and that **not responding is a fourth outcome that is not a provider action**. No promised response time — the window is `POLICY PENDING`. | `Delivered`. `NotificationIntent` and its `DeliveryAttempt` are separate records; **notification failure never changes request state**. | The customer navigates to `UX-11`. | Repeat submission must not create a second request. Notification failure is visible **to its own party** and is never presented as the counterparty's non-response. |
| `CJ-09` | Provider response | Learn whether the provider can engage. | `UX-11` `UX-12` | The three response kinds are **user-visibly distinct**: `clarification` (a question, no price, thread continues), `decline` (optional provider-authored reason), `offer` (optional `PriceStatement` with explicit currency, basis, inclusions, exclusions, conditions, and as-of). `Superseded` responses stay visible to both parties. | `ProviderResponse` kind and sequence; response latency as an internal `ResponsivenessObservation`, **internal only, never published as a label in V1**. | Any response, or `noResponseWindowElapsed`. | `CJX-03` decline. `CJX-04` `NoResponse`. Where revision is not archetype-enabled, "current offer" is simply the most recent by recency, with no acknowledgement machinery. |
| `CJ-10` | Conversation | Clarify fit and terms in context. | `UX-12` | One chronological thread: the request and each `ProviderResponse` as anchored, visually distinct entries, plus `Message`s. Author role is always shown. `ThreadEvent` system markers are visually distinct and **never counted as messages**. No presence, typing indicators, read receipts, or realtime affordances — there is no realtime transport by architecture. | `Message`s with `contentLocale`. Unread is computed server-side and rendered on navigation; **no global polling**. | The customer decides, withdraws, or the request lapses. | `CJX-12` abuse, report, block. Contact-disclosure policy is unresolved (`Q-008`, `A-010`, `OR-011`); the disclosure seam means the UX is identical under all three owner answers. |
| `CJ-11` | Customer-reported outcome | State what actually happened, honestly. | `UX-14` | `proceeding` / `not_proceeding` / `unknown`, prompted on a governed trigger. **No money-shaped language, no accept button, no booking claim, no payment implication.** An optional secondary reason may accompany the governed value. | `ReportedOutcome` with value, reported-by, reported-at, basis `self_declared`. Append-only. Silence is `Unreported` and is recorded as such. | `Closed`. | The customer may decline to answer — that is `Unreported`, not `unknown`. The richer secondary reason list (chose another provider · provider did not respond · price too high · plans changed · still deciding) is a **`PROPOSED` P04 extension to `ADR-003`**, modelled alongside the governed triple and never as a replacement enum. |
| `CJ-12` | V1 ENDS | — | — | The journey terminates at a marketplace outcome. | — | — | Booking, deposit, payment, payout, refund, and transaction-derived review are `FUTURE` (`WA-03`, `DB-02`). See §6. |

---

## 3. Exception journeys

Every exception below is a real journey with its own surfaces, not an error message. Each ends somewhere the customer can act.

### 3.1 `CJX-01` — Zero results

```text
Need
  → Intake · DISCOVERY tier                      `UX-07`
  → Results: zero eligible supply                `UX-04`
  → Honest coverage explanation
     (no supply in this CATEGORY  vs  no supply in this AREA — recorded separately)
  → Correct the constraint  OR  progressive relaxation with the relaxed constraint NAMED
  → results  OR  optional no-promise demand capture
  → V1 ENDS for this attempt
```

The draft survives; the customer may change `Category` or `Place` and continue. The relaxed constraint is always named — the customer must see which constraint was relaxed. Unmet demand is captured with **no notification promise**: durable `DemandWatch`, saved searches, and search alerts are `Q-031`, unratified, and excluded from V1 scope. **A query is never converted into a broadcast.** Full treatment in `docs/04-ux/discovery-and-results.md`.

### 3.2 `CJX-02` — Provider is not accepting requests

```text
Results  `UX-04`
  → Provider profile with `RequestIntake` = `paused`   `UX-05`
  → "Not accepting requests" stated plainly, with the provider's optional horizon and optional provider-authored reason
  → request action unavailable for that offering
  → back to results, draft intact                       `UX-04` `UX-13`
  → V1 ENDS for this recipient
```

**"Not accepting requests" is not date unavailability and must never be worded as either.** `RequestIntake` is an intake state (`ADR-005`). A `paused` offering is surfaced, not silently excluded from results — the customer is told rather than shown a shorter list for an unexplained reason. `unconfirmed` is different again: the provider has not recently reconfirmed, the state is shown honestly, and it **may not demote the provider in ranking in V1**.

### 3.3 `CJX-03` — Provider declines

```text
Delivered  `UX-11`
  → `ProviderResponse` kind `decline`   `UX-12`
  → `Declined`
  → decline reason shown IF the provider authored one; otherwise the absence is shown as absence
  → acknowledge
  → `Closed`   `UX-14`
  → back to discovery with the reusable `RequestDraft`   `UX-13` `UX-04`
  → V1 ENDS for this recipient
```

Two sub-paths, and the difference is user-visible:

| ID | Sub-path | What the customer sees | What must never happen |
|---|---|---|---|
| `CJX-03a` | Decline **with** a provider-authored reason | The reason as the provider wrote it, attributed to the provider, immutable. | The reason must never be operator-derived, never rewritten, and **never punitive** toward the provider. |
| `CJX-03b` | Decline **without** a reason | That the provider declined and gave no reason. Absence is shown as absence. | **No inferred reason, no "likely unavailable", no substituted explanation.** The platform does not know why. |

The recorded decline reason is the instrument that converts *"do we need an availability model?"* from a debate into a Phase-1 measurement. That is why it is optional to give and mandatory to record when given. Returning to discovery uses the reusable private draft; any new request requires **deliberate provider selection and fresh confirmation** (`ADR-003`). It is never an automatic reroute.

### 3.4 `CJX-04` — Provider does not respond

```text
Delivered  `UX-11`
  → noResponseWindowElapsed (SYSTEM, not a provider action)
  → `NoResponse`   `UX-11` `UX-12`
  → state shown neutrally, with a manual path back to discovery
  → EITHER lateProviderResponse → `InDiscussion`
     OR    withdraw (customer) → `Withdrawn`
     OR    eventDateDefinite AND eventDatePassed → `Lapsed`
  → V1 ENDS or continues
```

**`NoResponse` is system-observed, non-punitive, and non-terminal.** Its window length is `POLICY PENDING` and is a named P04 non-decision (`Q-032`). Consequences for the UX, all mandatory:

- Never present silence as a judgement about the provider, and never as a judgement about the request.
- Never publish a response-rate or response-time badge. `ResponsivenessObservation` is computed and retained but **internal only in V1**.
- Never promise a response time at any earlier stage, because no approved window exists to promise.
- A late response is a normal transition, not an exception — the surface must accommodate a response arriving after `NoResponse` without implying the earlier state was wrong.
- Distinguish `NoResponse` from **notification delivery failure**, which is visible to its own party and is never presented as the counterparty's non-response.

### 3.5 `CJX-05` — Customer abandons intake

```text
first answered composer question → local `RequestDraft` (browser-local, anonymous)   `UX-07`
  → customer leaves
  → same-device anonymous resume from local state          `UX-07`
  → OR sign in voluntarily → local draft promoted to a server `RequestDraft` → cross-device resume   `UX-13`
  → OR never returns → no lifecycle state ever existed; nothing was delivered; no provider learns anything
```

Abandonment **after** submit is a different journey and a different state:

```text
`PendingVerification`  `UX-09`
  → verificationWindowElapsed OR customerAbandoned
  → `Abandoned`   `UX-11`
  → the request is retained for the customer and the provider never sees it
```

`Abandoned` is the measurable friction cost of `WA-05` and must be instrumented as such (`R-022`). It is **not** a customer failure and no surface may frame it as one. The verification-window length is `OPEN` — no approved value exists. Composer abandonment and verification abandonment are measured separately because the fixes are different: the first is a question-design problem, the second is a channel-and-trust problem.

### 3.6 `CJX-06` — Verification abandonment or failure

```text
`PendingVerification`   `UX-09`
  → EITHER verificationRejected → `VerificationFailed`   `UX-11`
     OR    customerAbandoned / verificationWindowElapsed → `Abandoned`   `UX-11`
  → the request content is retained; the provider was never shown it
  → retry with the same or a different contact channel   `UX-09`
  → V1 ENDS or re-enters `PendingVerification`
```

`DB-12` is fixed: a request must have a verified reachable customer contact channel before delivery. The only claim V1 ever makes is `verificationBasis` = control of this contact channel was proven at time T. The surface must say that and nothing stronger — no identity claim, no trust score, no badge. Failure messaging must not disclose whether a contact channel is already associated with another `Account`; that is an enumeration oracle, by the same reasoning `ADR-009` applies to claim disclosure.

### 3.7 `CJX-07` — Recipient becomes ineligible between submit and delivery

```text
`PendingVerification`   `UX-09`
  → recipientNoLongerEligible (SYSTEM)
  → `RecipientIneligible`   `UX-11`
  → the customer is told the request could not be delivered and why in policy terms
  → the `RequestDraft` remains reusable
  → back to results to select a different provider deliberately   `UX-04` `UX-13`
  → V1 ENDS for this recipient
```

The cause is an `EligibilityDecision` input that changed: the offering left `Published`/`Stale`, the category binding changed, `LocationEligibility` moved to `ineligible`, `RequestIntake` moved to `paused`, or a blocking trust state appeared. **The customer must not be told which.** Doing so leaks `Suspended` as a state, which customers must never be able to distinguish from `Deactivated`, and it leaks moderation status. The honest statement is that the request could not be delivered and the customer may select another provider. **There is no automatic reroute.**

### 3.8 `CJX-08` — Customer wants a different provider for the same need

```text
`Declined` / `NoResponse` / `Withdrawn` / any state   `UX-11`
  → open the reusable private `RequestDraft`   `UX-13`
  → back to results for the same `Category` + `Place`   `UX-04`
  → select a DIFFERENT provider deliberately   `UX-05`
  → fresh confirmation for this recipient   `UX-08`
  → a SECOND, INDEPENDENT `ServiceRequest` with its own lifecycle   `UX-11`
  → V1 ENDS per request
```

This is the single most important thing V1 does **not** do automatically. There is **no recipient set, no routing, no fan-out, no shared response window, no auto-closure of siblings, and no reroute.** The `ServiceRequest` invariant "exactly one recipient" is not implemented as a collection of size one, and the UX must not imply it is. Each request has its own state, its own conversation, and its own outcome. The customer may hold several open simultaneously; simultaneous-open and closure behaviour require owner and user validation and are not decided here. Consent-based "send to similar providers" is a preserved `FUTURE` branch (`DB-01`, `WA-02`) and must not be prototyped into V1 copy.

### 3.9 `CJX-09` — Customer wants another service for the same event

```text
any request, open or closed   `UX-11` `UX-12`
  → "Request another service for this event"
  → NEW `RequestDraft` pre-filled from the previous request's `EventContext`,
    carrying the customer-declared `eventGroupingHint`   `UX-07`
  → pick a new `Category`   `UX-07`
  → answer ONLY that archetype's `CATEGORY` questions
  → select a new provider deliberately   `UX-04` `UX-05`
  → a separate single-recipient `ServiceRequest`   `UX-11`
  → V1 ENDS per request
```

`EventType != ServiceCategory`. One event may need many independent services; **the V1 request is single-service.** This path is the same P02 aggregate used twice — no new entity, **no multi-service RFQ, no fan-out, no automatic routing.** The customer-facing grouping of requests sharing an `eventGroupingHint` is a **light presentational grouping only**; it is not an `Event` aggregate and no surface may draw one. Universal `EventContext` answers carry across; category-specific answers do not, because they belong to the previous archetype.

### 3.10 `CJX-10` — Customer withdraws

```text
`Delivered` / `InDiscussion` / `NoResponse`   `UX-11` `UX-12`
  → withdraw (CUSTOMER action)
  → `Withdrawn` (terminal)
  → the provider sees the request is withdrawn
  → the `RequestDraft` remains reusable   `UX-13`
  → V1 ENDS
```

Withdrawal is the customer's own act and is always available while the request is live. It is not a `ReportedOutcome` and must never be presented as one — a withdrawn request has no outcome value, and conflating the two would corrupt marketplace-health measurement. It does not penalise the provider, does not close sibling requests, and generates only allowlist-bound notification content: that an event occurred, its type, the acting party's public display name, a non-guessable link, and coarse timing.

### 3.11 `CJX-11` — Event date passes

```text
`Delivered` / `InDiscussion` / `NoResponse`, with a DEFINITE event date   `UX-11`
  → eventDateDefinite AND eventDatePassed (SYSTEM)
  → `Lapsed`   `UX-11`
  → outcome prompt on the governed trigger   `UX-14`
  → close with `ReportedOutcome`
  → `Closed`
  → V1 ENDS
```

**A flexible-date request never lapses.** `flexible` is a first-class valid answer to the date question, not a missing answer, and the system has no fact about the world that would justify expiring it. This is deliberate: `Lapsed` exists because a passed definite date is a fact, not a platform deadline, which is why it needs no policy approval — unlike `NoResponse`. Generic expiry was declined at P01 and must not reappear as a default. The boundary may be wrong for rescheduled or recurring events; that limb sits inside `Q-032` and is `OPEN`.

### 3.12 `CJX-12` — Abuse, report, block

```text
Results / profile / conversation   `UX-04` `UX-05` `UX-12`
  → report OR block (either party may block)
  → report intake WITHOUT requiring the reported content to still be visible
  → operator queue   `UX-27`
  → IF abuseConfirmed → `Quarantined`   `UX-11`
  → blocked party sees that posting is disabled and a POLICY-LEVEL REASON ONLY
  → V1 ENDS for this request
```

Never exposed on any surface: who reported, the report contents, and the reporter's identity. Report intake must not depend on the content still being visible, because the most common report follows deletion or edit. Moderation evidence is preserved; closing a request never deletes conversation evidence. `Quarantined` is terminal and is stated in policy terms only.

---

## 4. What the customer sees versus what the provider sees

The asymmetry is a domain invariant, not a presentation choice. **The customer sees the full lifecycle. The provider sees nothing before `Delivered`, never sees `Abandoned` requests, and never sees the customer's other requests.**

| ID | Stage | Customer sees | Provider sees |
|---|---|---|---|
| `CV-01` | Local `RequestDraft` | The draft, resumable on the same device; cross-device once server-persisted. | **Nothing. A draft has no recipient and is never visible to any provider.** |
| `CV-02` | `PendingVerification` | That the request is saved, that verification is pending, and that it has not been sent yet. | **Nothing.** The request is durable and invisible. |
| `CV-03` | `Abandoned` | That the request was never delivered, retained for reference. | **Nothing, ever.** The provider never learns the request existed. |
| `CV-04` | `VerificationFailed` | That contact control was not proven, with a retry path. | **Nothing.** |
| `CV-05` | `RecipientIneligible` | That the request could not be delivered, in policy terms, without naming which eligibility input changed. | **Nothing.** |
| `CV-06` | `Delivered` | That the request reached the provider's inbox; a delivery-attempt failure is shown to the customer as a delivery problem, **not** as provider non-response. | The full request in `UX-18` / `UX-19`: `EventContext`, category answers, budget band if given, free text if given — **inside the authenticated surface only**. |
| `CV-07` | `InDiscussion` | The response kind, the body, and any `PriceStatement` with currency, basis, inclusions, exclusions, conditions, and as-of. | Their own response and the conversation. **Not the customer's other requests to other providers.** |
| `CV-08` | `Declined` | The decline and the provider's reason if authored. | Their own decline, immutable. **No penalty, no score, no visible consequence.** |
| `CV-09` | `NoResponse` | The state, neutrally, with a manual path onward. | The request is still open and answerable. **The provider is not shown a punitive marker**, and no public badge is derived from it. |
| `CV-10` | `Withdrawn` | Their own act. | That the request was withdrawn. **Not why.** |
| `CV-11` | `Lapsed` | The state and the outcome prompt. | That the event date passed. |
| `CV-12` | `Closed` with `ReportedOutcome` | Their own recorded value and optional secondary reason. | A minimal outcome/lead visibility view. **Never presented as a booking, a conversion, a payment, or a completion.** |
| `CV-13` | Throughout | Their own contact channel, event address, event date, guest count, budget, free text, and conversation content, inside authenticated surfaces. | The same request content inside the authenticated provider surface, **never in a notification body** (`ADR-010`). |

**Never publicly exposed at any stage, to anyone:** precise provider base location; customer name or contact channel; event address (the highest-harm field in V1); event date; guest count; budget; request free text; conversation text; offer amount and terms; `Suspended` as a state; response-rate or response-time badges; `ResponsivenessObservation` and `FreshnessObservation`; report contents or reporter identity.

**Notification bodies (`UX-35`) carry only the allowlist:** that an event occurred and its type, the acting party's public display name, a non-guessable link to the authenticated surface, and coarse timing. A notification is never a `Message`, and Superola must **not** tell owners or users that "in-platform" means contact-protected (`ADR-010`).

---

## 5. Where FUTURE capability would attach

Named as extension points so the seams are visible. **None of these is designed here, and none may be implied on any V1 surface.**

| ID | Capability | Attachment point on this journey | What it would add | Why it is not V1 |
|---|---|---|---|---|
| `CF-01` | Booking and commitment | After `CJ-09`, as a new transition out of `InDiscussion` that does **not** exist in V1 | An accept action that creates an obligation; `Event` becomes a necessary aggregate immediately | `WA-03`, `DB-02`. V1 deliberately has **no accept transition that creates an obligation**. `Offer != Booking`. `UX-38`. |
| `CF-02` | Payment, deposit, payout, refund | After `CF-01`, never before it | A third and fourth lifecycle with independent cancellation, refund, chargeback, and payout timing | `Booking != Payment`. Naming the split now is what stops `ReportedOutcome: proceeding` being mislabelled as either. `UX-38`. |
| `CF-03` | Transaction-derived review | After `CF-02` | A review event whose basis is a verifiable transaction | V1 has no `verified booking` and must not invent one. **Do not fabricate ratings or reviews.** |
| `CF-04` | Fan-out to similar providers | At `CJX-08`, as a **consented** step after the first request | A consent-gated multi-recipient path with recipient provenance per request | `WA-02`, `DB-01`. V1 has no recipient set and no routing. |
| `CF-05` | Calendar availability | At `CJ-04` and `CJ-05`, replacing the date-as-context model | Bookable, synchronised availability with a date filter | `WA-01`, `DB-10`, `ADR-005`. `G-06` is unresolved. **`V1 has no availability model`.** |
| `CF-06` | Assisted intake | Before `CJ-02` and inside `CJ-05`, as an optional prefill layer | Natural language → extracted fields → user confirms or corrects → ask only what is missing | `FUTURE`, four conditions required (see §6). **The model proposes; the form disposes.** |
| `CF-07` | `DemandWatch` / saved search | At `CJX-01`, converting demand capture into a durable watch | A notification when supply appears | `Q-031` unratified and excluded from V1 search scope. Capture in V1 carries **no notification promise**. |
| `CF-08` | Sponsored placement | At `CJ-03`, as a **separately allocated, separately labelled section** | A second `placementBasis` value | `FUTURE` (`ADR-008`, `UX-37`). **Never a `featured` boolean.** |
| `CF-09` | Multi-service event request | At `CJX-09`, replacing the twice-used single-service path | A multi-service RFQ and an `Event` aggregate | `FUTURE`. V1's grouping is presentational only, via `eventGroupingHint`. |

---

## 6. Constraints and gates this journey depends on

This journey is not self-supporting. Each item below is a live dependency that can change the design, and none is P04's to close.

| ID | Unresolved item | What it is | What in this journey moves if it resolves differently | Owner |
|---|---|---|---|---|
| `CG-01` | `G-06` / `Q-007` — what "available" promises a customer | **UNSATISFIED.** The gate that formally blocks P04. P04 proceeds on `WA-01` under explicit David authorization and says so. | `CJ-04`, `CJ-05`, `CJX-02`, `CJX-11`, and every surface carrying the `V1 has no availability model` non-claim. If the owner answers that "available" promises a date, `CF-05` enters V1 and the date question stops being request context. | Owner. |
| `CG-02` | `WA-01` — accepting requests ≠ guaranteed date availability | `DAVID WORKING ASSUMPTION — OWNER VALIDATION PENDING`. Preserves the calendar branch. | The entire relationship between `RequestIntake`, the date question, and `CJX-02`'s wording. | Owner validation pending. |
| `CG-03` | `Q-032` — what Superola may conclude from provider silence | The `NoResponse` window length is **`POLICY PENDING`**. No approved value exists. | `CJX-04` in full, and the prohibition on promising a response time at `CJ-08`. Also the `Lapsed` boundary for rescheduled or recurring events. | Owner + product. |
| `CG-04` | Contact-disclosure policy — `Q-008`, `A-010`, `OR-011` | `OPEN`. When, if ever, parties may exchange direct contact details. | Nothing structural: the disclosure seam (`ADR-010`) means the UX is identical under all three owner answers and only the resolved value changes. That is the point of the seam — but the value is still unset. | Owner policy + compliance. |
| `CG-05` | `Q-026` disclosure limb | The auditability limb is answered (`DAVID_DIRECTIVE`); **whether users are told** an operator read their request or conversation is not. | Whether `CJ-10` and `UX-15` carry a disclosure statement at all. | Owner + David + counsel. |
| `CG-06` | Verification-window length | `OPEN`. No approved value governs `PendingVerification` → `Abandoned`. | `CJX-05` and `CJX-06`. Too short destroys recoverable requests; too long inflates a state the provider cannot see. | Owner + product. |
| `CG-07` | `RequestIntake` decay window length | `OPEN`. Governs `accepting` → `unconfirmed`. | `CJ-04` and `CJX-02`. Note the constraint that survives any value: `unconfirmed` may be surfaced by eligibility and **may not be a ranking input in V1**. | Owner + product. |
| `CG-08` | `ReportedOutcome` secondary reason | A **`PROPOSED` P04 extension to `ADR-003`**, not existing P02. | `CJ-11`. If rejected, the governed triple stands alone and the question *"why did this marketplace fail this customer?"* stays unmeasured. | P02 owner + David. |
| `CG-09` | `SRC-006` NOT RECEIVED | There is no usability or traffic evidence in this repository. | Every efficiency claim in this document is a **design target with a stated measurement**, never a measured result. Composer and verification abandonment under `R-022` are the measurements that would settle them. | — |
| `CG-10` | `Q-031` — `DemandWatch` | Unratified and excluded from V1 search scope. | `CJX-01`'s demand capture must carry **no notification promise** until this is ratified. | Owner + David + product. |
