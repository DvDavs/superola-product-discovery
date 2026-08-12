# Provider Onboarding — P04 UX Architecture

> Status: `PROPOSED — OWNER VALIDATION REQUIRED`. Provenance: `TECHNICAL_DISCOVERY`. This is a P04 UX architecture proposal, not an approved design and not a visual specification.
> Working assumptions: `WA-01`–`WA-05` (see `docs/04-ux/README.md`). `G-06` is UNSATISFIED.

This document defines how a provider goes from *no account* to *discoverable supply*, as a sequence of governed states and requirements. It is **not** a visual specification, not a copy deck, not a component inventory, and not a migration design. It does not decide the length of any freshness or decay window, does not decide contact-disclosure policy, and does not design the legacy cohort import — those are named as `OPEN` or owned elsewhere. It describes structure: what is required, when, why, and what the provider and the customer each see at every step.

The single organising idea is a separation the repository has not previously written down in one place: **the requirements to create an `Account` and the requirements to appear in discovery are different sets, collected at different times, for different reasons.** Collapsing them produces either a registration wall that suppresses supply or a publication path that leaks incomplete profiles into the marketplace. Section 3 is the point of this document.

---

## 1. Scope and surfaces

| ID | Surface | Role in onboarding |
|---|---|---|
| `UX-16` | Sign in / sign up | Establishes the `Account` and the first verified `ContactChannel`. |
| `UX-17` | Provider onboarding (progressive) | The wrapper journey; creates the `Business`, the `ProviderProfile`, and the first `ServiceOffering`. |
| `UX-21` | Profile editor | `ProviderNarrative`, `contentLocale`, profile-level `MediaReference` ordering. |
| `UX-22` | Offering editor | `Category`, `OfferingAttributeValue`s, `BaseLocation`, `ServiceAreaDeclaration`, `RequestIntake`. |
| `UX-23` | Media manager | `MediaAsset` upload for images, external link fields for audio and video, `MediaRightsDeclaration`, alternative text. |
| `UX-24` | Publication state and requirements checklist | The live checklist, the preview entry point, the submit/publish action, and the provider-visible publication state with its reason. |
| `UX-36` | Legacy profile claim | `FUTURE` / conditional. Section 9. |

`UX-17` is classified `LOCAL` with islands `I-1` (governed type-ahead picker) and `I-3` (media upload manager). Every step degrades to a server-rendered form post with no JavaScript; the islands buy per-keystroke governed lookup and per-file processing state, nothing else.

---

## 2. The progressive onboarding chain

```text
Account (UX-16)
  → Business (UX-17)
  → Service offering: governed Category (UX-17 entry → UX-22)
  → Category-specific information: governed attribute values (UX-22)
  → Location and service area: BaseLocation + ServiceAreaDeclaration (UX-22)
  → Profile: narrative + contentLocale (UX-21)
  → Media: images uploaded, audio/video linked (UX-23)
  → Verification and publication requirements (UX-24)
  → Preview: the UX-05 public projection rendered from Draft (UX-24 → UX-05)
  → Publish: submit (UX-24)
      → PENDING REVIEW where the Category requires review
      → PUBLISHED where it does not
  → DISCOVERY ELIGIBLE
```

Ordering rules that are load-bearing:

- **`Account` precedes everything**, because `Business`, `ProviderProfile`, and `RequestDraft` are all owned or reached through an authenticated principal. Nothing before `Account` is a provider-side artifact.
- **`Category` precedes category-specific information**, because the question set is derived from `CategoryAttributeDefinition` bound to the Category's `CategoryArchetype`. Before a Category exists there is no question set to render. This is the same progressive mechanism the customer request composer (`UX-07`) uses, applied to supply.
- **The category-attribute step and the location/service-area step are order-independent** and both live in `UX-22`. Neither gates the other. `docs/02-architecture/domain-model.md` §5.1 lists service area first; this document lists attributes first; the difference is presentational and neither order is a requirement.
- **Preview precedes publish and does not require the chain to be complete.** Section 4.
- **Nothing in the chain is a wizard the provider cannot leave.** Every step commits on save; the provider may exit at any point and resume, and the checklist in `UX-24` is the resume surface.

Adding a new `Category` adds **no step and no screen**. A Category is governed data plus governed attribute definitions; `UX-22` renders it. Adding a `CategoryArchetype` is an engineering act (`ADR-007`) and is the only thing that can change this chain.

---

## 3. The separation — account versus discovery

### 3.1 Required to create an `Account`

- Evidence: `PROPOSED`. Provenance: `TECHNICAL_DISCOVERY`.

| ID | Requirement | Entity | Why it is required | What it unlocks |
|---|---|---|---|---|
| `PO-01` | One `ContactChannel` — kind plus normalized value | `ContactChannel` | An `Account` that cannot be reached cannot be notified of a `ServiceRequest`, and an unreachable provider is not supply. It is also the only identifier the platform can later prove anything about. | Sign-in, `NotificationIntent` delivery, and the sole input to `PO-02`. |
| `PO-02` | Proven control of that channel | `VerificationFact`, claim type *control of this contact channel was proven at time T* | It is the **only** identity claim V1 can honestly make. It is a per-channel fact, never a boolean on the `Account` or the `Business`. | The authenticated shell, and the first of the two consumers of the same mechanism — the second is the publication gate (`PO-12`). |

**Nothing else.** Explicitly not required to create an `Account`: legal name, business-registry evidence, government identity document, tax identifier, postal address, payment instrument, `Category`, `Business`, media, or a completed profile. **V1 performs no government identity verification and no business-registry verification, so requiring their artifacts at registration would collect sensitive data the platform cannot use for any decision.**

An `Account` is **not typed** by marketplace role (`ADR-004`). Creating one does not make anybody a provider and does not make anybody a customer. See §8.

### 3.2 Required to appear in discovery — `publicationGateMet`

`publicationGateMet` is the named predicate in `docs/02-architecture/domain-model.md` §5.1. It is **archetype-aware**, and every element below is a distinct reason a profile is not yet discoverable.

| ID | Requirement | Entity | Why it is required | What it unlocks |
|---|---|---|---|---|
| `PO-10` | All governed required attributes complete for each offering's `Category`, **at the current `PublicationRequirement` version** | `OfferingAttributeValue` → `CategoryAttributeDefinition` | Missing attribute values are what produced the legacy taxonomy and saturation failure. Governed null semantics mean **missing is not matching** — an incomplete offering would be filtered incorrectly in both directions. | Correct filtering and correct absence from filters the offering does not satisfy. |
| `PO-11` | At least one `ServiceOffering` with an archetype-valid `ServiceAreaDeclaration` | `ServiceAreaDeclaration` | Discovery matches **offerings, not profiles** (`ADR-006`). A profile with no offering has nothing to be eligible. The variant must be one the offering's archetype permits — a venue cannot declare a travel radius. | `LocationEligibility` evaluation, and the declared-coverage statement on `UX-05`. |
| `PO-12` | At least one `VerificationFact` proving control of a provider contact channel | `VerificationFact` | A published provider that cannot receive a delivered request is a dead end for the customer. Same mechanism as `PO-02`, second consumer. | `ServiceRequest` delivery and the `verificationBasis` shown on `UX-05`. |
| `PO-13` | A `BaseLocation` whose `GeoPoint` coordinate precision is **sufficient for that archetype's eligibility predicate** | `BaseLocation`, `GeoPoint` | This clause is load-bearing and was added in critical review. Without it a mobile performer could be `Published`, declare a travel radius, and be **permanently `undetermined`** in discovery — structurally unmatchable in the modal launch archetype. | Distance-based `LocationEligibility` where the archetype uses it. |
| `PO-14` | Media rights acknowledged for **every** referenced asset | `MediaRightsDeclaration` | Rights attach once, to the asset, and are versioned and append-only. A referenced asset without an acknowledgement is an unacknowledged rights exposure on a public page. | Public rendering of any `MediaReference`. |
| `PO-15` | No blocking moderation state | `ModerationCase`, `ActorRestriction` | Publication must not be reachable around an open blocking decision. Trust gates decide publication **before** any other consideration. | Nothing; it is a negative condition. Its absence is what the gate tests. |
| `PO-16` | Entitlement eligible — **constant `true` in V1**; it is one of the six named inputs to `EligibilityDecision` enumerated in `docs/02-architecture/domain-model.md` §1, whose ownership rests with Discovery (`ADR-006`) | `ProviderEntitlement` (named, constant) | The seam exists so a future entitlement can **narrow** eligibility without rewriting the gate. **Payment must never buy publication, and `EligibilityDecision` must never gain a paid branch** (`ADR-008`). | Nothing in V1. It is recorded so the surface never implies a paid tier exists. |

**Archetype-awareness of `PO-13`, stated concretely:**

| Archetype | What `PO-13` demands | What the provider is asked for |
|---|---|---|
| Fixed venue | A resolvable `Place`; coordinates need only be good enough to render a coarse label and, where the provider chooses, a published address. | The venue address, with the provider choosing separately whether to publish it. |
| Mobile performer | A coordinate precise enough to compute a radius against, **whenever the declaration is radius-based**. A named-market declaration does not need it. | A base address, or a named-market list as a first-class alternative. |
| Mobile professional | As mobile performer, plus any declared setup or delivery constraint. | As mobile performer. |
| Delivery and food | A production base plus a delivery zone as radius, `Place` list, or postal-prefix list, **plus a declared lead time**. A zone expressed only as free text yields `undetermined`. | Zone in a governed form, and lead time as a value — not prose. |
| Transportation and route | Modelled, and **recommended out of the launch cohort** — its route-corridor semantics do not reduce to a containment predicate. | Not exercised at launch. |

**A named-market list is a first-class alternative to a radius for every archetype**, and offering it inside `UX-22` is how a provider whose radius would cross a border, a mountain range, or a metro boundary avoids declaring something false.

### 3.3 What the separation buys

- **Discovery, profile viewing, and request composition stay anonymous for customers** (`WA-05`); registration is not the price of looking. The provider side is symmetric: `Account` creation is cheap so the provider can see the checklist and the preview before deciding to invest in completeness.
- **Every publication requirement is defensible as an answer to a marketplace decision.** `PO-10` answers filtering, `PO-11` and `PO-13` answer eligibility, `PO-12` answers delivery, `PO-14` answers rights, `PO-15` answers trust, `PO-16` answers nothing in V1 and says so. A requirement that answers no decision does not belong in the gate.
- **The two sets never merge.** Raising a publication requirement changes `publicationGateMet`; it never changes what is required to hold an `Account`.

---

## 4. The live requirements checklist

`UX-24` renders the checklist, and the checklist is visible **from the first screen of `UX-17`**, not at the end.

| ID | Rule | Rationale |
|---|---|---|
| `PO-20` | Every checklist item states **why it is required** and **what it unlocks**, in the item, not in a help page. | A requirement whose purpose is invisible reads as a gate the platform imposes for its own benefit. The columns in §3.2 are the content. |
| `PO-21` | The checklist is visible **before** any item is satisfied, from the first screen. | The provider decides whether to invest with the full cost known, rather than discovering requirements one at a time. |
| `PO-22` | Progress is saved at **every** step. Leaving `UX-17` loses nothing. | The chain in §2 has **ten** steps — `Account` through `Publish`. `DISCOVERY ELIGIBLE` is the resulting state, not a step, and is the only item in the drawn chain that is not one. Providers are mobile-first in practice (`DB-06`). A session that must be completed in one sitting on a phone is a session that is abandoned. |
| `PO-23` | **Preview is available before every requirement is satisfied.** The preview renders the `UX-05` public projection from the `Draft` and marks itself unmistakably as a preview. | Value before completeness. The provider sees what the effort produces before producing all of it. |
| `PO-24` | The preview shows the **allowlist-derived projection**, not the editor's content. Fields not in the publishable field set are absent from the preview exactly as they will be absent from the published page. | A preview that shows more than the projection teaches the provider a false model of what is public. |
| `PO-25` | **An incomplete profile must never silently become public.** Publication is an explicit provider action against a satisfied gate, never a side effect of editing, of time passing, of an import, or of an operator action other than `approve` or `reinstate`. | This is the same structural rule that makes a `LegacyProviderRecord` incapable of appearing in discovery (`ADR-009`). Publication is never a side effect. |
| `PO-26` | Where the gate is unmet, the checklist names the **specific** unmet item and the surface that satisfies it, and links there. | "Profile incomplete" is not actionable. "Your travel radius needs a base address precise enough to measure from — add it in your offering" is. |
| `PO-27` | Checklist state is derived from `publicationGateMet` at read time, never from a cached completion flag. | A cached flag drifts from the predicate, and the drift direction that matters is the one that lets an unqualified profile publish. |

---

## 5. Publication state as the provider experiences it

The state machine is `docs/02-architecture/domain-model.md` §5.1. This section is that machine expressed as provider experience. It adds no state and removes none.

### 5.1 States

| ID | State | What it means to the provider | Reason shown | Customer-side |
|---|---|---|---|---|
| `PO-30` | `Draft` | Not discoverable. Editable. The checklist names what remains. | The unmet gate items, itemised. | Absent. |
| `PO-31` | `PendingReview` | Submitted, waiting on an operator, because this `Category` requires review. Not discoverable. Still editable is a policy question; V1 treats the submission as frozen until the operator acts. | That the Category requires review, and that no action is needed from the provider. | Absent. |
| `PO-32` | `Published` | Discoverable and eligible to receive a `ServiceRequest`, subject to `RequestIntake` and `LocationEligibility`. | None needed; the state is the good one. | Visible, with `RequestIntake`. |
| `PO-33` | `Stale` | **Still discoverable**, flagged as not recently confirmed. Not a penalty. One action restores it. | That the freshness window elapsed, with the reconfirm action adjacent. | Visible, **with a freshness indicator**. |
| `PO-34` | `Deactivated` | Not discoverable, by the provider's own choice or by stale escalation after a notification was attempted. Reversible. | Which of the two causes applies, in the provider's own words where the provider acted, and plainly where the system acted. | **Not listed.** |
| `PO-35` | `Suspended` | Not discoverable, by operator decision. A `ModerationDecision` exists and carries a reason. | The policy-level reason from the `ModerationDecision`. **Never the reporter's identity and never the report contents.** | **Not listed — and indistinguishable from `Deactivated`.** |
| `PO-36` | `Rejected` | Review declined the submission. Revisable. | The operator's required reason. | Absent. |
| `PO-37` | `Closed` | Terminal. Commercial records survive the closure. | The closing actor and basis. | Absent. |

### 5.2 Transitions

| ID | From → to | Verb | Who acts | What the provider sees |
|---|---|---|---|---|
| `PO-40` | — → `Draft` | `create` | Provider | The checklist, from the first screen. |
| `PO-41` | `Draft` → `Draft` | `edit` | Provider | Saved state and a recomputed checklist. |
| `PO-42` | `Draft` → `PendingReview` | `submit` [`publicationGateMet` AND `categoryRequiresReview`] | Provider | Confirmation that the submission is queued and that review is a property of the Category, not a judgement of this profile. |
| `PO-43` | `Draft` → `Published` | `submit` [`publicationGateMet` AND NOT `categoryRequiresReview`] | Provider | Confirmation, the public link, and the first freshness date. |
| `PO-44` | `PendingReview` → `Published` | `approve` | Operator | Approval, with no operator identity disclosed. |
| `PO-45` | `PendingReview` → `Draft` | `requestChanges` (reason required) | Operator | The required reason, and the specific surface to change. |
| `PO-46` | `PendingReview` → `Rejected` | `reject` (reason required) | Operator | The required reason and the revise path. |
| `PO-47` | `Rejected` → `Draft` | `revise` | Provider | The editor, with the prior reason still visible. |
| `PO-48` | `Published` → `Stale` | `freshnessWindowElapsed` | System | A notification and, on the workspace, the reconfirm action. **Framed as a confirmation request, never as a demotion.** |
| `PO-49` | `Stale` → `Published` | `reconfirm` | Provider | Immediate return to `Published`. Section 6. |
| `PO-50` | `Stale` → `Deactivated` | `staleEscalationElapsed` AND `notificationAttempted` | System | That the escalation happened, that a notification was attempted, and the one-action reactivation path. **The notification attempt is a precondition of the transition, so the provider is never removed silently.** |
| `PO-51` | `Published` → `Deactivated` | `deactivate` | Provider | Confirmation that the profile is no longer listed and that nothing was deleted. |
| `PO-52` | `Deactivated` → `Draft` | `reactivate` [`publicationGateMet` at the **current** requirement version] | Provider | The checklist against the current version, with any newly added requirement named. Reactivation lands in `Draft`, not directly in `Published`. |
| `PO-53` | `Published` / `Stale` / `Deactivated` → `Suspended` | `suspend` (`ModerationDecision` required) | Operator | The policy-level reason and the appeal path, if one exists. **Never who reported and never the report contents.** |
| `PO-54` | `Suspended` → `Published` | `reinstate` (`ModerationDecision` required) | Operator | Reinstatement, recorded as a new decision — the prior decision is never overwritten. |
| `PO-55` | `Published` / `Deactivated` / `Suspended` → `Closed` | `close` | Provider or operator | Confirmation, the retention basis, and what survives closure. |
| `PO-56` | `Rejected` → `Closed` | `abandon` | Provider or system | Confirmation. |

Every operator transition is audited with actor, authority, reason, and policy version. The provider sees **its own true state and reason** at all times; the operator's identity is not part of that.

### 5.3 Customer-side observability

- Customers see **only `Published` and `Stale`**, plus `RequestIntake`. `Stale` carries a freshness indicator.
- `Draft`, `PendingReview`, `Rejected`, and `Closed` are simply absent.
- **`Suspended` and `Deactivated` must be indistinguishable to a customer.** Both present as no longer listed, with identical wording, identical status codes on a direct link, and identical absence from results. Publishing suspension is a punitive label prohibited before policy validation, and a customer-visible difference between the two is that label reconstructed by inference.
- The provider is told which state it is actually in. Asymmetry between the provider view and the customer view is the design, not a leak.

---

## 6. `Stale` → `reconfirm` — the highest-frequency maintenance action

**This is the single most frequent provider maintenance action in the product.** Every published profile passes through it on a governed cycle, forever, whereas onboarding happens once.

| ID | Rule |
|---|---|
| `PO-60` | The `Stale` notification contains a **non-guessable link to the authenticated surface** and nothing else that is not on the `ADR-010` allowlist: that the event occurred and its type, the acting party's public display name, the link, and coarse timing. |
| `PO-61` | Following the link lands on a surface where **reconfirm is one action**. No re-entry into the onboarding chain, no re-validation of unrelated fields, no multi-step confirmation. |
| `PO-62` | Reconfirmation asserts *this profile is still accurate*. It does **not** assert availability on any date and must never be worded as if it does. |
| `PO-63` | Where the profile no longer satisfies the current `PublicationRequirement` version, reconfirm still returns it to `Published` — see §7. The provider is told what changed and asked to update at its own pace. |
| `PO-64` | `Stale` remains **discoverable and flagged**. Reconfirmation restores freshness; it does not restore visibility, because visibility was never removed. |
| `PO-65` | The reconfirmation path is the same one-action path whether reached from the notification, the workspace home (`UX-18`), or `UX-24`. |

The freshness window length and the stale-escalation window length are **`OPEN`** — `POLICY PENDING`, not decided by P04 (`docs/04-ux/design-canon.md` §5.18). The measurement that would set them is the distribution of provider reconfirmation latency against the observed rate of requests reaching providers whose declared facts had changed.

---

## 7. Raising a requirement must not retroactively unpublish

- **Rule:** a `ProviderProfile` records **which `PublicationRequirement` version it satisfied**. Raising the bar does not re-evaluate existing profiles. A profile stays `Published` until it next edits, at which point it is evaluated against the current version.
- **Why:** the alternative is a governance act that silently deletes supply. An operator adding one required attribute to a Category would unpublish every provider in it, with no provider action, no provider notification tied to a provider mistake, and a marketplace that looks broken for a reason nobody can see from the data.
- **What the provider sees instead:** the profile stays `Published`. `UX-24` shows a **non-blocking** notice naming the new requirement, why it was added, what it unlocks, and the surface that satisfies it. The checklist distinguishes *satisfied at version N* from *required at version N+1*.
- **Where the new version does bite:** `reactivate` from `Deactivated` evaluates `publicationGateMet` **at the current requirement version** (`PO-52`), and a `Draft` submitted after the change is evaluated against the current version. Both are explicit provider actions, so neither is silent.
- **What must not happen:** a scheduled sweep that re-evaluates published profiles against a new version and demotes them. That is retroactive unpublication with an extra step.

---

## 8. Media onboarding — `ADR-018`

Media is an input to **no marketplace decision**: not eligibility, not ranking, not the request loop. It is presentation and credibility. The rules below exist because the failure modes are expensive, not because the asset is load-bearing.

| ID | Rule | Basis |
|---|---|---|
| `PO-70` | **Images are uploaded and hosted.** `UX-23` uses island `I-3` for multi-file selection and per-file progress, and degrades to a plain file input plus a page reload. | `ADR-018` Decision rule 1. |
| `PO-71` | **Every uploaded image has a visible processing state.** The provider sees that derivatives are being generated and that the asset is not yet publishable. | `ADR-018` Decision rule 2 — derivatives are pre-generated at upload, so a state exists to show. |
| `PO-72` | **The derivative set is a small, fixed, enumerated set generated at upload. There are no on-the-fly sizes.** A derivative generated on first request has no state to gate publication on, which is why on-the-fly transformation was rejected as architecturally ineligible rather than outbid. | `ADR-018` Option C. |
| `PO-73` | **Audio and video are link/embed fields, never uploaders.** `UX-23` presents a URL field, not a drop target, and the surface never implies an upload path exists for them. | `ADR-018` Decision rule 3. |
| `PO-74` | **Dead external link is a real, provider-visible state.** A swept link that no longer resolves is shown to the provider with the failing reference named, and it raises a case in the **existing staleness queue `OQ-08` (`UX-33`) — one stated ID, and no new operator queue**. The public profile does not render a broken embed. **This is not separately budgeted: `docs/03-technology/cost-model.md` §5.2 contains no dead-link line**, so no document may call it "already budgeted". | `ADR-018` Rationale 4 (*the freshness sweep must exist regardless, because request intake decays on a governed window*) and `ADR-018` Consequences (dead links need a sweep and an operator queue). **That the state is provider-visible with the failing reference named is a `PROPOSED` P04 addition**, not a clause `ADR-018` contains. |
| `PO-75` | **Per-provider count caps and per-file size caps are hard, stated before upload, and enforced server-side.** The cap and the remaining allowance are visible in `UX-23` before the provider selects files. | `ADR-018` Decision; `D-06`. |
| `PO-76` | **All metadata is stripped during the derivative re-encode.** Provider photographs routinely carry precise GPS, and precise provider base location is provider-private and must never reach a projection. The uploaded bytes are never served back. Content type is verified by magic bytes, not by declared type or extension. | `ADR-018` Decision rule 4; `R-048`. |
| `PO-77` | **A `MediaRightsDeclaration` is acknowledged per referenced asset, versioned and append-only**, and covers linked audio and video as well as uploaded images. For mariachis, norteño groups, bands, and DJs the performance may be the provider's but the composition almost never is — the acknowledgement is worded so the provider is answering about the specific asset, not ticking a global term. | `ADR-018` Rationale 3; `PO-14`. |
| `PO-78` | **Alternative text is a property of the `MediaReference` placement, not the asset**, and it is a **publication-quality item**, not an afterthought. The same asset placed on two offerings may carry two different alternative texts. | `docs/02-architecture/domain-model.md` §1.3; `docs/04-ux/design-canon.md` §5.11. |
| `PO-79` | **Publication gates on the database fact that media exists, never on whether the vendor is currently serving it.** *This profile has media* is a database fact; *the delivery network can currently serve it* is a vendor fact. Conflating them means a delivery outage silently unpublishes supply. | `ADR-018` **Decision rule 5**; `R-042`. |
| `PO-80` | Media is required for publication **only** where the Category's `PublicationRequirement` names required media roles. Where it is not required, the checklist may recommend it and must not block on it. | `docs/02-architecture/domain-model.md` §1.2. |

The **exact member list of the fixed derivative set and the exact names of the processing states are `OPEN`.** `ADR-018` fixes that both are small, fixed, and enumerated; it does not enumerate them, and P04 does not invent the enum. What P04 fixes is that the state is provider-visible and that publication depends on it.

The design assumption for the public profile is **~200 KB of served bytes per anonymous view, with 400 KB as the reconsideration threshold.** That is a budget on `UX-05`, and it constrains how many images `UX-23` should encourage above the fold — see `docs/04-ux/provider-profile.md`.

---

## 9. Legacy profile claim — `UX-36`

- Evidence: `FUTURE`. Provenance: `TECHNICAL_DISCOVERY`. Conditional on an approved legacy cohort.

`UX-36` is **conditional and `FUTURE`**. It exists in the surface inventory as a named extension point. **If no cohort is approved, this surface does not ship and the claim queue is empty.**

| ID | Rule | Basis |
|---|---|---|
| `PO-90` | A `LegacyProviderRecord` is **structurally incapable** of appearing in discovery or receiving a `ServiceRequest`. It is not a `ProviderProfile` in a hidden state; it is a different kind of thing. A default-deny flag is one bug away from failing; a record that is not a profile cannot appear in discovery at all. | `ADR-009` Option A. |
| `PO-91` | **A claim binds ownership. It does not publish.** A granted claim seeds a **new `ProviderProfile` in `Draft`**, which then passes the same `publicationGateMet` through the same governed commands as a fresh provider. There is no shortcut, no pre-filled publication, and no second provider model. | `ADR-009`; `domain-model.md` §6. |
| `PO-92` | **Nothing is disclosed before the grant decision** — not the record's contact channel, not a masked or partial contact hint, not a confirmation that a record matching the claimant's input exists. **Even a masked contact hint is an enumeration oracle:** a claimant who can distinguish *a record exists* from *no record exists* can enumerate the cohort. | `ADR-009`; `security-privacy-architecture.md` §4. |
| `PO-93` | Competing claims **never** auto-resolve first-come. While contested: not published, no request delivery, no partial access. | `docs/02-architecture/domain-model.md` §1.9. |
| `PO-94` | Claim adjudication is **one of the two case kinds on `UX-29`, inside the single case queue `OQ-04`** — not a queue of its own — and it is empty unless a cohort is approved. | Canon §5.17; `docs/04-ux/operator-surfaces.md` §2. |

**P04 does not design the migration.** Lawful basis is an owner-plus-counsel fact (`G-09`, `OR-009`); the migration strategy is P05's. This section defines only what the claim surface may and may not show.

---

## 10. Constraints this document adopts, and gates it does not satisfy

- **Every `ADR-` record cited in this document — `ADR-003`, `ADR-004`, `ADR-005`, `ADR-006`, `ADR-007`, `ADR-009`, `ADR-010`, `ADR-018` — is itself `PROPOSED` and unapproved.** This document **adopts** them as constraints; it does not treat them as requirements. `AGENTS.md` forbids promoting a `PROPOSED` record to a requirement without recorded approval.
- `G-06` (what "available" promises a customer) is **UNSATISFIED**. Onboarding proceeds under `WA-01` — `DAVID WORKING ASSUMPTION — OWNER VALIDATION PENDING`. Nothing in this chain collects, implies, or publishes date availability.
- The **freshness window**, the **stale-escalation window**, and the **`RequestIntake` decay window** are `OPEN` — `POLICY PENDING`. P04 designs the paths, not the clocks.
- The **member list of the fixed derivative set** and the **processing-state enum** are `OPEN` in `ADR-018`.
- `ADR-018` is `PROPOSED`, not accepted. Its unresolved highest-priority item — whether image-heavy public pages fall under the chosen storage provider's content-delivery restriction — is a **live exposure on this product's primary discovery surface**, and it must be confirmed before `PO-70` is promised to any provider.
- `ADR-009` is `PROPOSED`; the lawful-basis component of `G-09` is a fact David cannot supply. `UX-36` may not be built against this document alone.
- `publicationGateMet` is defined in `docs/02-architecture/domain-model.md` §5.1 and is **not P04's to change.** If a requirement here contradicts that definition, that definition wins and this document is the defect.

---

*Record dates — phase executed 2026-08-12. No usability or traffic evidence exists; `SRC-006` is NOT RECEIVED.*
