# Surface Inventory — P04 UX Architecture

> Status: `PROPOSED — OWNER VALIDATION REQUIRED`. Provenance: `TECHNICAL_DISCOVERY`. This is a P04 UX architecture proposal, not an approved design and not a visual specification.
> Working assumptions: `WA-01`–`WA-05` (see `docs/04-ux/README.md`). `G-06` is UNSATISFIED.

This is the **evidence base for `ADR-020`** — the rendering decision. It is a reference register, not a narrative: one record per surface, fixed fields, no journeys. The journeys live in `docs/04-ux/customer-journey.md`, `docs/04-ux/request-intake.md`, `docs/04-ux/provider-workspace.md`, and `docs/04-ux/operator-surfaces.md`, and the decision argument that consumes this evidence lives in `docs/04-ux/rendering-evidence.md`.

**No surface in this inventory has been implemented.** Every client-interaction-complexity judgement below is therefore an **argued estimate, not a measurement** — `ADR-020`'s third required item (whether a progressive-enhancement implementation was attempted and what it cost) has no measured answer anywhere in this document, and no reader may treat one as if it did. There is no usability evidence (`SRC-006` is NOT RECEIVED). This document defines no visual design, no components, no wording, and no routes.

## 1. Legend

### 1.1 Rendering classes (`docs/04-ux/design-canon.md` §5.8)

| Class | Meaning | Consequence |
|---|---|---|
| `DOC` | Mostly document and navigation. | Server-rendered views are sufficient. No island is required **by the class**; an island may still be attached to a `DOC` surface and must then be named. |
| `LOCAL` | Local interactive state that **does not need to survive navigation** — or, as one bounded exception, browser-local state that **does** survive navigation for a defined window while its durable owner is a server-side domain object (`UX-07`'s anonymous pre-`Account` window; see that record). The exception is bounded by three conditions: a named island covers it, a named domain object takes ownership as soon as it exists, and the surface still degrades to a working server-rendered path. | A progressive-enhancement or island treatment, decided per surface. |
| `RICH` | Rich client state that **must survive navigation**. | Would justify `ADR-020` Option B **for that surface**. |

### 1.2 Named islands (`docs/04-ux/design-canon.md` §5.8) — budget of three, reconsideration trigger at five

| Island | What it is | Why progressive enhancement alone is insufficient |
|---|---|---|
| `I-1` | Governed type-ahead picker (`Category` + `Place`) | Per-keystroke governed lookup with keyboard and ARIA combobox semantics; a full round trip per keystroke is a latency and cost problem, and the read path has its own budget. |
| `I-2` | Composer step controller + local draft persistence | Anonymous pre-`Account` draft survival and conditional question reveal without a round trip per answer. |
| `I-3` | Media upload manager | Multi-file selection, per-file progress, and the pre-generated-derivative **processing state** (`ADR-018`) have no HTML-form equivalent. |

**Every island must degrade to a working server-rendered path.** Every island's data access goes through the same application layer and the same single machine-access enforcement point — **no JSON API built "just in case"** (`R-012`, `ADR-012`).

### 1.3 Field definitions used in every record

`User` · `Purpose` · `Primary action` · `Information shown` · `States` · `Empty case` · `Error case` · `Degraded case` (what happens when a dependency is unavailable — geocoder, media delivery, notification, JavaScript disabled) · `V1 / Future` · `Rendering class` · `Client-interaction complexity` (argued estimate) · `Client state survives navigation` · `Public`.

A standing note on the geocoder: **customer-side location resolves against the governed internal `Place` list with zero vendor calls** (`ADR-019` Level 1). A geocoder outage therefore cannot degrade any customer discovery surface. It degrades only provider base-address resolution and unresolved free text.

## 2. Public customer surfaces

### `UX-01` — Home / need entry

| Field | Value |
|---|---|
| User | Customer, anonymous |
| Purpose | Convert an undefined need into a governed `Category` plus a governed `Place` |
| Primary action | Pick a governed `Category` and a governed `Place`, then search |
| Information shown | Governed type-ahead, a small set of high-supply entry paths, an honest statement of what the platform does, the `ADR-005` non-claim in substance |
| States | Idle · typing · no governed match · both selections resolved |
| Empty case | Not applicable — the surface is authored, never data-empty. If the taxonomy has no published categories, the entry paths are absent and the type-ahead states it plainly |
| Error case | Unresolvable input is **rejected and shown to the customer, never approximated**. Lookup failure falls back to browse |
| Degraded case | No JavaScript: `I-1` degrades to a server-rendered browse plus a plain `<select>` over governed labels. Geocoder irrelevant here. Media delivery irrelevant |
| V1 / Future | V1 |
| Rendering class | `DOC` + `I-1` |
| Client-interaction complexity | Low. One combobox with governed lookup and managed active-descendant; everything else is links |
| Client state survives navigation | No |
| Public | Yes |

### `UX-02` — Category / archetype browse

| Field | Value |
|---|---|
| User | Customer, anonymous |
| Purpose | Explain what a governed `Category` covers and route into supply |
| Primary action | Select a `Category`, then a `Place`, entering `UX-03` or `UX-04` |
| Information shown | Governed category definition, localized labels and synonyms, `CategoryArchetype`, what a request in this category will ask for |
| States | Category list · single-category view · deprecated category showing its merge target |
| Empty case | A `Category` with no governed attribute definitions **has no page** — the node is not generated (`R-011`) |
| Error case | Unknown category identifier resolves through retained redirect history, or returns an unambiguous not-found |
| Degraded case | No JavaScript: fully functional — this surface is links and text. Geocoder irrelevant. Media delivery irrelevant |
| V1 / Future | V1 |
| Rendering class | `DOC` |
| Client-interaction complexity | None. Static document and navigation |
| Client state survives navigation | No |
| Public | Yes |

### `UX-03` — Market page (Category × Place)

| Field | Value |
|---|---|
| User | Customer, anonymous |
| Purpose | The people-first page for "this service, in this place" |
| Primary action | Enter a provider profile, or refine into `UX-04` |
| Information shown | Coarse `Place` label, eligible `Published`/`Stale` supply, declared coverage in words, differentiated locally-true content, `placementBasis` = `organic`, the `ADR-005` non-claim |
| States | Has supply · has only `Stale` supply · `undetermined` candidates surfaced with their uncertainty |
| Empty case | **A zero-supply Market page is not generated.** If supply drops to zero after generation, the page states the coverage position honestly and offers correction or a named relaxation — it never fabricates supply and never converts the visit into a broadcast |
| Error case | Unknown place or category identifier resolves through retained redirect history, or returns not-found |
| Degraded case | No JavaScript: fully functional. Media delivery unavailable: profile cards render without imagery — publication gates on the **database fact** that media exists, never on whether the vendor is serving it (`ADR-018`). Geocoder irrelevant |
| V1 / Future | V1 |
| Rendering class | `DOC` |
| Client-interaction complexity | None. Document and navigation |
| Client state survives navigation | No |
| Public | Yes |

### `UX-04` — Search results

| Field | Value |
|---|---|
| User | Customer, anonymous |
| Purpose | Deterministic eligible results for `Category` × `Place`, ranked and explained |
| Primary action | Open a provider profile |
| Information shown | Result slots with `placementBasis` = `organic` and a per-slot disclosure marker, per-slot ranking explanation, freshness indicator on `Stale`, `RequestIntake`, `undetermined` shown **with its uncertainty**, the `ADR-005` non-claim. **Filters are listed without counts** (`Q-035`, P04 recommendation) |
| States | Results · too broad (rank and paginate, do not refuse) · too narrow (relax **with the relaxed constraint named**) · zero results distinguishing *no supply in this category* from *no supply in this area* · insufficient location granularity |
| Empty case | Explain coverage honestly, offer correction or progressive relaxation, capture unmet demand, offer an optional no-promise "tell us what you were looking for". **Never promise notification** — `DemandWatch` and saved searches are `Q-031`, unratified and excluded |
| Error case | Unresolvable free text is rejected and shown, never approximated. Query failure states the failure rather than rendering an empty result set as zero supply |
| Degraded case | No JavaScript: `I-1` degrades to a plain `<select>` and a full-page GET per refinement; results are unaffected because ranking is server-side. Media delivery unavailable: cards render textually. Geocoder irrelevant — `Place` resolves internally with zero vendor calls |
| V1 / Future | V1 |
| Rendering class | `DOC` + `I-1` |
| Client-interaction complexity | Low. One combobox; filters are ordinary form controls submitting to the server. No client-side ranking, no client-side eligibility |
| Client state survives navigation | No |
| Public | Yes |

### `UX-05` — Provider profile (public)

| Field | Value |
|---|---|
| User | Customer, anonymous |
| Purpose | Present the provider's case and open the deliberate request path (`WA-02`) |
| Primary action | Request this provider |
| Information shown | Identity, `ServiceOffering`s with governed category attributes, narrative with explicit `contentLocale`, images, linked-out audio and video, coarse `Place` label and declared coverage in words, publication freshness, `RequestIntake`, `verificationBasis` on every trust-flavoured fact, the `ADR-005` non-claim. **Machine-translated content is explicitly marked** |
| States | `Published` · `Stale` with a freshness indicator (**stays discoverable, never hidden**) · media processing · dead external A/V link. **Customers cannot distinguish `Suspended` from `Deactivated`** |
| Empty case | A profile with no media, no narrative, or a single offering still renders; absent optional fields are simply absent, never placeholder-filled |
| Error case | Unknown or unpublished identifier returns not-found. A `LegacyProviderRecord` is **structurally incapable** of appearing here (`ADR-009`) |
| Degraded case | No JavaScript: fully functional — no island. Media delivery unavailable: text and structure render, images fail gracefully, publication is unaffected (`ADR-018`). Dead A/V link: shown as a dead-link state, swept and queued to `UX-32`-adjacent operator handling via `UX-31`. Geocoder irrelevant — the coarse label is stored, not computed at render |
| V1 / Future | V1 |
| Rendering class | `DOC` |
| Client-interaction complexity | None. This is the highest-value public document in the product and deliberately carries no client state. Target ~200 KB served bytes per anonymous view, 400 KB reconsideration threshold (`ADR-018`) |
| Client state survives navigation | No |
| Public | Yes |

### `UX-06` — Informational / trust pages

| Field | Value |
|---|---|
| User | Anyone, anonymous |
| Purpose | Policy, safety, how the marketplace works, correction and reporting paths |
| Primary action | Read; follow a correction, reporting, or contact path |
| Information shown | Authored first-party text: what a request is, what a response is, what the platform does **not** promise, privacy and consent notices as **versioned content** |
| States | Current version · superseded version retained for consent-record reference |
| Empty case | Not applicable — authored content |
| Error case | Not-found on a retired page, resolved through retained redirect history where a successor exists |
| Degraded case | No JavaScript: fully functional. All other dependencies irrelevant |
| V1 / Future | V1 |
| Rendering class | `DOC` |
| Client-interaction complexity | None |
| Client state survives navigation | No |
| Public | Yes |

## 3. Request composition and identity

### `UX-07` — Request composer (progressive intake)

| Field | Value |
|---|---|
| User | Customer, anonymous or authenticated |
| Purpose | Progressive, deterministic, category-governed intake — ask only the next question the selected `CategoryArchetype` actually requires |
| Primary action | Answer the current question group and advance |
| Information shown | One question group at a time, ordered by the five governed classes of `docs/04-ux/request-intake.md` §6 (`DISCOVERY`, `PRE-SUBMIT`, `CATEGORY`, `QUALITY`, `NOT-V1`), a **named-stage indicator** (never a false "step N of M"), what has been carried from discovery |
| States | Local anonymous draft · server-persisted `RequestDraft` · category changed · event type changed · resumed · abandoned |
| Empty case | First entry with no carried context: the composer asks `Category` then `Place`. Entering from `UX-03`/`UX-04`/`UX-05` reuses them and does not re-ask |
| Error case | Field errors are **associated with their field and announced, not only colored**. A rejected governed lookup is shown, never approximated. Category change drops previous category-specific answers **only after an explicit before-the-fact warning naming what will be lost**, retained in session for immediate undo. Event type change drops nothing |
| Degraded case | No JavaScript: `I-2` degrades to **one server-rendered step per POST**, and `I-1` to a plain `<select>` — the full path still completes. Geocoder irrelevant — `Place` is governed and internal. Notification irrelevant at this stage |
| V1 / Future | V1 |
| Rendering class | `LOCAL` + `I-1`, `I-2` |
| Client-interaction complexity | **Highest in the customer set, and still `LOCAL`.** Conditional question reveal, local draft writes, and a governed combobox. The hard part is accessibility, not state: the revealed region must be announced politely, focus must move to the new region's heading rather than the first input, and answered questions must not reorder. Validation lives in the domain (`ADR-011`), never in the island |
| Client state survives navigation | **Yes, and this surface is the one bounded exception in the inventory.** After an `Account` exists the state is held by `RequestDraft`, a server-persisted domain aggregate (`ADR-003`), and survival is a database read. **Before an `Account` exists (`WA-05`), `UX-07` does contain navigation-persistent anonymous browser-local state, and that state lives nowhere but the client for that window.** P04 proposes satisfying that requirement with the bounded `I-2` island plus local draft persistence rather than adopting a richer client rendering model, and therefore classes the surface `LOCAL` rather than `RICH`. **This is an implementation hypothesis, not a measurement, until `ADR-020`'s `RQ-03` is measured against real code.** This is the answer to `ADR-020`'s `RQ-02` for `UX-07`, stated in these same words in `docs/04-ux/rendering-evidence.md` §3 and §4.1 |
| Public | Yes |

### `UX-08` — Request review and send

| Field | Value |
|---|---|
| User | Customer, anonymous or authenticated |
| Purpose | Let the customer see and correct everything before it can reach a provider |
| Primary action | Send to the one selected provider (`WA-02`) |
| Information shown | Full assembled `EventContext` and category answers, the named recipient, what will and will not be shared, each answer editable in place |
| States | Complete · missing a `PRE-SUBMIT` field · sent |
| Empty case | Unreachable — this surface is only entered from a draft with answers |
| Error case | A missing `PRE-SUBMIT` field returns the customer to the named stage that owns it, with everything else intact. Submission failure preserves the draft entirely |
| Degraded case | No JavaScript: fully functional — a review page and a form POST. Other dependencies irrelevant |
| V1 / Future | V1 |
| Rendering class | `DOC` |
| Client-interaction complexity | None. Deliberately a plain server-rendered review page: this is the last honest checkpoint before delivery and must not depend on client state |
| Client state survives navigation | No |
| Public | Yes |

### `UX-09` — Identity and channel verification

| Field | Value |
|---|---|
| User | Customer |
| Purpose | Prove control of a contact channel before the request may be delivered (`DB-12`, `WA-05`) |
| Primary action | Supply a contact channel and prove control of it |
| Information shown | The channel, the proof step, and the guarantee that the request is **already durably stored as `PendingVerification`** and will not be lost |
| States | Channel supplied · proof pending · proven · expired · abandoned. **No government identity verification.** The only honest V1 claim is `verificationBasis` = control of this contact channel was proven at time T |
| Empty case | Not applicable |
| Error case | Wrong or expired proof is re-issuable without losing the request. **Delivery does not occur** until proof succeeds |
| Degraded case | Notification channel unavailable: the `DeliveryAttempt` failure is **visible to this party** and is never presented as the provider's non-response; the customer is offered a retry or an alternative channel. No JavaScript: fully functional. Geocoder and media irrelevant |
| V1 / Future | V1 |
| Rendering class | `DOC` |
| Client-interaction complexity | None. A form and a state |
| Client state survives navigation | No — `PendingVerification` is a durable server state |
| Public | Transitional. Reached anonymously, exits authenticated. Never indexable, never linkable as an entity |

### `UX-16` — Sign in / sign up

| Field | Value |
|---|---|
| User | Any |
| Purpose | Establish or resume an `Account` |
| Primary action | Sign in, or create an `Account` |
| Information shown | The form only. **No role selection** — `Account` is opaque and not typed by role (`ADR-004`) |
| States | Signed out · in progress · signed in · channel proof pending |
| Empty case | Not applicable |
| Error case | Failures are stated without revealing whether a channel is registered |
| Degraded case | No JavaScript: fully functional. Notification unavailable: proof delivery failure is visible to this party with a retry path |
| V1 / Future | V1 |
| Rendering class | `DOC` |
| Client-interaction complexity | None |
| Client state survives navigation | No. On voluntary sign-in mid-composition, a local draft is **promoted** to a server `RequestDraft` |
| Public | Yes. **Never placed in front of `UX-01`–`UX-08`** — `UX-08` is public and anonymous and is the last anonymous surface under `WA-05`. Reaching `UX-16` from the report affordance on `UX-04`/`UX-05` is an authentication requirement on a reporting action (`UX-39`), **not** a login wall on discovery |

## 4. Authenticated customer and cross-role surfaces

### `UX-10` — Request submitted confirmation

| Field | Value |
|---|---|
| User | Customer |
| Purpose | State exactly what happened and exactly what was not promised |
| Primary action | Continue to `UX-11` |
| Information shown | The request, its recipient, that the provider may quote, clarify, decline, or **not respond** — and that **not responding is a fourth outcome** (`NoResponse`, system-observed, non-punitive). **No availability claim, no booking language, no money-shaped language** |
| States | Submitted and delivered · submitted and pending verification |
| Empty case | Not applicable |
| Error case | Not applicable at this point — delivery failure surfaces in `UX-11`/`UX-12`, visible to its own party |
| Degraded case | No JavaScript: fully functional. Notification unavailable: the on-screen confirmation is authoritative; the notification is a convenience, never the record |
| V1 / Future | V1 |
| Rendering class | `DOC` |
| Client-interaction complexity | None |
| Client state survives navigation | No |
| Public | No |

### `UX-11` — My requests (customer)

| Field | Value |
|---|---|
| User | Customer |
| Purpose | See every `ServiceRequest` this `Account` sent and its current state |
| Primary action | Open a request |
| Information shown | Request list with state, recipient display name, unread marker computed server-side, freshness, and the **light presentational grouping** of requests sharing an `eventGroupingHint`. **This is a presentational grouping, not an `Event` aggregate** |
| States | All twelve `ServiceRequest` states (`docs/02-architecture/domain-model.md` §5.2), because **the customer sees the full lifecycle**: `PendingVerification` · `Abandoned` · `VerificationFailed` · `RecipientIneligible` · `Delivered` · `InDiscussion` · `Declined` · `NoResponse` (window `POLICY PENDING`) · `Withdrawn` · `Quarantined` · `Lapsed` · `Closed` |
| Empty case | No requests yet: route back into discovery. Never a broadcast offer |
| Error case | A `DeliveryAttempt` failure is shown to this party **as a delivery failure**, never as the provider's non-response |
| Degraded case | No JavaScript: fully functional. **Unread is computed server-side and rendered on navigation — no global polling.** Notification unavailable: this surface remains the authoritative record |
| V1 / Future | V1 |
| Rendering class | `DOC` |
| Client-interaction complexity | None. A list |
| Client state survives navigation | No |
| Public | No |

### `UX-12` — Request detail: response + conversation (customer)

| Field | Value |
|---|---|
| User | Customer |
| Purpose | Read the provider's response and continue the conversation |
| Primary action | Reply, or ask for clarification |
| Information shown | One chronological thread: the original request and each `ProviderResponse` as **anchored, visually distinct entries**, plus clarifying messages. Author role always shown. `ThreadEvent` system markers are visually distinct and **never counted as messages**. Response kinds are **user-visibly distinct**: `clarification` · `decline` (optional provider-authored reason, non-punitive, immutable) · `offer` (optional `PriceStatement` with explicit currency, basis, inclusions, exclusions, conditions, as-of). `Superseded` responses remain visible. Which side of the request you are on is stated |
| States | Three entities are rendered on this surface and their states are **never merged into one list** (`domain-model.md` §5.2–§5.4). `ServiceRequest`: `Delivered` · `InDiscussion` · `Declined` · `NoResponse` (window `POLICY PENDING`) · `Withdrawn` · `Quarantined` · `Lapsed` · `Closed`. `ProviderResponse` — kind `clarification` \| `decline` \| `offer`, state `Submitted` · `Acknowledged` · `Superseded` · `Withdrawn` · `Expired`. `Conversation`: `Open` · `Restricted` · `Blocked` · `Closed` · `Retained` · `Purged` |
| Empty case | Delivered with no response yet: state the observation plainly. **Never present silence as a judgement** (`Q-032`) |
| Error case | Post failure preserves the composed text. Blocked: posting is disabled with a **policy-level reason only — never who reported, never the report content** |
| Degraded case | No JavaScript: fully functional — a server-rendered thread plus a form POST. **There is no realtime transport by architecture** (no WebSockets, SSE, presence, typing indicators, read receipts, or push). If a polling refresh is ever added it is **on the open conversation surface only**, stated with its query cost, and is not the default — a 30-second unread poll across 200 sessions is ≈ 6.7 req/s, comparable to all public traffic at Growth (`docs/03-technology/technology-evaluation.md` §2) |
| V1 / Future | V1 |
| Rendering class | `LOCAL` |
| Client-interaction complexity | Low. A message composer with local text state and optional client-side length feedback. **No accept button that creates an obligation — a quote is not a booking** |
| Client state survives navigation | No |
| Public | No |

### `UX-13` — My drafts

| Field | Value |
|---|---|
| User | Customer |
| Purpose | Resume unsent `RequestDraft`s |
| Primary action | Resume a draft |
| Information shown | Draft list with `Category`, `Place`, last stage reached. **A draft has no recipient and is never visible to any provider** |
| States | Local-only (same device) · server-persisted (cross-device resumable) |
| Empty case | No drafts: route into discovery |
| Error case | A local draft whose `Category` has been deprecated resolves through the recorded merge target, or is shown as needing a new `Category` — never silently remapped |
| Degraded case | No JavaScript: server-persisted drafts list and resume normally. **Local anonymous drafts are invisible here by definition** — they exist only in the originating browser |
| V1 / Future | V1 |
| Rendering class | `DOC` |
| Client-interaction complexity | None. A list |
| Client state survives navigation | No |
| Public | No |

### `UX-14` — Outcome capture

| Field | Value |
|---|---|
| User | Customer |
| Purpose | Record `ReportedOutcome`, customer-only and `self_declared`, on a governed trigger |
| Primary action | Report the outcome |
| Information shown | The governed triple `proceeding` / `not_proceeding` / `unknown`, plus an **optional secondary reason** (chose another provider · provider did not respond · price too high · plans changed · still deciding). The secondary reason is a `PROPOSED` P04 **extension** to `ADR-003`, never a replacement enum |
| States | `Unreported` (silence) · reported. **`unknown` is an explicit customer answer and is not `Unreported` — never collapse them** |
| Empty case | Not prompted outside its governed trigger |
| Error case | Submission failure preserves the selection |
| Degraded case | No JavaScript: fully functional as a form POST |
| V1 / Future | V1 |
| Rendering class | `LOCAL` |
| Client-interaction complexity | Very low. Conditional reveal of the optional secondary reason. **No implication of payment or confirmed booking anywhere on this surface** |
| Client state survives navigation | No |
| Public | No |

### `UX-15` — Account and settings (locale, notifications, consent)

| Field | Value |
|---|---|
| User | Any authenticated `Account` |
| Purpose | Manage `uiLocale`, notification preferences, contact channels, and consent |
| Primary action | Change a setting |
| Information shown | `uiLocale`, channel list with `verificationBasis` per channel, notification preferences, consent records **with the version of the notice text recorded on each**, data export and deletion paths |
| States | Saved · pending channel proof · consent superseded by a new notice version |
| Empty case | Not applicable |
| Error case | A failed save states which setting failed; nothing is partially applied silently |
| Degraded case | No JavaScript: fully functional. Notification unavailable: channel-proof failure is visible here with a retry |
| V1 / Future | V1 |
| Rendering class | `DOC` |
| Client-interaction complexity | None. Forms |
| Client state survives navigation | No |
| Public | No |

### `UX-39` — Report intake and block confirmation

Reached by a customer or a provider acting as a participant, never by an operator; recorded here rather than in §5 or §6 because the same surface serves both sides of the marketplace. Added during adversarial review, which found report and block drawn as actions on `UX-04`, `UX-05`, `UX-12` and `UX-20` with no surface record behind them.

| Field | Value |
|---|---|
| User | Customer or Provider, **authenticated**. `domain-model.md` requires a `Report` to come from an authenticated participant |
| Purpose | Compose a `Report` against a named target — a `ProviderProfile`, a `ServiceRequest`, a `ProviderResponse`, or a `Conversation` — and create or revoke a `Block` against the other party |
| Primary action | Submit a report with a governed reason, or confirm a block |
| Information shown | The governed reason list, an optional free-text note, what a report is and is not (**a report is not a finding**), and what a block does — it prevents further interaction, **survives closure of any request, and applies to future ones**. **Neither party ever learns who reported or what the report said**; a blocked party sees a policy-level reason only |
| States | `Report`: composing · submitted · acknowledged. `Block`: absent · created · revoked. The `Conversation` consequences (`Restricted`, `Blocked`) are decided by a `ModerationDecision` on `UX-27` and are rendered on `UX-12` / `UX-20`, never here |
| Empty case | Not applicable — the surface is only ever entered against a named target |
| Error case | **Report intake must not require the reported content to still be visible.** A deleted, edited, unpublished, `Suspended` or `Quarantined` target must not block submission; the report is recorded against the target's identity and the evidence is retained by the case (`UX-27`). Submission failure preserves the composed note |
| Degraded case | No JavaScript: fully functional — a reason form and a POST. Media delivery unavailable: intake still completes, because it does not depend on rendering the target. Notification unavailable: the on-screen acknowledgement is authoritative and the failure is visible to this party, never presented as inaction by anyone else |
| V1 / Future | V1 |
| Rendering class | `LOCAL` |
| Client-interaction complexity | Very low. Conditional reveal of the reason-specific note field. **No island** |
| Client state survives navigation | No |
| Public | **No — authenticated.** The public affordance on `UX-04` and `UX-05` routes through `UX-16` sign-in and returns here with the target carried; entry from `UX-12` and `UX-20` is already authenticated. **This is an authentication requirement on a reporting action, not a login wall on discovery** — `UX-01`–`UX-08` remain anonymous under `WA-05`. Requiring authentication is also what bounds `UX-27`'s queue volume: anonymous intake would make operator case creation unrate-limited and **unbounded by design**. The reporter's free-text note is classified **sensitive** — reporters describe harassment in it — and is never public, never in a notification body (`ADR-010`), and readable only through an audited access (`UX-34`) |

## 5. Provider surfaces

Cross-reference `docs/04-ux/provider-workspace.md`. Providers are **mobile-first in practice** (`DB-06`), which makes `UX-19` the single highest-risk mobile surface in the product.

### `UX-17` — Provider onboarding (progressive)

| Field | Value |
|---|---|
| User | Provider |
| Purpose | Take an `Account` to a `Business` with a publishable `ProviderProfile` |
| Primary action | Complete the next requirement |
| Information shown | A **live requirements checklist from the first screen**, each item stating why it is required and what it unlocks; order is `Account` → `Business` → first `ServiceOffering` → service area → category attributes → narrative → media → preview → submit |
| States | Account created · `Business` created · offering added · `ServiceAreaDeclaration` valid · attributes complete · media rights acknowledged · `publicationGateMet` · submitted |
| Empty case | First entry shows the full checklist, all unmet, with the public-profile **preview available before every requirement is satisfied** — value before completeness |
| Error case | An invalid `ServiceAreaDeclaration` or an insufficient-precision `BaseLocation` names the archetype predicate it fails. **Silent publication of an incomplete profile must not happen** |
| Degraded case | **Geocoder unavailable: base-address resolution is the one place a geocoder is on the path.** The address is stored as entered, `LocationEligibility` is `undetermined`, the state is **surfaced, never coerced**, and it queues to `UX-31`. No JavaScript: `I-1` degrades to a plain `<select>`, `I-3` degrades to a single-file multipart POST per file with the processing state rendered on reload. Progress is saved at every step; nothing is lost |
| V1 / Future | V1 |
| Rendering class | `LOCAL` + `I-1`, `I-3` |
| Client-interaction complexity | Moderate and the highest in the provider set. Governed combobox, archetype-conditional attribute reveal, and multi-file upload with per-file processing state. Still `LOCAL` — every step commits to the server, so nothing needs to survive navigation client-side |
| Client state survives navigation | No. Server-side step state carries it |
| Public | No |

### `UX-18` — Provider workspace home / request inbox

| Field | Value |
|---|---|
| User | Provider |
| Purpose | See incoming `ServiceRequest`s and what needs action |
| Primary action | Open a request |
| Information shown | Requests newest-first with state and unread marker, publication state, `RequestIntake`, `Stale` reconfirm prompt. **Not a CRM** — no pipeline stages, no custom fields, no tags, no bulk actions, no reporting builder, no calendar |
| States | New · read · responded · `NoResponse` window elapsed · closed |
| Empty case | No requests: state it plainly, plus the publication and `RequestIntake` facts that affect eligibility. **No fabricated activity, no vanity metrics** |
| Error case | Not applicable beyond load failure |
| Degraded case | No JavaScript: fully functional. **Unread is computed server-side and rendered on navigation — no global polling.** Notification unavailable: this inbox remains the authoritative record and the provider is never penalised for a delivery failure |
| V1 / Future | V1 |
| Rendering class | `DOC` |
| Client-interaction complexity | None. A list |
| Client state survives navigation | No |
| Public | No |

### `UX-19` — Provider request detail and respond

| Field | Value |
|---|---|
| User | Provider |
| Purpose | Read the full request and respond |
| Primary action | Respond as `clarification`, `decline`, or `offer` |
| Information shown | Full `EventContext` and category answers, budget band where supplied (**authenticated surface only, never in a notification body** — `ADR-010`), which side of the request the reader is on, and *You are replying as **{Business}*** |
| States | Unanswered · `clarification` sent · `decline` sent (optional provider-authored reason, **never operator-derived, never punitive**, immutable) · `offer` sent · `Superseded` |
| Empty case | Not applicable |
| Error case | Post failure preserves the composed response. Structured offer fields, revision, and expiry are **archetype-gated flags** (`structuredFields`, `revisionEnabled`, `expiryEnabled`, `invalidateOnAmendment`); where revision is disabled, "current offer" is simply the most recent by recency, with no acknowledgement machinery |
| Degraded case | No JavaScript: a server-rendered form with archetype-conditional fields rendered server-side; fully completable. **Must be completable on a phone** (`DB-06`) |
| V1 / Future | V1 |
| Rendering class | `LOCAL` |
| Client-interaction complexity | Low to moderate. Archetype-conditional field reveal and `PriceStatement` composition with explicit currency. **Currency is never implicit.** Validation lives in the domain (`ADR-011`) |
| Client state survives navigation | No |
| Public | No |

### `UX-20` — Provider conversation

| Field | Value |
|---|---|
| User | Provider |
| Purpose | Asynchronous clarifying text attached to the request |
| Primary action | Post a message |
| Information shown | One chronological thread with the request and each `ProviderResponse` anchored and visually distinct; author role always shown; `ThreadEvent` markers distinct and never counted as messages; *You are replying as **{Business}*** |
| States | Open · blocked · closed · withdrawn by customer |
| Empty case | No messages yet: the request and any response are still the thread's anchors |
| Error case | Post failure preserves the text. Blocked: posting disabled with a **policy-level reason only** |
| Degraded case | No JavaScript: fully functional. **No realtime transport by architecture.** Free text is classified as possibly containing contact data regardless of policy, and **Superola must not tell users that "in-platform" means contact-protected** (`ADR-010`) |
| V1 / Future | V1 |
| Rendering class | `LOCAL` |
| Client-interaction complexity | Low. A composer with local text state |
| Client state survives navigation | No |
| Public | No |

### `UX-21` — Profile editor

| Field | Value |
|---|---|
| User | Provider |
| Purpose | Author the narrative and identity that `UX-05` renders |
| Primary action | Edit and save a profile field |
| Information shown | Narrative fields with explicit `contentLocale`, identity fields, live requirements impact, public preview |
| States | `Draft` · `PendingReview` · `Published` · `Stale` · `Deactivated` · `Suspended` · `Rejected` · `Closed` — **all provider-visible with reasons**; customers see only `Published`/`Stale` |
| Empty case | New profile: every field empty, checklist complete, preview available |
| Error case | Save failure preserves the edit. **Raising a publication requirement must not retroactively unpublish** |
| Degraded case | No JavaScript: fully functional as server-rendered forms. Spanish strings run materially longer than English — **no fixed-width control may depend on English string length** |
| V1 / Future | V1 |
| Rendering class | `LOCAL` |
| Client-interaction complexity | Low. Form state and unsaved-change warnings |
| Client state survives navigation | No |
| Public | No — but it authors public content |

### `UX-22` — Offering editor (category attributes, service area, `RequestIntake`)

| Field | Value |
|---|---|
| User | Provider |
| Purpose | Define each `ServiceOffering` — category, governed attributes, `ServiceAreaDeclaration`, `RequestIntake` |
| Primary action | Save an offering |
| Information shown | Governed `CategoryAttributeDefinition`s for the archetype (**operator-governed, never provider-writable**), `ServiceAreaDeclaration` as a named-market list or a radius **with units preserved as entered**, `RequestIntake` control |
| States | `RequestIntake` = `accepting` · `paused` (optional horizon, optional provider-authored reason) · `unconfirmed`. Eligibility is per `ServiceOffering`, **never per `Business`** |
| Empty case | No offerings: the checklist names the first offering as the blocking requirement |
| Error case | A category the provider wants that does not exist becomes a `CategoryProposal` (`UX-30`), **never a node**. An archetype-invalid service area names the predicate it fails |
| Degraded case | **Geocoder unavailable: an unresolved area or base address yields `undetermined`, surfaced and queued to `UX-31`, never coerced.** No JavaScript: `I-1` degrades to a plain `<select>` over the governed `Place` and `Category` lists |
| V1 / Future | V1 |
| Rendering class | `LOCAL` + `I-1` |
| Client-interaction complexity | Moderate. Archetype-conditional attribute reveal plus a governed combobox. `RequestIntake` is an **intake state, not availability**, and its wording must never read as date unavailability |
| Client state survives navigation | No |
| Public | No — but it authors public content |

### `UX-23` — Media manager

| Field | Value |
|---|---|
| User | Provider |
| Purpose | Manage images, and link out audio and video |
| Primary action | Upload an image, or add an A/V link |
| Information shown | Per-file processing state, derivative readiness, alternative text per `MediaReference` placement, rights acknowledgement, per-provider count and per-file size caps, dead external A/V link state |
| States | Selected · uploading · processing · ready · failed · rights not acknowledged · dead link |
| Empty case | No media: profile still publishable if the requirement set permits; the checklist states the effect |
| Error case | Cap exceeded, unsupported type, or a failed re-encode is stated per file. **Metadata is stripped on re-encode.** **Audio and video are link/embed fields, never uploaders** (`ADR-018`) |
| Degraded case | **Media delivery unavailable: publication gates on the database fact that media exists, never on whether the vendor is currently serving it.** Dead A/V links are a real state needing a sweep and an operator queue. No JavaScript: `I-3` degrades to a single-file multipart POST per file, with the processing state rendered on the next page load and no progress bar |
| V1 / Future | V1 |
| Rendering class | `LOCAL` + `I-3` |
| Client-interaction complexity | Moderate, and the only surface where an island is unavoidable. Multi-file selection, per-file progress, and a **pre-generated-derivative processing state from a small fixed enumerated set** (no on-the-fly sizes) have no HTML-form equivalent. Alternative text is a **publication-quality item, not an afterthought** |
| Client state survives navigation | No. In-flight uploads are abandoned on navigation and re-startable; completed uploads are server facts |
| Public | No — but it authors public media |

### `UX-24` — Publication state and requirements checklist

| Field | Value |
|---|---|
| User | Provider |
| Purpose | State exactly why the profile is or is not publishable |
| Primary action | Act on the next unmet requirement, or `reconfirm` when `Stale` |
| Information shown | `publicationGateMet` broken into its archetype-aware parts: governed required attributes at the current requirement version · at least one `VerificationFact` proving control of a provider contact channel · media rights acknowledged · at least one `ServiceOffering` with an archetype-valid `ServiceAreaDeclaration` and a sufficiently precise `BaseLocation` · no blocking moderation state · entitlement eligible (a constant `true` in V1) |
| States | All eight publication states, provider-visible **with reasons** |
| Empty case | Not applicable |
| Error case | A requirement-version change is shown as new work; it **does not retroactively unpublish** |
| Degraded case | No JavaScript: fully functional. **`Stale` → `reconfirm` is a one-action path from a notification link — the highest-frequency provider maintenance action in the product** — and must work as a plain link plus a single POST |
| V1 / Future | V1 |
| Rendering class | `DOC` |
| Client-interaction complexity | None. A checklist and one action |
| Client state survives navigation | No |
| Public | No |

### `UX-25` — Provider settings and notifications

| Field | Value |
|---|---|
| User | Provider |
| Purpose | Manage `Business`-scoped notification preferences and membership |
| Primary action | Change a setting |
| Information shown | Notification preferences per event type, provider contact channels with `verificationBasis`, `BusinessMembership` list |
| States | Saved · pending channel proof |
| Empty case | Not applicable |
| Error case | A failed save names the setting |
| Degraded case | Notification unavailable: **`DeliveryAttempt` failure is visible to this party** and queued to `UX-32`; it is never presented as the counterparty's non-response. No JavaScript: fully functional |
| V1 / Future | V1 |
| Rendering class | `DOC` |
| Client-interaction complexity | None. Forms |
| Client state survives navigation | No |
| Public | No |

## 6. Operator surfaces

Cross-reference `docs/04-ux/operator-surfaces.md`. Exception-only, minimum viable. Each queue records **reason, state, priority where justified, actor/owner, and an auditable action**. **No generic admin suite, no entitlement engine, no sponsored-campaign system, no dashboard inflation.** **Eight domain queues plus a cross-queue index, on nine surfaces (`UX-26`–`UX-34`); `UX-29` carries two case kinds.** That is already a workload claim at launch: the operator-hours assumption is **unmeasured**, and the modelled frame is 8.35 / 55.42 / 340.58 hours per month at the three scenarios. Operator queues are the one surface class allowed to be desktop-first, and that is **stated rather than assumed**. Authorization for every one of them is decided in the domain (`ADR-011`), never by a route guard.

### `UX-26` — Operator case queue index

| Field | Value |
|---|---|
| User | Operator |
| Purpose | The cross-queue index: one entry point to each of the **eight domain queues** (`UX-27`–`UX-34`) with its outstanding count. It is not itself a domain queue |
| Primary action | Enter a queue |
| Information shown | The eight domain queues, outstanding counts, oldest-item age. `UX-29` is listed with **two case kinds** — duplicate suspicion and `OwnershipClaim`s — under one queue |
| States | Empty · has items · backlogged |
| Empty case | All queues empty is a valid and expected state and is shown as such |
| Error case | Load failure states the failure rather than rendering zero |
| Degraded case | No JavaScript: fully functional |
| V1 / Future | V1 |
| Rendering class | `DOC` |
| Client-interaction complexity | None |
| Client state survives navigation | No |
| Public | No |

### `UX-27` — Report / moderation case detail

| Field | Value |
|---|---|
| User | Operator |
| Purpose | Work one moderation case to an auditable action |
| Primary action | Record a decision with a reason |
| Information shown | Case reason, state, priority where justified, actor/owner, action history, and the reported content **where access is audited and reason-tagged** |
| States | Open · in progress · actioned · closed |
| Empty case | Not applicable |
| Error case | **Report intake must not require the reported content to still be visible.** A deleted or edited target must not block the case |
| Degraded case | No JavaScript: fully functional as forms. Media delivery unavailable: the case still resolves on its recorded facts |
| V1 / Future | V1 |
| Rendering class | `LOCAL` |
| Client-interaction complexity | Low. Conditional reason fields per action type. **Neither party ever learns who reported or what the report said** |
| Client state survives navigation | No |
| Public | No |

### `UX-28` — Publication review queue

| Field | Value |
|---|---|
| User | Operator |
| Purpose | Review profiles in `PendingReview` where the `Category` requires review |
| Primary action | Approve or reject with a reason |
| Information shown | Profile as it would publish, unmet or borderline requirements, rights acknowledgements |
| States | Pending · approved · rejected with a provider-visible reason |
| Empty case | Empty when no Category in the launch cohort requires review |
| Error case | Load failure states the failure |
| Degraded case | Media delivery unavailable: review proceeds on recorded facts; publication gates on the database fact that media exists (`ADR-018`). No JavaScript: fully functional |
| V1 / Future | V1 |
| Rendering class | `DOC` |
| Client-interaction complexity | None. A table plus a decision form |
| Client state survives navigation | No |
| Public | No |

### `UX-29` — Duplicate suspicion and ownership claims

| Field | Value |
|---|---|
| User | Operator |
| Purpose | Resolve suspected duplicate providers and adjudicate `OwnershipClaim`s |
| Primary action | Merge, dismiss, or grant a claim |
| Information shown | Suspected pairs with the signals that raised them; claim evidence |
| States | Open · merged · dismissed · claim granted · claim refused |
| Empty case | **The claims side is empty unless a legacy cohort is approved.** Empty is the expected V1 state |
| Error case | A merge that would publish a `LegacyProviderRecord` is refused — such a record is **structurally incapable** of appearing in discovery or receiving a request (`ADR-009`) |
| Degraded case | No JavaScript: fully functional |
| V1 / Future | V1 (claims empty unless a cohort is approved) |
| Rendering class | `DOC` |
| Client-interaction complexity | None. A table plus decision forms |
| Client state survives navigation | No |
| Public | No. **Disclose nothing before a claim grant — even a masked contact hint is an enumeration oracle** |

### `UX-30` — `CategoryProposal` review

| Field | Value |
|---|---|
| User | Operator |
| Purpose | Govern taxonomy growth |
| Primary action | Accept, merge, or refuse a `CategoryProposal` |
| Information shown | Proposal text, proposing `Business`, nearest existing governed categories and synonyms, demand signal from `UX-04` zero-result capture |
| States | Open · accepted · merged into an existing category · refused |
| Empty case | Expected to be sparse. Empty is normal |
| Error case | Acceptance requires governed attribute definitions to exist — **adding a Category is a governance act; a proposal never becomes a node by itself** |
| Degraded case | No JavaScript: fully functional |
| V1 / Future | V1 |
| Rendering class | `DOC` |
| Client-interaction complexity | None |
| Client state survives navigation | No |
| Public | No |

### `UX-31` — Geocode / `undetermined` / eligibility exceptions

| Field | Value |
|---|---|
| User | Operator |
| Purpose | Resolve `LocationEligibility` = `undetermined` and geocode failures |
| Primary action | Resolve a place, or record that it remains `undetermined` |
| Information shown | The unresolved address or free text, the `GeoPoint` with its **precision and provenance**, the archetype predicate that could not be evaluated |
| States | Unresolved · resolved · permanently `undetermined` |
| Empty case | Empty is the healthy state |
| Error case | **`undetermined` is never coerced to `eligible` or `ineligible`.** It is surfaced to the customer with its uncertainty regardless of queue depth |
| Degraded case | **Geocoder unavailable: the queue accumulates; nothing is guessed.** Customer discovery is unaffected because customer-side `Place` resolution makes zero vendor calls (`ADR-019` Level 1). No JavaScript: fully functional |
| V1 / Future | V1 |
| Rendering class | `DOC` |
| Client-interaction complexity | None. A table plus a resolution form. **No rendered map** (`ADR-019` Level 3) |
| Client state survives navigation | No |
| Public | No |

### `UX-32` — Delivery-attempt failure queue

| Field | Value |
|---|---|
| User | Operator |
| Purpose | See `DeliveryAttempt` failures and act on systemic ones |
| Primary action | Retry, or mark a channel unreachable |
| Information shown | Failed attempts by `NotificationIntent` type, channel class, failure reason, affected party |
| States | Failed · retried · permanently unreachable |
| Empty case | Empty is the healthy state |
| Error case | **A notification is never conflated with a `Message`.** A failure is visible to its own party and is **never presented as the counterparty's non-response** |
| Degraded case | Notification provider unavailable: this queue is the surface where that outage becomes visible and is its primary diagnostic. No JavaScript: fully functional |
| V1 / Future | V1 |
| Rendering class | `DOC` |
| Client-interaction complexity | None |
| Client state survives navigation | No |
| Public | No |

### `UX-33` — Staleness and `RequestIntake` decay / reactivation

| Field | Value |
|---|---|
| User | Operator |
| Purpose | Keep public supply honest as it ages |
| Primary action | Trigger a reconfirm prompt, or record a decay transition |
| Information shown | `FreshnessObservation` per profile, `RequestIntake` age, last reconfirm. **These observations are internal and never public** — `docs/02-architecture/domain-model.md` §1.9 records `FreshnessObservation` and `ResponsivenessObservation` as *computed and retained; NOT published as a label in V1*, and `docs/02-architecture/security-privacy-architecture.md` §12 governs it as an allowlist in which anything not listed is prohibited for that element |
| States | Fresh · `Stale` · intake decayed to `unconfirmed` · reactivated |
| Empty case | Empty is the healthy state |
| Error case | **`Stale` stays discoverable and flagged, never hidden.** The decay window length is `POLICY PENDING` and P04 does not set it |
| Degraded case | Notification unavailable: reconfirm prompts queue rather than being dropped, and `UX-24` remains a working in-product path. No JavaScript: fully functional |
| V1 / Future | V1 |
| Rendering class | `DOC` |
| Client-interaction complexity | None |
| Client state survives navigation | No |
| Public | No |

### `UX-34` — Audited private-content access

| Field | Value |
|---|---|
| User | Operator |
| Purpose | Make every operator read of private content an auditable, reason-tagged event |
| Primary action | Open private content **with a recorded reason** |
| Information shown | The access log: actor, target, reason tag, timestamp; and the gated content itself |
| States | Access requested · access recorded · access reviewed |
| Empty case | Empty is the expected state |
| Error case | Access without a reason tag is refused. **`Q-026`'s auditability limb is resolved here; its disclosure limb remains open and P04 does not resolve it** |
| Degraded case | No JavaScript: fully functional. If the audit record cannot be written, **access is refused** — the audit is not best-effort |
| V1 / Future | V1 |
| Rendering class | `DOC` |
| Client-interaction complexity | None |
| Client state survives navigation | No |
| Public | No |

## 7. Channel and future surfaces

### `UX-35` — Notification message (email body, allowlist-bound)

| Field | Value |
|---|---|
| User | Any party to an event |
| Purpose | Tell a party that something happened and give them a way back into the authenticated surface |
| Primary action | Follow a non-guessable link into the product |
| Information shown | **Permitted only:** that an event occurred and its type · the acting party's public display name · a non-guessable link to the authenticated surface · coarse timing. **Forbidden:** counterparty contact data · request free text · event address · event date · guest count · budget · offer amounts or terms · any conversation content (`ADR-010`). **No money-shaped language** (`DB-02`) |
| States | `NotificationIntent` created · `DeliveryAttempt` sent · delivered · failed |
| Empty case | Not applicable |
| Error case | Failure surfaces to its own party and in `UX-32`, **never as the counterparty's non-response** |
| Degraded case | Channel provider unavailable: the in-product surfaces (`UX-11`, `UX-18`) remain the authoritative record. **There is no push and no server-initiated transport** — a notification is not a fallback for a missing realtime channel, because no realtime channel exists by architecture |
| V1 / Future | V1 |
| Rendering class | n/a — not a web surface |
| Client-interaction complexity | None. A templated body, per locale and per channel |
| Client state survives navigation | No |
| Public | No. Channel-bound |

### `UX-36` — Legacy profile claim

| Field | Value |
|---|---|
| User | Provider |
| Purpose | Bind ownership of a `LegacyProviderRecord` to an `Account` |
| Primary action | Submit an `OwnershipClaim` |
| Information shown | **Nothing about the record before a claim is granted.** After a grant, the claim seeds a new `ProviderProfile` in `Draft` |
| States | Claim submitted · granted · refused |
| Empty case | The whole surface is absent unless a legacy cohort is approved |
| Error case | **Claim does not publish.** A granted claim produces a `Draft`, which then follows `UX-17` and `UX-24` |
| Degraded case | No JavaScript: fully functional |
| V1 / Future | `FUTURE` / conditional (`ADR-009`, P05) |
| Rendering class | `DOC` |
| Client-interaction complexity | None. A form |
| Client state survives navigation | No |
| Public | No. **Even a masked contact hint is an enumeration oracle** |

### `UX-37` — Sponsored placement surfaces

| Field | Value |
|---|---|
| User | Operator / Provider |
| Purpose | Extension point only. Recorded so that `placementBasis` and the per-slot disclosure marker exist from day one |
| Primary action | n/a in V1 |
| Information shown | n/a in V1. When it ships, sponsored placement is a **separately allocated, separately labelled section** |
| States | n/a in V1. `placementBasis` has the single value `organic` in V1, on every result slot **and every measurement event** |
| Empty case | n/a |
| Error case | n/a |
| Degraded case | n/a |
| V1 / Future | `FUTURE` (`ADR-008`) |
| Rendering class | — |
| Client-interaction complexity | Not assessed. Out of the V1 slice |
| Client state survives navigation | n/a |
| Public | n/a. **No `featured` or `sponsored` boolean exists in V1** |

### `UX-38` — Booking / payment / payout surfaces

| Field | Value |
|---|---|
| User | — |
| Purpose | Extension point only. Recorded so P04 can show where a transaction would attach without designing it |
| Primary action | n/a in V1 |
| Information shown | n/a in V1 |
| States | n/a. **`ReportedOutcome: proceeding` is neither a booking nor a payment**, and there is no `verified booking` in V1 |
| Empty case | n/a |
| Error case | n/a |
| Degraded case | n/a |
| V1 / Future | `FUTURE` (`WA-03`, `DB-02`) — Transaction Extension, separately owner-approved |
| Rendering class | — |
| Client-interaction complexity | Not assessed. Out of the V1 slice |
| Client state survives navigation | n/a |
| Public | n/a |

## 8. Roll-up

### 8.1 By rendering class

| Rendering class | Total | V1 | `FUTURE` | Surfaces |
|---|---|---|---|---|
| `DOC` | 25 | 24 | 1 | `UX-01` `UX-02` `UX-03` `UX-04` `UX-05` `UX-06` `UX-08` `UX-09` `UX-10` `UX-11` `UX-13` `UX-15` `UX-16` `UX-18` `UX-24` `UX-25` `UX-26` `UX-28` `UX-29` `UX-30` `UX-31` `UX-32` `UX-33` `UX-34` · `UX-36` (`FUTURE`) |
| `LOCAL` | 11 | 11 | 0 | `UX-07` `UX-12` `UX-14` `UX-17` `UX-19` `UX-20` `UX-21` `UX-22` `UX-23` `UX-27` `UX-39` |
| `RICH` | **0** | **0** | **0** | **None.** |
| n/a or not assessed | 3 | 1 | 2 | `UX-35` (channel, V1) · `UX-37` `UX-38` (`FUTURE`) |
| **Total** | **39** | **36** | **3** | — |

Of the 36 V1 surfaces, **35 are rendered web surfaces** — 24 `DOC` and 11 `LOCAL` — and `UX-35` is an email body, classified `n/a`.

### 8.2 By V1 slice

| Slice | Count | Note |
|---|---|---|
| V1 | 36 | `UX-01`–`UX-35` plus `UX-39` |
| `FUTURE` | 3 | `UX-36` (conditional on an approved legacy cohort), `UX-37`, `UX-38` |

### 8.3 Island usage against the budget

| Island | Surfaces using it | Count |
|---|---|---|
| `I-1` governed type-ahead picker | `UX-01` `UX-04` `UX-07` `UX-17` `UX-22` | 5 surfaces, **1 island** |
| `I-2` composer step controller + local draft | `UX-07` | 1 surface, **1 island** |
| `I-3` media upload manager | `UX-17` `UX-23` | 2 surfaces, **1 island** |
| **Named islands total** | — | **3, against a reconsideration trigger of 5** |

### 8.4 Headline finding

> **No V1 surface is `RICH`, and the named island count is three against a reconsideration trigger of five.**

The one candidate for `RICH` — the multi-step request composer `UX-07` — has its cross-navigation state solved by a **domain object that already exists**: `RequestDraft`, browser-local while anonymous and server-persisted once an `Account` exists. That is not a client-framework requirement; it is the aggregate P02 designed for exactly this purpose. The conversation surfaces (`UX-12`, `UX-20`) have **no realtime transport by architecture**, so each is a form post plus a server-rendered thread. The operator queues (`UX-26`–`UX-34`) are tables with filters. The provider response surface (`UX-19`) is a form with archetype-conditional fields. The one surface where an island is genuinely unavoidable is `UX-23`, because a pre-generated-derivative **processing state** has no HTML-form equivalent (`ADR-018`).

The strongest `RICH` candidate that could have existed — a rendered map — **does not exist in V1** (`ADR-019` Level 3, decided on user value, not vendor capability). Had it shipped, it would single-handedly have weakened the `ADR-020` Option A recommendation.

## 9. Constraints on this inventory

1. **This document is evidence, not a decision.** The decision argument that consumes it is `docs/04-ux/rendering-evidence.md`, which must record, per surface, `ADR-020`'s four required items: the interaction requirement evidenced by a named journey in `docs/04-ux/`; whether client state must survive navigation; whether a progressive-enhancement implementation was attempted and what it cost; and whether the surface is public. Those four items are recorded for **all thirty-seven classified surfaces** — every surface in this register except `UX-37` and `UX-38`, which are `FUTURE`, are not designed, and are **deliberately left unclassified rather than guessed at**. A `FUTURE` surface must not be used to argue a V1 rendering model.
   On the fourth item: `ADR-020` Decision rule 4 names `UX-01`–`UX-06` and `UX-16` as the public surfaces it binds. **P04 extends the same constraint to `UX-07` and `UX-08`** — recorded here as a P04 extension, not as the ADR's own scope — because both are reached anonymously under `WA-05` and `UX-08` is the last anonymous surface before submit.
2. **Nothing here was implemented or measured.** Every complexity judgement is an argued estimate. `ADR-020`'s third required item has **no measured answer in this repository**, and `rendering-evidence.md` must say so in the same words.
3. **Cost is not the deciding factor** and must not be presented as one — rendering moves no infrastructure line. The deciding factors are SEO and acquisition on public surfaces, accessibility of dynamic question reveal, validation living in the domain (`ADR-011`), one machine-access enforcement point (`ADR-012`), and one part-time developer.
4. **The island budget is a budget.** A fourth island is permitted only with a named surface and a stated reason; a fifth triggers reconsideration of `ADR-020` Option A itself.
5. **`G-06` is UNSATISFIED.** Every record above proceeds on `WA-01` — `DAVID WORKING ASSUMPTION — OWNER VALIDATION PENDING`.
6. Surface IDs are stable. **Never renumber, never reuse, never drop an ID from this register.**

---

*Record dates — phase executed 2026-08-12. Nothing has been implemented, so every interaction-cost judgement in this document is an argued estimate rather than a measurement.*
