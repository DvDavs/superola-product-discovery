# Provider Workspace — P04 UX Architecture

> Status: `PROPOSED — OWNER VALIDATION REQUIRED`. Provenance: `TECHNICAL_DISCOVERY`. This is a P04 UX architecture proposal, not an approved design and not a visual specification.
> Working assumptions: `WA-01`–`WA-05` (see `docs/04-ux/README.md`). `G-06` is UNSATISFIED.

This document defines the authenticated provider surfaces `UX-18` through `UX-25`, and the dual-role shell that contains them. It is **not** a layout, not a navigation design, not a component inventory, and not a permissions matrix — authorization is decided in the domain (`ADR-011`), never in a shell, a template, or a client. It does not decide the `NoResponse` window, the `RequestIntake` decay window, or contact-disclosure policy.

The workspace has one job: **let a provider see demand, answer it, and keep its own supply facts true.** Section 7 states what that excludes and why, and section 8 discharges a consequence `ADR-004` assigns to P04 by name.

---

## 1. Surface inventory — `UX-18` to `UX-25`

| ID | Surface | Purpose | Primary action | Information shown | States | Empty / error / degraded |
|---|---|---|---|---|---|---|
| `UX-18` | Provider workspace home and request inbox (`DOC`) | The one place a provider looks to find work that needs an answer. | Open a request. | Delivered requests newest-first with unread state, `Category`, coarse service location, coarse timing, request state; publication state summary; `RequestIntake` per offering; any `Stale` reconfirm prompt. | Per request: `Delivered`, `InDiscussion`, `Declined`, `NoResponse`, `Lapsed`, `Withdrawn`, `Quarantined`, `Closed`. | **Empty:** no requests yet — states plainly that discoverability depends on publication state, `RequestIntake`, and service area, and links to each, **without promising demand**. **Degraded:** if unread cannot be computed, the list renders without unread marks rather than failing. |
| `UX-19` | Request detail and respond (`LOCAL`) | Give the provider everything needed to answer, and nothing else. | Submit a `ProviderResponse` of kind `clarification`, `decline`, or `offer`. | Full `EventContext` including date or window or `flexible`, occasion type, service-location constraint with its timezone, guest-count indication, budget indication where the customer supplied one, category-scoped answers, request free text, prior responses including `Superseded` ones. | Request state; response form state; archetype-gated fields (`structuredFields`, `revisionEnabled`, `expiryEnabled`, `invalidateOnAmendment`). | **Error:** a response submitted against a request no longer in `Delivered` or `InDiscussion` is refused in the domain with the reason shown. **Degraded:** the form is a server-rendered POST; no island is required to answer. |
| `UX-20` | Provider conversation (`LOCAL`) | Asynchronous clarification attached to one request. | Post a `Message`. | One chronological thread: the request and each `ProviderResponse` as anchored, visually distinct entries; `Message`s with author role always shown; `ThreadEvent` system markers **visually distinct and never counted as messages**. | Open; blocked; quarantined; closed-but-readable. | **Blocked:** posting is disabled with a **policy-level reason only** — never who reported, never report contents. **Degraded:** no realtime affordances exist to lose; there is no typing indicator, presence, or read receipt to degrade. |
| `UX-21` | Profile editor (`LOCAL`) | Keep the profile narrative and its presentation true. | Save narrative and `contentLocale`. | Narrative text, content locale, profile-level media ordering, moderation state where a decision affects this content. | `Draft` / published-with-unpublished-edits; moderation state. | **Error:** a save rejected by moderation policy names the policy version, not the reporter. |
| `UX-22` | Offering editor (`LOCAL` + `I-1`) | Keep the facts that drive eligibility true. | Save the offering. | `Category` (governed picker), `OfferingAttributeValue`s derived from `CategoryAttributeDefinition`, `BaseLocation`, `ServiceAreaDeclaration` in an archetype-permitted variant with units as entered, `RequestIntake`. | Per offering: draft edits, saved, publication-relevant unmet items. | **Error:** an unresolvable location input is **rejected and shown**, never approximated. **Degraded:** `I-1` falls back to a plain select over governed values with no keystroke lookup. |
| `UX-23` | Media manager (`LOCAL` + `I-3`) | Add images and reference external audio and video. | Upload an image, or add an external media link. | Per asset: processing state, moderation state, rights acknowledgement state, per-placement alternative text and caption; per provider: count and size caps with remaining allowance shown **before** selection. | Processing; ready; failed; dead external link. | **Dead link:** shown to the provider with the failing reference named; the public profile omits it rather than rendering a broken embed. **Degraded:** `I-3` falls back to a single-file input plus a page reload; processing state still renders. |
| `UX-24` | Publication state and requirements checklist (`DOC`) | Tell the provider exactly where it stands and exactly what to do next. | Submit for publication, reconfirm, deactivate, reactivate. | The live `publicationGateMet` checklist with **why each item is required and what it unlocks**; the current publication state with its reason; the preview entry point; any new-requirement notice. | `Draft`, `PendingReview`, `Published`, `Stale`, `Deactivated`, `Suspended`, `Rejected`, `Closed`. | **Empty:** a brand-new profile shows the full checklist unsatisfied, which is the intended first screen. **Error:** a submit against an unmet gate names the specific unmet item and links to the surface that satisfies it. |
| `UX-25` | Provider settings and notifications (`DOC`) | Control channels, consent, locale, and see delivery truth. | Save notification preferences; verify or replace a `ContactChannel`. | Channels with per-channel verification state and deliverability state; consent by purpose class (**transactional/service** and **marketing/reactivation**, separably); `uiLocale`; recent `DeliveryAttempt` outcomes. | Channel: verified / unverified; deliverable / degraded. Consent: granted / withdrawn. | **Degraded channel:** shown as **degraded, not unverified** — a delivery failure is never evidence about the party. **Error:** a failed verification is retryable and never changes any request state. |

`UX-19` is **the single highest-risk mobile surface in the product.** Providers are mobile-first in practice — performers working from phones (`DB-06`) — and this is the surface where the marketplace either produces a response or does not. It must be completable at phone width, in one column, with archetype-conditional fields revealed accessibly (announced politely, focus moved to the revealed region's heading, answered fields never reordered).

---

## 2. Requests inbox

### 2.1 What the provider sees

| ID | Request state | Provider sees | Provider may act |
|---|---|---|---|
| `PW-01` | `PendingVerification` | **Nothing.** The request is durable but invisible. | — |
| `PW-02` | `Abandoned`, `VerificationFailed`, `RecipientIneligible` | **Nothing, ever.** These requests are never disclosed in any form, including counts. | — |
| `PW-03` | `Delivered` | The full request. This is the state that means *visible in the provider's in-platform inbox*. | Respond: `clarification`, `decline`, `offer`. |
| `PW-04` | `InDiscussion` | The request plus the full response and message history, including `Superseded` responses. | Respond again; message. |
| `PW-05` | `Declined` | The request and its own decline, including the optional reason it authored. Immutable. | Read only. |
| `PW-06` | `NoResponse` | That the response window elapsed. **Non-punitive, non-terminal, observable.** A late response still moves the request to `InDiscussion`. | Respond late. |
| `PW-07` | `Withdrawn` | That the customer withdrew. **No reason is disclosed and none is requested.** | Read only. |
| `PW-08` | `Lapsed` | That a **definite** event date passed. Flexible-date requests never lapse. | Read only. |
| `PW-09` | `Quarantined` | That the request is no longer actionable, with a policy-level reason only. | Read only. |
| `PW-10` | `Closed` | That the request closed. **`ReportedOutcome` visibility is governed by §5, not by this state.** | Read only. |

### 2.2 What the provider never sees

| ID | Prohibition | Why |
|---|---|---|
| `PW-11` | **Nothing before `Delivered`.** No count, no placeholder, no "a request is being verified". | `PendingVerification` exists precisely so the customer's work is durable **without** being visible. A visible placeholder converts an abandoned verification into a provider-visible event, which is exactly what the state was built to prevent. |
| `PW-12` | **Never an `Abandoned` request** — not its existence, not its content, not an aggregate it could be inferred from. | An abandoned request is a customer who did not complete identity verification. Disclosing it discloses an unverified person's intent. |
| `PW-13` | **Never the customer's other requests** — not to the same provider's other offerings, not to other providers, not grouped by `eventGroupingHint`. | One `ServiceRequest`, one recipient offering. **No recipient sets, no routing, no fan-out, no shared response window, no auto-closure of siblings, no reroute.** The grouping hint is a customer-side presentational aid, not a provider-side view. |
| `PW-14` | **Never customer contact data as an attribute of the request.** It resolves at render time through a recorded disclosure decision. | `ADR-010`. The disclosure limb of the policy is owner-open; the seam makes the surface identical under every candidate answer. |
| `PW-15` | **Never whether the customer read a message, is online, or is typing.** | There is no realtime transport, no presence, no read receipts, and no server-initiated push, by architecture. |

### 2.3 Ordering and unread

| ID | Rule | Basis |
|---|---|---|
| `PW-16` | **Newest-first by delivery time** is the default and only V1 ordering of the inbox. | Canon §5.14. The provider's decision is *what needs an answer now*, and recency is the honest proxy. Any other default encodes a priority judgement the platform has no basis for. |
| `PW-17` | Requests needing an answer (`Delivered`, `InDiscussion`) are distinguishable from terminal ones **without reordering by state**, so a filter never becomes a hidden ranking. | A state-sorted inbox silently buries an old `Delivered` request the provider has not answered. |
| `PW-18` | **Unread is computed server-side and rendered on navigation.** | It is a server-side comparison of last-read markers against thread contents; the client computes nothing and stores nothing authoritative. |
| `PW-19` | **No global polling.** No background unread poll anywhere in the shell. | A 30-second unread poll across 200 sessions is ≈ 6.7 req/s — **comparable to all public traffic at the Growth scenario**, spent on a number. |
| `PW-20` | If a polling refresh is ever added, it is on **the open conversation surface only**, it is stated with its query cost, and it is **not the default**. | Canon §5.16. |
| `PW-21` | Unread state is carried in **text as well as any visual mark**, never by color alone. | Canon §5.11. |

---

## 3. `RequestIntake` control

`RequestIntake` is set **per `ServiceOffering`** — never per `Business` and never per profile — because eligibility is evaluated per offering.

| ID | State | Provider-set fields | What the surface says |
|---|---|---|---|
| `PW-30` | `accepting` | — | Willing to receive requests. **Adjacent, in text: this is not a statement about any date.** |
| `PW-31` | `paused` | Optional **paused-until horizon**; optional **provider-authored reason** | Not receiving requests right now. The horizon, where set, is shown as the provider's own statement of when it expects to resume. |
| `PW-32` | `unconfirmed` | — (system-set) | Reached when a stale `accepting` **decays** after a governed freshness window. Shown with the one-action reconfirmation path. |

| ID | Rule | Why |
|---|---|---|
| `PW-33` | **The provider-authored reason is never operator-derived and never punitive.** The platform never writes it, never suggests a punitive option, and never displays it as a platform judgement. | `ADR-005`. A reason the platform authors is a label the provider did not choose but is judged by. |
| `PW-34` | **`paused` is not date unavailability and must never be worded as either** — not in the control, not in its help text, not on `UX-05`. | `V1 has no availability model.` A pause control that reads as a calendar is the availability model arriving through the copy. |
| `PW-35` | **Decay to `unconfirmed` is preceded by nothing the provider cannot see, and reversed by one action.** The reconfirmation path is identical from `UX-18`, `UX-22`, `UX-24`, or a notification link. | Reconfirmation is the highest-frequency provider maintenance action in the product (`docs/04-ux/provider-onboarding.md` §6). |
| `PW-36` | **`unconfirmed` is surfaced in eligibility, not silently excluded.** The offering still appears, carrying its uncertainty. | `ADR-006` names `RequestIntake` as a **surfaced** eligibility input. |
| `PW-37` | **`unconfirmed` may NOT be a ranking input in V1.** Eligibility surfaces it; **ranking must not demote it.** | This was a defect found in critical review, not an original position. Letting decay lower rank reproduces exactly the failure mode date-filtered search was rejected for: **a system clock silently suppresses supply for a reason the provider never learns, and the cause is undiagnosable from data.** In a cold-start market a stale signal is the better trade. |
| `PW-38` | The decay window length is **`OPEN`** — `POLICY PENDING`. P04 designs the control and the reconfirmation path, not the clock. | Canon §5.18. |

---

## 4. Notification settings and delivery truth

| ID | Rule | Why |
|---|---|---|
| `PW-40` | `UX-25` shows, per `ContactChannel`, three **separately kept** facts: **verified** (control proven), **consented** (permission given for a named purpose class), and **deliverable** (the channel currently works). They are routinely conflated and are never merged here. | `docs/02-architecture/domain-model.md` §1.8. |
| `PW-41` | Consent is recorded per purpose class, minimally **transactional/service** and **marketing/reactivation**, separably withdrawable. **Unsubscribe is not delete**, and withdrawal never erases the prior grant. | Conflating the classes makes reactivation unusable and degrades transactional deliverability. |
| `PW-42` | **`DeliveryAttempt` outcomes are visible to their own party.** A provider sees that a notification to its own channel failed, and the failing channel is marked **degraded, not unverified**. | A channel that fails delivery is a channel fact, not a party fact. |
| `PW-43` | **Delivery failure is never evidence of provider non-response, and is never presented to the counterparty as such.** A failed notification never changes request state and never rolls back a marketplace action. | `docs/02-architecture/domain-model.md` §1.8; `docs/04-ux/design-canon.md` §5.16. `Delivered` means visible in the in-platform inbox — notification success or failure is a separate fact. |
| `PW-44` | Notification bodies carry only the `ADR-010` allowlist: **that an event occurred and its type, the acting party's public display name, a non-guessable link to the authenticated surface, coarse timing.** `UX-25` never offers a setting that would add more. | A notification body **escapes the platform's access control permanently**, so everything in it is out of scope for deletion by construction. |
| `PW-45` | Forbidden in any notification the provider receives or triggers: counterparty contact data, request free text, event address, event date, guest count, budget, offer amounts or terms, any conversation content. | Same. The event address is the highest-harm field in V1. |
| `PW-46` | There is **no push notification and no server-initiated channel.** Notifications are outbound messages on a verified channel; the workspace is reached by following a link and authenticating. | `system-architecture.md` §3. |

---

## 5. Minimal outcome and lead visibility

| ID | Rule |
|---|---|
| `PW-50` | `ReportedOutcome` is **customer-only** and **`self_declared`**. A provider claim would create a conflicting assertion with no adjudication mechanism, so none is collected. |
| `PW-51` | What the provider **may** see on a closed request: that the customer reported an outcome, and its governed value — `proceeding`, `not_proceeding`, or `unknown`. Whether even that is disclosed is a **policy question the workspace must be able to answer either way**; the surface is built so the value is a resolved disclosure, not a stored attribute of the provider's view. |
| `PW-52` | What the provider **must never** see: the customer's optional secondary reason, if that `PROPOSED` extension ships. A reason such as *chose another provider* or *price too high* is a measurement instrument, not feedback to be delivered to the party it names. |
| `PW-53` | **`unknown` is an explicit customer answer. `Unreported` is silence.** They are never collapsed, never rendered with the same words, and never counted together. |
| `PW-54` | **What it must not be called:** conversion, booking, confirmed, won, closed-won, sale, payment, completion, or any pipeline vocabulary. `ReportedOutcome: proceeding` is **neither a booking nor a payment**, and it is never derived from platform activity. |
| `PW-55` | The word **lead** is not an entity and is not a surface name. The entity is `ServiceRequest`; the owner-facing synonym for it is RFQ. A surface called "Leads" imports a CRM model the product does not have. |
| `PW-56` | No aggregate performance figure is shown to the provider that the platform cannot substantiate: no response rate, no win rate, no conversion rate, no "you responded to N% of requests". `ResponsivenessObservation` is computed and retained **internal only**. |

---

## 6. Where transaction surfaces would attach, and do not

`WA-03`: V1 Phase 1 ends at marketplace outcome. `UX-38` (booking, payment, payout) is `FUTURE` and separately owner-approved. The workspace names the attachment point and builds nothing: a future booking would attach to the request plus the **current offer version**, reuse the same conversation, and be the point at which an `Event` aggregate becomes justified. The one carried seam is that an offer has a stable identifier and immutable versions. **No accept button that creates an obligation exists on any surface in this document.**

---

## 7. What this workspace is not

**It is not a CRM.** That is a scope statement with teeth, not a disclaimer.

| ID | Excluded | Why it is excluded |
|---|---|---|
| `PW-60` | **Pipeline stages** | The request lifecycle is a governed state machine owned by the domain. A parallel provider-defined pipeline is a second, unauthoritative state model that will disagree with the first, and the disagreement will reach the customer. |
| `PW-61` | **Custom fields** | Attribute definitions are operator-governed and **never provider-writable**. A provider-defined field is an ungoverned key — the legacy taxonomy failure in miniature, arriving through the workspace instead of the taxonomy. |
| `PW-62` | **Tags** | Same as custom fields, with a friendlier name. Tags become an ungoverned parallel taxonomy that leaks into filtering the moment somebody asks for it. |
| `PW-63` | **Bulk actions** | Every marketplace action here is a communication with a specific person about a specific event. Bulk response is broadcast wearing a provider's face, and it is the exact behaviour `WA-02` exists to prevent on the demand side. |
| `PW-64` | **Report builder or analytics dashboard** | `MarketplaceEvent` is **write-only: no module reads it, no decision depends on it, it is never a source of truth.** A provider-facing report builder would make it read-path infrastructure and would surface numbers V1 cannot substantiate. |
| `PW-65` | **Calendar** | `V1 has no availability model.` **No date, calendar, hold, concurrency, or resource-availability field exists anywhere.** A calendar in the workspace is the availability model shipped as a widget, and customers would be told about it within a week. |

**The admissibility test, applied to every proposed provider surface:**

> **If a surface cannot name the marketplace decision it supports, it does not ship.**

Worked examples: the requests inbox supports *which request do I answer now*. `UX-22` supports *am I eligible for the requests I want*. `UX-24` supports *why am I not discoverable*. `RequestIntake` supports *do I want requests right now*. A tag supports nothing — it supports a workflow the provider might build on top of the product, which is a different product.

---

## 8. Dual-role UX — one `Account`, two contexts

`ADR-004` is **ACCEPTED**. Its stated negative consequence is assigned to P04 by name: *"interface design must make the current role obvious, or a user can be confused about which capacity they are acting in. That is a P04 concern."* **This section is P04's discharge of that consequence.**

| ID | Rule | Why |
|---|---|---|
| `PW-70` | **One `Account`, one session.** There is no `Customer` entity, no `Provider` entity, and no account type. Role is derived from what the `Account` does: acting as a customer means owning a `ServiceRequest`; acting as a provider means holding a `BusinessMembership`. | `ADR-004`. The dual-role case is normal in this market, not an edge case — a venue owner planning a quinceañera, a DJ hiring a photographer. |
| `PW-71` | **The authenticated shell carries two persistent contexts side by side: `Hiring` and `My business`.** Both are visible simultaneously. `Hiring` holds requests, drafts, and messages as a customer. `My business` holds `UX-18`–`UX-25`. | Two persistent contexts make capacity a place, not a setting. A user always knows which one they are in because both are on screen. |
| `PW-72` | **`My business` is present only when a `BusinessMembership` exists.** Its absence is the honest state for an account that has never become a provider. | A membership is a revocable relation, never an owner reference. The context follows the relation. |
| `PW-73` | **There is no mode toggle that changes global state.** No account-wide switch, no "you are now in provider mode", no setting that changes what other surfaces mean. Navigating between contexts is navigation, and it changes nothing about the account. | A global mode is a hidden variable that every other surface must then account for. It also invites exactly the account-type check `ADR-004` exists to prevent. |
| `PW-74` | **Becoming a provider is an additive action inside the same account** — never a second account, never a type flip, never a re-registration. It creates a `Business`, a `BusinessMembership`, and a `ProviderProfile` in `Draft`, and it removes nothing on the `Hiring` side. | Option B (typed accounts) and Option C (a mutable type flag) were both rejected. **A flag that must be flipped is a partition with extra steps**, and the flip becomes a support case. |
| `PW-75` | **Every surface where capacity is ambiguous states it, in text, on the surface.** A conversation states *You are replying as **{Business}*** or *You are writing as yourself*. A request detail states which side of the request the viewer is on. A notification names the acting party by public display name so the recipient knows which capacity produced it. | This is the concrete answer to `ADR-004`'s negative consequence. The ambiguity is real and it is resolved by naming the capacity where the action happens, not in a global indicator the user stops seeing. |
| `PW-76` | **A provider hiring another provider uses the ordinary customer path, with no special case.** Same anonymous-capable discovery, same composer, same one deliberate recipient, same verification rules. The `Hiring` context is not reduced, restyled, or restricted because the account also holds a `BusinessMembership`. | It is the routine case, and any special case for it would be a partition reintroduced through the interface. |
| `PW-77` | **No authorization decision may depend on which context is displayed.** The displayed context is presentation. Every authorization is decided in the domain, per action, against actor, resource, and state. | `ADR-011`, and `ADR-004`'s own security note: **an account-type check is exactly the kind of coarse guard that breaks when the same human legitimately holds two roles.** A context-derived guard is that same check wearing a UI name. |
| `PW-78` | **One consent set, one verified-channel set, one notification preference set, per `Account`** — presented once in settings, not duplicated per context. `UX-15` and `UX-25` must not become two competing notification settings surfaces for the same channels. | One `Account` means one consent record set and one verified-channel set, which is *simpler* to reason about for data-subject requests than two linked identities. Duplicating them in the interface undoes that. |
| `PW-79` | **A blocked pair applies to the `Account`, in both capacities.** A `Block` survives closure of any request and applies to future ones; it is not scoped to a context. | `Block` is a party-pair relationship, user-initiated only. Scoping it to a context would let the same human reach a blocking party from the other side. |

The validation that would confirm this section: **a single `Account` can own a `ServiceRequest` and hold a `BusinessMembership` simultaneously without conflict, and no authorization decision anywhere depends on an account type or on a displayed context.**

---

## 9. Constraints this document is bound by, and gates it does not satisfy

- `G-06` is **UNSATISFIED**. `RequestIntake` in §3 is the deliberate absence of an availability design, pending the owner's answer, under `WA-01` — `DAVID WORKING ASSUMPTION — OWNER VALIDATION PENDING`.
- The **`NoResponse` window** (`PW-06`) and the **`RequestIntake` decay window** (`PW-38`) are `OPEN` — `POLICY PENDING`. `PW-37` holds regardless of what those clocks are set to.
- **`PW-51` is `OPEN`**: whether a provider may see the customer's `ReportedOutcome` value at all is a policy question P04 does not resolve. The surface is designed so it is a resolved disclosure either way.
- The **optional secondary outcome reason** referenced in `PW-52` is a **`PROPOSED` P04 extension to `ADR-003`**, not existing P02. It must not be presented as decided.
- Contact-disclosure policy (`Q-008`, `A-010`, `OR-011`) and the disclosure limb of `Q-026` are **owner-open**. The auditability limb is resolved: **every private-content access is audited and reason-tagged.**
- `ADR-004` is `ACCEPTED` and its consequence is discharged here **in design only**. The discharge is not complete until the validation in §8 is executed against an implementation — and P04 implements nothing.
