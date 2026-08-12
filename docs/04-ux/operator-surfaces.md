# Operator Surfaces — P04 UX Architecture

> Status: `PROPOSED — OWNER VALIDATION REQUIRED`. Provenance: `TECHNICAL_DISCOVERY`. This is a P04 UX architecture proposal, not an approved design and not a visual specification.
> Working assumptions: `WA-01`–`WA-05` (see `docs/04-ux/README.md`). `G-06` is UNSATISFIED.

This document designs the operator side of Superola V1 as a **minimum viable, exception-only** set of case queues. It is **not** an admin console, **not** a back-office product, **not** a case-management system, **not** a business-intelligence surface, and **not** a staffing plan. It does not define operator roles beyond the single operator role V1 has, does not define service levels, and does not estimate per-queue operator time — `SRC-006` is NOT RECEIVED and no operator workload in this product has been measured. Every queue below exists because `security-privacy-architecture.md` §9 requires the corresponding action to be **possible without a database console**, since *"a console action has no actor, no reason, and no audit record."* That sentence, not a feature list, is the entire justification for this surface set.

## 1. The operator model

Operator work in V1 is **exception handling**, not operations. There is no routine daily flow; there is a set of queues that are empty when the marketplace is behaving and non-empty when it is not.

| Rule | Statement |
|---|---|
| **Every case records a reason** | Not a status change with an actor stamp — a reason, written or selected at the moment of action, stored with the decision |
| **Every case has states** | An explicit lifecycle, not an implicit "done" flag |
| **Priority only where justified** | A queue gets a priority basis only where a defensible ordering argument exists. Otherwise it is queue age, oldest first, and that is stated rather than dressed up |
| **Every case has an actor and an owner** | The acting `Account` plus the operator role, and a current owner where the case can sit unresolved. Attributing an action to "the system" or to a `Business` instead of to an `Account` destroys accountability and cannot be retro-fitted (`security-privacy-architecture.md` §3) |
| **Every case has an auditable action** | The action produces an `AuditRecord` carrying actor, action type, subject **reference**, outcome, basis reference, timestamp with zone, and channel. **References, never payload copies** |
| **Every reversal is a new decision** | `ModerationDecision` is append-only and is never overwritten. Reversal is recorded, not erased, and the prior state is recoverable |
| **Every private-content access is reason-tagged and audited** | §4.9. This applies across every queue, not only to `OQ-09` |
| **Desktop-first is permitted here, and only here** | Canon §5.12 allows the operator queues to be desktop-first. This is stated as a decision, not assumed. Every customer- and provider-facing surface is evaluated at phone width; these are not |

**Rendering.** All operator surfaces are `DOC` — server-rendered tables with filters — except `UX-27`, which is `LOCAL` because a moderation decision composes a reason, a scope, and an action in one form. No island is allocated to any operator surface — the P04 island budget is three (`I-1`, `I-2`, `I-3`) and all three are customer- or provider-facing (`ADR-020`, P04 recommendation Option A).

## 2. Queue register

**The canonical count, stated once and used everywhere.** `OQ-01` is the cross-queue **index surface** (`UX-26`). Its own case-creation column reads *"Nothing"* — it is a read over the case queues plus assignment — so **an index is not a queue and is not counted as one**. `OQ-02`–`OQ-09` are the **eight case queues** in the P04 operator scope. The register therefore covers **eight case queues plus one index surface, on nine surfaces `UX-26`–`UX-34`**, and `UX-29` carries **two case kinds** (duplicate suspicion and ownership claims) inside the single case queue `OQ-04`. **The count that matters for workload is eight case queues**; "nine" refers to surfaces, never to queues. §6 treats the eight as the workload claim it is.

| ID | Queue | Surface | What creates a case | Reason recorded | States | Priority basis where justified | Actor / owner | Auditable action | What must never be shown |
|---|---|---|---|---|---|---|---|---|---|
| `OQ-01` | Cross-queue index and triage — **an index surface, not a case queue** | `UX-26` | Nothing — it is a read over the eight case queues, plus assignment | Assignment reason, where a case is reassigned | `Unassigned` · `Assigned` · `Aging` (derived) | **None.** Queue age, oldest first, per queue. No cross-queue score | Operator role; assignment sets an owner | Case assignment and reassignment | Any private content. **The index shows references, counts, ages, and states — never request text, conversation text, contact data, or reporter identity** |
| `OQ-02` | Reports and moderation cases | `UX-27` | A `Report` from a participant, or proactive operator detection with no reporter | Policy clause plus operator reasoning on every `ModerationDecision`, with the policy version | `Open` · `UnderReview` · `Decided` · `Reversed` (a new decision) | **Justified**: report kinds implicating personal safety are worked before commercial-policy reports; then case age. **Not** an SLA engine, no timers, no escalation automation | Operator role; one named owner per `ModerationCase` | Hide / restore content · restrict / suspend / reinstate an actor · open, hold, release an `EvidenceHold` · record the decision | Reporter identity or the reporter's free-text note to the reported party. The report content to any party. **The two lifecycles must not be merged** — content state and actor state are separate |
| `OQ-03` | Publication review | `UX-28` | A `ProviderProfile` enters `PendingReview` because its `Category` requires review | Approval or rejection reason, provider-visible, naming the specific requirement | `PendingReview` · `Published` · `Rejected` (with reason) | **None justified.** Queue age, oldest first | Operator role; owner on pick-up | Publish · reject with a provider-visible reason · request changes | Any customer-private content. A reviewer does not need and does not see requests or conversations |
| `OQ-04` | Duplicate suspicion and ownership claims | `UX-29` | A `DuplicateSuspicion` raised at registration or detected later; an `OwnershipClaim` filed | Merge, dismiss, or adjudication basis; for a claim, the evidence considered **by reference** and the decision basis | `Suspected` · `Confirmed` · `Dismissed` · `Merged`; claims: `ClaimOpened` · `ClaimContested` · `Claimed` · `Suppressed` | **Justified for claims**: contested claims first, because a contested claim blocks publication and request delivery for every party to it. Duplicates: queue age | Operator role; a contested claim has a single named adjudicator | Merge or dismiss a duplicate · grant, deny, or escalate a claim · revoke a grant and restore the pre-claim projection | Record contents to an unauthenticated or unverified claimant, **including masked contact hints** — that is an enumeration oracle. Competing claimants' identities to each other |
| `OQ-05` | `CategoryProposal` review | `UX-30` | A provider submits a category that does not exist | Accept-as-new-`Category`, map-to-existing, or reject, each with a stated basis and the taxonomy version | `Submitted` · `UnderReview` · `Accepted` (governed `Category` created) · `Mapped` (to an existing node or synonym) · `Rejected` | **None justified.** Queue age, oldest first | Operator role; taxonomy owner | Create a governed `Category` · add a governed synonym or alias · map · reject with a reason | **The proposal itself, anywhere public.** A `CategoryProposal` is never publicly visible, never searchable, never a taxonomy node, and never a public URL |
| `OQ-06` | Geocode, `undetermined`, and eligibility exceptions | `UX-31` | A provider `BaseLocation` fails to geocode or resolves below the precision its archetype requires; a `LocationEligibility` evaluates to `undetermined`; a `ServiceAreaDeclaration` is archetype-invalid | Correction basis: what was changed, from what, to what, and on what evidence | `Open` · `Corrected` · `ProviderActionRequested` · `AcceptedAsUndetermined` | **None justified.** Queue age, oldest first | Operator role; geography owner | Correct a `Place` binding · re-run geocoding for one record · request provider correction · record `undetermined` as accepted | The precise provider base location in any output that leaves this surface. **`undetermined` must never be coerced to `eligible` or `ineligible` to clear the queue** |
| `OQ-07` | Delivery-attempt failures | `UX-32` | A `DeliveryAttempt` fails, bounces, or is marked a complaint | Follow-up action and its basis; channel disposition | `Failed` · `Retried` · `ChannelMarkedUndeliverable` · `Resolved` · `Abandoned` | **Justified**: failures on a transactional / service intent for a request in `Delivered` or `InDiscussion` before any other class, because that failure breaks the marketplace loop. Marketing / reactivation failures are never worked ahead of it | Operator role | Retry · mark a channel undeliverable · request re-verification from its own party · resolve | The notification body's referenced content. **A failure is never surfaced to the counterparty and is never presented as provider non-response** |
| `OQ-08` | Staleness, `RequestIntake` decay / reactivation, and dead external media links | `UX-33` | A published profile crosses the governed freshness window (`Stale`); `RequestIntake` decays to `unconfirmed`; **the same freshness sweep finds an external audio or video reference that no longer resolves — a dead external media link is a case in this queue and in no other, under the single ID `OQ-08`** | Reactivation basis, or the basis for an operator-side state change where the provider did not act; for a dead link, the failing reference named | `Stale` · `ReconfirmRequested` · `Reconfirmed` · `Deactivated` | **Justified**: age past the freshness window, oldest first — this queue is stock-driven and grows with the cumulative published base, not with new supply | Operator role; provider self-service is the intended resolution path | Send a reconfirm request · record a reconfirmation · deactivate with a reason · record a dead external media reference and tell the provider | Nothing customer-private. This queue reads supply state only |
| `OQ-09` | Audited private-content access | `UX-34` | An operator opens customer-private or provider-private content through the audited read path | **A recorded operational purpose on every access**, selected before the content renders | `Requested` · `Granted` · `Denied` · `Reviewed` | **None.** This is not a work queue. It is an access-control surface plus its review log | The accessing `Account` plus operator role; a reviewer distinct from the accessor where staffing allows | Grant or deny an access request · record the access · review the access log | **The log's own contents to any non-operator.** Whether a given access happened, to the data subject — see §4.9, that is `Q-026`'s **open** disclosure limb |

## 3. What creates no queue

Stated to prevent inflation by omission.

| Not a queue | Why |
|---|---|
| `NoResponse` | Provider silence is observable, non-punitive, and non-terminal. It creates no case and no operator work. Creating a queue from it would make silence a matter for enforcement, which is exactly what `Q-032` leaves open |
| Request withdrawal | A customer withdrawing a request is a normal terminal path |
| `ReportedOutcome` of `not_proceeding` | A marketplace outcome, not an exception |
| A declined `ProviderResponse` | `ADR-003` records the decline reason as **optional and provider-authored** (`ADR-005` relies on it as the availability measurement, but does not record it). That it is **never operator-derived and never punitive** is a `PROPOSED` P04 addition, not an inherited `ADR-003` clause. No operator reads it as a case |
| A profile that is simply `Deactivated` | A provider-initiated state, reversible by the provider |
| Claim adjudication volume, today | **Zero at every modelled scenario, because no legacy cohort is approved** (`G-09` unresolved). `OQ-04`'s claim limb is empty by construction until it is |

## 4. Per-queue design notes

### 4.1 Reports and moderation — `OQ-02` / `UX-27`

The structural requirement that shapes this surface is that **content state and actor state are two lifecycles** (`security-privacy-architecture.md` §9). Conflating them means hiding one message suspends a business, or suspending an actor leaves their content live. The case detail therefore presents two independent action groups — content actions and actor actions — and never a single "resolve" control that does both.

- **Report intake does not require the reported content to still be visible.** The case opens against a subject reference, and the operator reads the `EvidenceHold` snapshot, not the live content. A purge or a deletion between report and review does not break the case.
- **The reporter's free-text note is sensitive** and is visible only inside this surface. It is never quoted into a decision reason that reaches the reported party, never into a notification, never into analytics.
- A `Business`'s participation state is distinct from an `Account`'s. Restricting an `Account` must not orphan a `Business` with independent obligations, and the surface makes the two subjects explicitly separate choices.
- Every action is reversible with the prior state recoverable, and the reversal is itself a new audited `ModerationDecision`.
- **The operator never appears in a `Conversation` as an author.** Operator effects surface to the parties as `ThreadEvent` markers and a policy-level reason only (`docs/04-ux/response-conversation-outcome.md` §10).
- Deliberately absent: automated classification, a service-level engine, an appeals workflow, and case templates. Report intake plus a manual queue is the correct first step at zero measured abuse volume (`security-privacy-architecture.md` §10).

### 4.2 Publication review — `OQ-03` / `UX-28`

Review applies **only where the `Category` requires it** — it is not a universal gate, and making it universal would convert supply acquisition into a queue in a cold-start market.

- The reviewer sees the publication requirements checklist the provider sees (`UX-24`), with each requirement's satisfied state, so approval and rejection are expressed in the provider's own vocabulary.
- A rejection reason is **provider-visible and names the specific requirement**. "Does not meet our standards" is not a reason.
- `publicationGateMet` is archetype-aware and is evaluated by the domain (`ADR-011`). The operator does not override the gate; the operator resolves the judgement items the gate cannot decide — prohibited content, media rights, and narrative quality.
- **Raising a publication requirement must not retroactively unpublish**, so this queue must not fill with previously approved profiles when a requirement version changes.
- The reviewer needs no access to requests or conversations, and the surface does not offer it.

### 4.3 Duplicate suspicion and ownership claims — `OQ-04` / `UX-29`

Two case kinds share one surface because both answer the same question — *which record is the real one, and who controls it* — and both resolve into the same merge-or-adjudicate action shape. **They are two case kinds inside one case queue, `OQ-04`, on one surface, `UX-29`.** They are not two queues, and no count in this repository may treat them as two.

**Duplicates.** Prevention at creation where cheap; detection afterwards is a permanent queue that never empties. Merge is an auditable action with the prior records recoverable.

**Ownership claims — empty unless a cohort is approved.** `G-09` is unresolved, no legacy cohort is approved, and a `LegacyProviderRecord` is structurally incapable of appearing in discovery or receiving a request (`ADR-009`). The claim limb of this queue therefore has **zero cases at every modelled scenario**, and `cost-model.md` §5.2 bills it at zero hours for exactly this reason. If a cohort is ever approved, this becomes the **highest per-case cost of any class** and this surface acquires:

| Requirement, if and only if a cohort is approved |
|---|
| **Disclose nothing before a claim grant — including masked contact hints.** *"We sent a code to j•••@example.com"* is a data leak and an enumeration oracle over the whole imported set |
| Non-disclosing challenge: the claimant answers record facts they were not shown |
| Evidence held **by reference**, never as a raw copy, where it is a third party's data |
| The **record state at grant time**, so post-claim edits are attributable and reversible |
| A post-claim change window on material public fields — legal name, category, service area, contact — held or flagged and reversible |
| **Failed attempts per record and per actor**, which is the only thing that detects claim farming or enumeration |
| Competing claims **never** resolve first-come. Adjudication is a reasoned, auditable operator decision, and while contested there is no publication, no request delivery, and no partial access |

### 4.4 `CategoryProposal` review — `OQ-05` / `UX-30`

The single structural control against the legacy taxonomy failure (`A-009`): **a provider may request a category; the request becomes a `CategoryProposal` in a governed queue and is never a node.** It is never publicly visible, never searchable, and never a public URL until an operator creates a governed `Category`.

- The three real outcomes are **create a governed `Category`**, **map to an existing node or add a governed synonym or alias**, and **reject**. Mapping is the common case and the surface makes it the cheapest action, because most proposals are naming variance rather than missing supply types.
- Adding a `Category` is a **governance act**; adding an `Archetype` is an **engineering act** (`ADR-007`). The surface offers the first and does not offer the second.
- Creating a `Category` requires binding it to an existing `CategoryArchetype` and supplying the governed `CategoryAttributeDefinition` set. `CategoryAttributeDefinition` is operator-governed and **never provider-writable**.
- Deprecation records `merged_into`, so search, public URLs, measurement, and migration survive.
- The synonym and alias table maintained here is the P04 search prerequisite (`search-architecture-requirements.md`), not an afterthought — this queue is where it is actually curated.

### 4.5 Geocode, `undetermined`, and eligibility exceptions — `OQ-06` / `UX-31`

`ADR-019` Level 1: Superola owns canonical `Place` identity, customer-side location resolves against the governed internal `Place` list with **zero vendor calls**, and geocoding applies to provider base addresses and unresolved free text only — **never per search**. This queue is therefore bounded by provider registrations and edits, not by traffic.

- `GeoPoint` carries **precision and provenance**, and both are visible on this surface. A coordinate whose precision is insufficient for its archetype's eligibility predicate is a case, even if it geocoded successfully.
- **`undetermined` must be surfaced, never coerced.** The queue's completion state includes `AcceptedAsUndetermined` precisely so that an operator is never forced to invent an answer to empty a row. An operator who guesses converts an honest uncertainty into a false claim on a customer-facing surface.
- The precise provider base location is **provider-private**. It is readable by the eligibility computation and by this surface; it is never emitted to a projection, a search result, a notification, or analytics.
- Archetype semantics differ and the surface says which one it is evaluating: a mobile archetype resolves within a radius of the base; a fixed venue is the base; transportation's route-corridor semantics **do not reduce to a containment predicate**, which is why P04 recommends transportation out of the launch cohort (`domain-model.md` §4.2).

### 4.6 Delivery-attempt failures — `OQ-07` / `UX-32`

`NotificationIntent` and `DeliveryAttempt` are distinct entities so that a retry cannot duplicate a marketplace effect and so that per-channel failure is visible. This queue works the failures.

- **A failed notification never rolls back the marketplace action.** The request stayed `Delivered`; only the alert failed.
- **Failure is never evidence of provider non-response**, and this surface must not present it that way to anyone. The failure is visible to its own party, on their own authenticated surface, with a correction path. The counterparty is told nothing.
- The operator sees the intent's **reference and the failure metadata** — channel, attempt, outcome, timing. The operator does not see the content the notification concerned. Opening the referenced record is a separate act through the audited read path (`OQ-09`).
- Transactional / service failures and marketing / reactivation failures are separate purpose classes and are worked separately (`security-privacy-architecture.md` §5). Conflating them turns provider alerts into complaints that degrade transactional deliverability.
- **The named reduction lever is product, not staffing**: `cost-model.md` §5.3 identifies making in-app request state authoritative and complete as the free lever against the largest support line — *"did they get my request?"* answered by the product instead of by a person.

### 4.7 Staleness and `RequestIntake` decay / reactivation — `OQ-08` / `UX-33`

This queue is **stock-driven**: it applies to the whole published base, because every published provider's intake state decays on a governed window. It is the one operator line that grows even when acquisition stops.

- `Stale` providers **stay discoverable with a freshness indicator** and are never hidden. Hiding them would silently shrink supply and would make the queue invisible instead of empty.
- `RequestIntake` decaying to `unconfirmed` is **an intake state, not availability** (`ADR-005`). No surface in this queue may word it as date unavailability, and no surface may word it as "not available".
- Customers see only `Published` and `Stale` plus `RequestIntake`. **Customers must not be able to distinguish `Suspended` from `Deactivated`**, and nothing an operator does here may make that distinction observable.
- **The intended resolution is provider self-service, not operator action.** `Stale` → `reconfirm` is a one-action path from a notification link — the highest-frequency provider maintenance action in the product, per the P04 provider-onboarding design. `cost-model.md` §5.3 names this the conversion of an operator hour into a provider's ten seconds. **The operator queue exists for the residue**, and a design that grows this queue is a design failure, not a staffing need.
- **Dead external media links are folded into this queue and create no tenth obligation.** `ADR-018` links audio and video rather than hosting them, and its Rationale 4 argues that the freshness sweep *"must exist regardless"*, so the dead-media check rides on a sweep this queue already runs. It is registered here under the single ID **`OQ-08`**. **It is not separately budgeted: `cost-model.md` §5.2 contains no dead-link line**, and no document may claim otherwise.
- The decay window length is `POLICY PENDING` and P04 explicitly does not choose it.

### 4.8 Audited private-content access — `OQ-09` / `UX-34`

This is the surface that makes every other queue safe, and it is the one operator surface whose purpose is to constrain the operator rather than to enable them.

**The requirement, from `DAVID_DIRECTIVE` (`SRC-014`, recorded 2026-08-11):** operator access to customer and provider private marketplace content must be **policy-governed, least-privilege, auditable, and carry a recorded operational purpose.** Silent unrestricted operator access must not be designed.

| Design requirement | Statement |
|---|---|
| **Reason before content** | The operational purpose is selected from a governed list **before** the content renders, not attached afterwards. A reason captured after the read is a formality; a reason captured before it is a control |
| **Every access is audited** | Each read produces an `AuditRecord`: the accessing `Account` plus operator role, the subject **reference**, the purpose, the outcome, and a timestamp with zone. **References, never payload copies** — an audit record that copies request text becomes a second store of customer-private data with a longer retention than the original |
| **The read of the audit log is itself audited** | `security-privacy-architecture.md` §9 requires it, and it is what makes the log credible rather than decorative |
| **Least privilege by default** | The queues in §2 are designed so that most cases resolve on references, states, and metadata. Opening private content is an **exception inside an exception queue**, not the normal way to work a case |
| **No database console as the operating path** | Every structurally required operator capability must be possible here, because a console action has no actor, no reason, and no audit record. Deferring this surface does not save the month — **it converts the month into an unaudited console habit that cannot be retro-fitted**, because reads that happened during the console period have no record and never will |
| **Audit is not deletable by the domain-deletion path** | Otherwise a deletion request erases the evidence that it was requested |
| **Audit needs a stated retention period** | "Forever" is not a policy; it is unbounded liability that collides with deletion rights |
| **A data subject's answer is derived from audit, not the log itself** | The right to know is served by an answer produced from the audit trail, never by handing over the log |

**`Q-026`, stated precisely and not over-resolved.**

| Limb | Status |
|---|---|
| **Auditability** — must Superola be able to answer *"who at Superola read this customer's request or conversation, and when"* | **RESOLVED.** `DAVID_DIRECTIVE`, `SRC-014`, 2026-08-11. Answer: yes, with a recorded operational purpose per access. `UX-34` is the surface that implements it |
| **Disclosure** — **are users told?** | **OPEN.** The directive says nothing about whether users are informed that their content may be read, or whether a data subject is told about a specific access. **P04 does not infer it and does not resolve it.** It is a privacy-notice question, not an architecture question, and it belongs with the versioned notice-and-consent text `security-privacy-architecture.md` §8 requires. Decision owner: owner plus David plus counsel |

**The design consequence of leaving the disclosure limb open.** `UX-34` records everything either answer would need, and **discloses nothing to a data subject by default**. If the owner later answers *disclose*, the disclosure is built from records that already exist and no retrofit is required. If the answer is *do not disclose*, nothing is removed. Building disclosure now would pre-empt an owner decision; building a surface that cannot support it later would be the retrofit this repository exists to prevent. **P04 takes neither side.**

## 5. Anti-inflation — what this surface set must never become

The operator area is where scope inflation is cheapest to justify and most expensive to carry, because every added surface adds build cost, audit surface, access surface, and permanent maintenance against a marketplace with no users yet.

| Forbidden | Why |
|---|---|
| **A generic admin suite** | A CRUD console over every entity is the opposite of exception-only. It creates an unaudited path to every field, makes least privilege unimplementable, and removes the reason requirement by construction |
| **An entitlement engine** | `EligibilityDecision`'s entitlement input is a **constant `true` in V1** and **must never gain a paid branch** (`ADR-008`; `ADR-006` owns the decision point, `ADR-008` records the no-paid-branch rule). An operator surface for managing entitlements would create the paid branch by the back door |
| **A sponsored-campaign system** | `placementBasis` has one V1 value, `organic`. Sponsored placement is `FUTURE` and, when it ships, is a separately allocated and separately labelled section (`ADR-008`). No V1 operator surface configures placement |
| **A dashboard builder** | Configurable reporting is a product in itself. `MarketplaceEvent` is **write-only** — no module reads it, no decision depends on it, it is never a source of truth. A dashboard builder over it would invent a read path the domain forbids |
| **Vanity metrics** | Totals, growth curves, and leaderboards that change no decision. There is no traffic evidence to populate them (`SRC-006` NOT RECEIVED), and a fabricated chart is worse than an empty one |
| **A case-management platform** | Service-level engines, automated classifiers, escalation trees, appeals workflows, and case templates. `security-privacy-architecture.md` §10 rules all of them out at this scope |
| **A permission matrix** | Two roles, one product, small team. The finding is about **placement** — authorization decided in the domain (`ADR-011`) — which is free. Sophistication buys nothing and costs operability |
| **Support impersonation as a domain concept** | It is an audited operator action, not a model (`domain-model.md` §6) |
| **A JSON endpoint for an operator client** | A JSON endpoint built for a client island is a machine-access surface and is bound by the same single enforcement point, deny by default (`ADR-012`, `R-012`) |

### 5.1 The admissibility test for a new operator surface

A proposed operator surface ships only if **all six** are true. Any one failing is a rejection, not a negotiation.

| # | Test |
|---|---|
| 1 | **It names a specific marketplace exception it resolves.** Not a category of work — a named state the marketplace can actually reach. If the state cannot be reached, the surface has no cases |
| 2 | **The action is structurally required**, per `security-privacy-architecture.md` §9: it must be possible **without a database console**, because a console action has no actor, no reason, and no audit record |
| 3 | **It records a reason, a state, an actor and owner, and an auditable action.** A surface that changes data without recording why is not admissible at any priority |
| 4 | **It does not duplicate a provider or customer self-service path.** If a provider can resolve it themselves, the product fix is the self-service path and the operator queue is the residue — the `Stale` → `reconfirm` pattern (§4.7) |
| 5 | **It does not require private-content access as its normal mode.** Cases resolve on references, states, and metadata; opening private content is an exception with a recorded purpose (§4.8) |
| 6 | **It does not create or configure a policy the owner has not approved** — entitlement, placement, pricing, response deadlines, or disclosure |

## 6. Workload honesty

**Eight case queues is itself a workload claim, and this document makes it explicitly.** Each case queue is a standing commitment of somebody's attention, and P04 is proposing **eight of them, plus one index surface that creates no cases**, for a marketplace that has not launched. The nine surfaces `UX-26`–`UX-34` are the build claim; the eight case queues are the workload claim.

The frame, from `docs/03-technology/cost-model.md` §5.2 — **marketplace-operator case work, modelled at an assumed USD $40/hour** across the three cost scenarios. Every figure below is **an illustrative sensitivity at an assumed USD $40/h against the modelled Pilot / Early Marketplace / Growth volumes; not a measurement, not derived from this queue set, and `SRC-006` is NOT RECEIVED:**

| Scenario | Modelled marketplace-operator hours per month | At `C-R2` |
|---|---|---|
| Pilot | **8.35 h** | USD $334 |
| Early | **55.42 h** | USD $2,217 |
| Growth | **340.58 h** | USD $13,623 — approximately 2.13 full-time equivalents at 160 h/month |

**What these numbers are, stated exactly.** They are a **cost-model estimate built from assumed case rates and assumed handling times against assumed volume drivers**, every one of which `cost-model.md` §5.2 labels as an assumption with no measured basis. They are the honest frame for the size of the claim. They are **not** a measurement of this queue set, and they were not derived from it.

**What is unmeasured, stated plainly:**

- **Per-queue operator time is unmeasured.** `cost-model.md` §5.2 models case *classes* — taxonomy proposals, duplicate resolution, report triage, stale-supply handling, notification-failure follow-up, media moderation, data-subject requests, support — which overlap the eight case queues but do not map one-to-one onto them. **Two of those classes have no P04 queue at all**: customer and provider support (120 h/month at Growth) and data-subject requests (22.50 h/month at Growth) — together **142.5 of the 340.58 Growth hours, roughly 42%**, sit outside this queue set entirely. **This document does not fabricate a per-queue hour figure and no reader should derive one from it.**
- **`SRC-006` is NOT RECEIVED.** There is no traffic data, no usability data, no support-volume data, and no operator-throughput data anywhere in this repository. Nothing here is calibrated against observed behaviour.
- **Claim adjudication is zero at every scenario** because no legacy cohort is approved, and it is the single largest swing factor in the operator table. `G-09` resolving toward a cohort turns `OQ-04`'s claim limb from zero into the most expensive operator hour in the product, budgeted at 30–45 minutes per contested claim.
- **Two of the largest modelled lines are product problems, not staffing problems** (`cost-model.md` §5.3): customer and provider support at 120 h/month at Growth, reducible by making in-app request state authoritative and complete; and stale supply at 83 h/month at Growth, reducible by making freshness confirmation a one-click provider action from a notification. Both are design levers owned by P04, and both are exercised in §4.6 and §4.7.

**The measurement that would replace this frame**, and which does not exist today: cases created per queue per month, operator minutes per case per queue, and the share of cases requiring private-content access — instrumented from the queues themselves from day one, since `ModerationCase` already carries queue age, owner, and escalation as *"the measured basis for deriving tooling later"* (`domain-model.md` §1.9). **Tooling investment beyond this surface set should wait for that measurement.**

## 7. Constraints this document adopts and does not relax

**Status of these sources, stated before the table.** Every `ADR-` record cited below — `ADR-005`, `ADR-006`, `ADR-007`, `ADR-008`, `ADR-009`, `ADR-010`, `ADR-011`, `ADR-012`, `ADR-018`, `ADR-019` — is itself `PROPOSED` and unapproved. This document **adopts** them as constraints; it does not treat them as requirements, and `AGENTS.md` forbids promoting a `PROPOSED` record to a requirement without recorded approval. If a record changes on approval, the rows below change with it.

| # | Constraint | Source |
|---|---|---|
| 1 | **Exception-only.** Every operator capability must be possible **without a database console**, because a console action has no actor, no reason, and no audit record | `security-privacy-architecture.md` §9 |
| 2 | **Every case records reason, state, priority where justified, actor and owner, and an auditable action** | P04 operator scope; `domain-model.md` §1.9 |
| 3 | **Every private-content access is reason-tagged and audited, with a recorded operational purpose captured before the content renders** | `DAVID_DIRECTIVE`, `SRC-014` |
| 4 | **`AuditRecord` holds references, never payload copies**; it is not deletable by the domain-deletion path; it has a stated retention period; and reading it is itself audited | `domain-model.md` §1.10 |
| 5 | **Content state and actor state are two lifecycles** and are never merged into one control | `security-privacy-architecture.md` §9 |
| 6 | **Reporter identity and report content are never shown to the reported party**; a blocked party sees a policy-level reason only | `domain-model.md` §5.4 |
| 7 | **Disclose nothing before a claim grant, including masked contact hints.** Competing claims never resolve first-come | `ADR-009`, `security-privacy-architecture.md` §4 |
| 8 | **A `CategoryProposal` is never a node, never public, never searchable, never a URL.** `CategoryAttributeDefinition` is operator-governed and never provider-writable | `ADR-007` |
| 9 | **`undetermined` is surfaced, never coerced.** Precise provider base location never leaves the eligibility computation and this surface | `ADR-019` |
| 10 | **A notification failure never changes request state and is never presented as counterparty non-response** | `domain-model.md` §1.8 |
| 11 | **`RequestIntake` is an intake state, not availability.** Customers must not be able to distinguish `Suspended` from `Deactivated`. `Stale` stays discoverable | `ADR-005`; `domain-model.md` §5.1 |
| 12 | **Entitlement is a constant `true` and must never gain a paid branch. `placementBasis` has one V1 value, `organic`** | `ADR-006`, `ADR-008` |
| 13 | **Authorization is decided in the domain, and every read path passes the single machine-access enforcement point, deny by default** | `ADR-011`, `ADR-012` |

**Gates and open items this document does not close.** `Q-026`'s **disclosure limb is OPEN** and §4.8 deliberately leaves it open; the auditability limb is resolved by `DAVID_DIRECTIVE` (`SRC-014`). `G-09` and the legacy lawful-basis legal questions are unresolved, so `OQ-04`'s claim limb is empty by construction and its design in §4.3 is conditional. `Q-024` — the retention basis and period for abuse-evidence snapshots and suppression lists — is a legal question with no answer, so `OQ-02`'s `EvidenceHold` clock is parameterizable and unset. The `RequestIntake` decay window and the `NoResponse` window are `POLICY PENDING`. `G-06` is UNSATISFIED. **Per-queue operator hours are unmeasured, `SRC-006` is NOT RECEIVED, and no figure in §6 may be quoted as a measurement of this queue set.**

---

*Record dates — phase executed 2026-08-12. No usability or traffic evidence exists; `SRC-006` is NOT RECEIVED.*
