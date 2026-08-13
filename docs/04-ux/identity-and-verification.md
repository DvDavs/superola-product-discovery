# Identity and Verification — P04 UX Architecture

> Status: `PROPOSED — OWNER VALIDATION REQUIRED`. Provenance: `TECHNICAL_DISCOVERY`. This is a P04 UX architecture proposal, not an approved design and not a visual specification.
> Working assumptions: `WA-01`–`WA-05` (see `docs/04-ux/README.md`). `G-06` is UNSATISFIED.

This document decides **when** in the customer journey Superola asks for an identity and proves control of a contact channel, what the customer experiences while a request waits for that proof, and what Superola is entitled to claim once it has it. It is not a screen design, not a copy deck, not an authentication-mechanism selection, and not a security architecture — `ADR-017` owns application-owned authentication and is not revisited here. It designs no storage. It does **not** decide the contact-disclosure policy, which is the owner's (`Q-008`, `A-010`, `OR-011`), and it does not resolve the disclosure limb of `Q-026`. It satisfies no owner gate.

The identity **boundary** is already fixed and is not P04's to move: `DB-12` requires that **a request must have a verified reachable customer contact channel before it is delivered to a provider.** What P04 owns is the **timing** — where in the sequence that verification happens.

---

## 1. The compared timings

**Note on the count.** The P04 canon introduces this as four compared timings and then enumerates five. Five are compared here, because five distinct sequences are genuinely available. The discrepancy is a labelling artifact and no option was added or dropped to produce it.

| ID | Timing | What the customer does first | Effect on anonymous public discovery and the acquisition hypothesis | Abandonment risk and where it lands | Abuse and spam exposure | Provider-side request quality | Verdict |
|---|---|---|---|---|---|---|---|
| `VT-01` | **Account before search** | Creates an account before running a single search. Sees no supply, no price signal, and no evidence the marketplace has anything they want. | **Destroys it.** No anonymous public surface exists to be found, linked, or shared. Every public acquisition path terminates at a wall in front of a product the visitor has not seen. | Highest, and **in the worst possible place**: before any value has been delivered and before intent can be read. The abandonment is also **unmeasurable as demand** — a visitor who leaves at a signup wall leaves no record of what they were looking for, so it corrupts the unmet-demand capture that zero-result handling depends on. | Lowest. Every action is attributable from the first interaction. | Highest — but on a request volume so small the number is meaningless. | `REJECTED`. It optimizes the one variable Superola can least afford to optimize. |
| `VT-02` | **Account before viewing a profile** | Searches and browses results anonymously; hits a wall when opening a `ProviderProfile`. | **Destroys the part that matters.** The `ProviderProfile` is the public surface carrying the acquisition hypothesis — it is the differentiated, per-provider content that a public-discovery strategy exists to produce (`R-011`, `DB-13`). Walling it makes every result a dead end and leaves only thin generated index pages public, which is precisely the search-quality debt `R-011` warns about. | High, and it lands **before the customer has seen a single provider** — the moment where interest is highest and evidence is lowest. | Low. | High. | `REJECTED`. It keeps the pages with no acquisition value public and hides the ones with all of it. |
| `VT-03` | **Pre-submit account** — account before composing the request | Searches, browses, opens profiles anonymously; hits a wall on *Request a quote*, before the composer opens. | **Preserves it.** All public discovery stays anonymous. This is a genuine, defensible option and the strongest alternative to the recommendation. | Moderate, and it lands **at the wall, before the composer**. Two consequences follow. The customer has invested **nothing** at that point, so the ask has its **worst** completion odds — it is a cost with no accumulated reason to pay it. And composer abandonment becomes largely unmeasurable, because most customers who would have abandoned in the composer never enter it, which makes `FM-01`'s per-stage instrumentation read a truncated population. | Low. Comparable to `VT-04` in practice: both require a verified channel before delivery, and neither delivers an unverified request. | High, and **identical to `VT-04`** — the delivered request is the same in both, because `DB-12`'s boundary is the same. | `REJECTED as primary.` **Retained as the compared alternative** (`Q-018`, `DB-12`), and it is the option to revisit first if `R-022` abandonment at verification proves worse than composer abandonment. |
| `VT-04` | **Guest-then-verify** — anonymous through composition; contact channel at submit | Searches, browses, opens profiles, and **composes the entire request** anonymously. At submit, supplies a contact channel. The request is stored as `PendingVerification`, durable and invisible to the provider, and is delivered only once control of that channel is proven. | **Preserves it fully**, including the profile. | Moderate, and it lands **at verification**, after the customer has invested composition effort — which is when a verification ask has the **best** completion odds. **The honest cost:** this concentrates `R-022`'s abandonment at a single step, and that step is now the highest-stakes moment in the customer funnel. See §2.2. | Low. Identical to `VT-03`: **no request reaches a provider without a verified channel.** The additional exposure over `VT-03` is confined to `PendingVerification` records that were never delivered — invisible to providers, terminal via `Abandoned`, and a storage and rate-limiting concern rather than a provider-attention concern. | High, and identical to `VT-03`. | **SELECTED (`WA-05`).** |
| `VT-05` | **Only-when-needed** — deliver first, verify later or never | Composes and submits anonymously; the request reaches the provider without a proven channel. | Preserves it, and has the best funnel numbers of any option. | Lowest. | **Highest, and structurally so.** An unreachable or spam request **burns provider attention**, which is the scarcest resource in a cold-start marketplace, and it makes Superola a mechanism for pushing messages at a third party's channel. | **Lowest.** The provider receives requests they may be unable to answer, and cannot tell which. | `REJECTED` — **outside the fixed boundary.** `DB-12` is not a P04 comparison; it is settled. This option is listed because it is the one a friction-minimising instinct proposes, and it is important to record that it was considered and is unavailable. |

---

## 2. Recommendation

> **`VT-04` — guest-then-verify. `WA-05`.** Evidence `PROPOSED`; provenance `TECHNICAL_DISCOVERY`. **This is P04's own recommendation and NOT a David directive** — unlike `WA-01`–`WA-04`, it carries no `DAVID WORKING ASSUMPTION — OWNER VALIDATION PENDING` stamp, because P04 may not convert its own proposal into a decision the owner or David has made.
>
> Public discovery, profile viewing, and request composition are **anonymous**. At submit, the customer supplies a contact channel, the request is durably stored as **`PendingVerification`**, and **delivery to the provider occurs only after control of that channel is proven.**

### 2.1 Why

| ID | Reason | Detail |
|---|---|---|
| `IV-01` | **Public browseability is the acquisition hypothesis** | A login wall in front of discovery does not add friction to the funnel; it removes the funnel. `VT-01` and `VT-02` are rejected on this alone. **The "hidden login wall" is the specific failure mode an adversarial review should attack this design for** — including walls that arrive by accident, such as a *save this provider* affordance that silently requires an account, or an error path that redirects an anonymous visitor to sign-in. Anonymous must mean anonymous on every public surface, on every path into it, and on every error out of it. |
| `IV-02` | **The customer has invested effort by submit time** | Verification asks the customer to do something for a reason they can see: the request they just wrote goes nowhere until the channel is confirmed. That is the moment with the best completion odds, and it is the only point in the journey where the ask has an obvious payoff attached. |
| `IV-03` | **`PendingVerification` already exists, and exists for exactly this** | It is not introduced by this recommendation. `domain-model.md` §5.2 and `DB-12` already define it as **durable but invisible to the provider**, precisely so that the customer's work is not lost on abandonment and so that abandonment becomes measurable. Adopting `VT-04` costs no new state. |
| `IV-04` | **The delivered request is identical under `VT-03` and `VT-04`** | Both satisfy `DB-12`. The provider-side outcome is the same, so the comparison reduces entirely to where abandonment lands and what is measurable — which is a UX question, which is why `Q-018` was assigned to P04. |
| `IV-05` | **Account creation is never required to browse, search, view a profile, or begin a draft** | Stated positively. A `RequestDraft` begins local and anonymous at the first answered composer question, and is promoted to a server `RequestDraft` only when an `Account` exists (`docs/04-ux/request-intake.md` §4). |
| `IV-06` | **One `Account`, not a customer account** | `ADR-004`: an `Account` is opaque, permanent, and **not typed by marketplace role**. The account created at verification is not a "customer account". The same person may later become a provider by an **additive** action inside the same account, never by a second account and never by a type flip. **No sign-up copy, form, or confirmation may name a role**, because the naming would create a type the model deliberately does not have. |

### 2.2 The honest cost

**Verification abandonment (`R-022`) is a real, measured cost, and `VT-04` concentrates it at this step rather than spreading it earlier.** That is a consequence of the recommendation, not an argument against it — `VT-03` does not remove the abandonment, it relocates it to a point where the customer has less reason to complete and where the loss is harder to see.

**Instrument verification abandonment separately from composer abandonment, because the fixes are different.** This is the operative requirement, and conflating the two makes both unfixable:

| ID | Failure | What it looks like | What fixes it |
|---|---|---|---|
| `IA-01` | **Composer abandonment** | The customer stops answering questions and never reaches submit. | Question set, ordering, honest progress, reveal behavior, deleting questions that gate nothing (`docs/04-ux/request-intake.md` §1.1, `IC-02`). |
| `IA-02` | **Verification abandonment** | The customer submits, reaches `PendingVerification`, and never proves the channel. | Channel choice, message deliverability, the wording of the pending surface, resend behavior and its rate limit, the length of the verification window, and the correction path when the channel was mistyped. **None of these is a composer fix.** |

A single blended "request abandonment" number reads as one problem, invites one intervention, and will be spent on the wrong one roughly half the time. `FM-04` in `docs/04-ux/request-intake.md` §3 exists for this reason.

**One structural interaction must be respected when choosing the channel and the mechanism.** `R-040` records that a purely email-based credential path silently cancels `D-05`'s free lever: in-app request state is meant to be authoritative and complete so that **notification failure degrades speed, not correctness** — and that requires users to reach in-app state **without email**. **Superola must retain at least one non-email credential path.** This qualifies `R-022`'s friction-minimising instinct directly: the lowest-friction mechanism concentrates all access on the single channel the architecture already treats as unreliable. Mechanism selection is `ADR-017`'s and is not made here; the constraint on it is recorded here because it is a UX consequence.

---

## 3. The `PendingVerification` surface — `UX-09` and `UX-10`

### 3.1 What the customer sees

```text
submit with one chosen recipient → ServiceRequest PendingVerification (durable, invisible to provider)
  → prove control of the contact channel → Delivered → V1 CONTINUES
  → window elapses or customer abandons → Abandoned → V1 ENDS
  → verification rejected → VerificationFailed → V1 ENDS
  → recipient no longer eligible → RecipientIneligible → V1 ENDS
```

| ID | Requirement | Detail |
|---|---|---|
| `PV-01` | **The request is saved, and the customer is told so plainly** | The first thing on the surface is that the request exists and will not be lost. This is the promise `PendingVerification` was designed to keep, and stating it is the difference between a pause and an apparent failure. |
| `PV-02` | **It is stated equally plainly that the request has *not* reached the provider yet** | No wording may imply delivery, receipt, viewing, or a provider's attention. **"Sent" is a prohibited word on this surface.** Presenting an undelivered request as sent is the single most damaging honesty failure available here, because the customer then waits for a reply that was never possible. |
| `PV-03` | **The channel is shown, with a correction path** | The customer sees the channel they entered and can correct it without re-composing the request. A mistyped address is the most common recoverable failure at this step, and requiring recomposition to fix it converts a typo into an abandonment. |
| `PV-04` | **Resend is available and rate-limited, and the limit is stated before it is hit** | A silent rate limit reads as a broken button. State the wait. |
| `PV-05` | **No realtime affordances** | No polling countdown presented as live status, no "waiting…" animation implying a live connection, no presence. **The architecture has no realtime transport and no server-initiated push** (`system-architecture.md` §3), and an interface implying otherwise is a false claim about the system. State refreshes on navigation or on an explicit user action. |
| `PV-06` | **No money-shaped language, and no provider-side language** | `DB-02`. The surface does not name a price, a deposit, a booking, a hold, or a confirmation. It also does not describe what the provider will do, because nothing has reached them. |
| `PV-07` | **The verification message body is allowlist-bound** | Whatever channel is used, the message may contain only: **that an event occurred and its type, the acting party's public display name, a non-guessable link to the authenticated surface, and coarse timing.** It may **not** contain the request free text, the event address, the event date, the guest count, the budget, the recipient provider's identity as counterparty contact data, or any offer terms (`ADR-010`). A notification body escapes the platform's access control permanently — it lands in shared inboxes and on lock screens and persists beyond any deletion request Superola can honour. |
| `PV-08` | **Notification failure is visible to the customer as the customer's own problem** | If the verification message could not be delivered, the customer is told, on their own surface. It is never presented as the provider's non-response, because the provider has not received anything. |

### 3.2 If the customer abandons

| ID | Behavior |
|---|---|
| `PV-09` | The request stays `PendingVerification` for the governed window. Within that window, returning through the link or signing in resumes it exactly where it was — **the composed request is intact, and nothing needs re-answering.** |
| `PV-10` | On `verificationWindowElapsed` **or** `customerAbandoned`, the request becomes **`Abandoned`**, a terminal state. |
| `PV-11` | **The provider never sees it, never sees that it existed, and never learns a request was almost sent.** No aggregate count, no signal, no "someone was interested" indicator. This is not a courtesy; a near-request is not a marketplace fact. |
| `PV-12` | `Abandoned` is **measured** (`IA-02`, `FM-04`). It is the measurable friction cost `A-014` needs, and it is the only reason the verification-timing question is answerable from data at all. |
| `PV-13` | **The verification-window length is `POLICY PENDING`.** P04 does not set it. It is a live tradeoff between recoverability and stale-request accumulation, and it should be set from `PV-12`'s data rather than guessed. |

### 3.3 If verification fails

| ID | Situation | Behavior |
|---|---|---|
| `PV-14` | `verificationRejected` → **`VerificationFailed`** (terminal) | The customer is told plainly that the channel could not be confirmed, with a path to correct the channel and submit again. **The wording carries no implication of judgement about the person** — this is a channel outcome, not a trust outcome, and Superola has made no assessment of the customer. |
| `PV-15` | The composed content survives | Re-submitting after a failure starts from the existing request content, not from an empty composer. Losing the composition here would defeat the entire reason `PendingVerification` is durable. |
| `PV-16` | `recipientNoLongerEligible` → **`RecipientIneligible`** (terminal) | A distinct outcome that has nothing to do with the customer, and must not read as though it does. The recipient stopped being eligible between submit and verification — the offering was deactivated, `RequestIntake` changed, the profile left `Published`. The customer is told honestly that this provider can no longer receive the request and is returned to results **with their composed content preserved and reusable for a different recipient** (`ADR-003`: reuse requires deliberate selection and fresh confirmation per recipient). |
| `PV-17` | **The reason is never over-disclosed** | `RecipientIneligible` copy must not let the customer distinguish `Suspended` from `Deactivated`. Both present as no longer available. Publishing suspension is a punitive label prohibited before policy validation, and the customer-facing distinction would publish it by inference. |

### 3.4 Resumability, and not creating a second request

| ID | Requirement | Detail |
|---|---|---|
| `PV-18` | **Resume by link** | A non-guessable link to the authenticated surface resumes the pending request. The link is the only navigational payload permitted in the message body (`PV-07`). |
| `PV-19` | **Resume by sign-in** | Once an `Account` exists, the pending request is listed under the customer's requests (`UX-11`) with its true state, and is reachable on any device. |
| `PV-20` | **Same device, before the window elapses** | Returning to the site resumes the pending request without the link. |
| `PV-21` | **A repeat submission must not create a second request** | Binding, and it is an `ADR-003` validation criterion (`R-013`). Re-clicking submit, refreshing the confirmation, following the link twice, resending the verification message, using the browser back button and submitting again, or submitting the same draft to the same recipient again while one is pending — **every one of these resolves to the same `ServiceRequest`.** |
| `PV-22` | **What the customer sees on a repeat submission** | The **existing pending request in its current state**, not a fresh confirmation screen and not a success message for an action that did not occur. A duplicate confirmation is how a customer concludes they sent two requests, and it is how a provider would receive two if the guarantee were only presentational. |
| `PV-23` | **Idempotency is a domain guarantee, not a button state** | Disabling the submit control after a click is a courtesy, not the mechanism. The guarantee lives in the application layer (`ADR-011`), where it holds for a second tab, a retried request, and a client with no JavaScript. |
| `PV-24` | **A second, deliberate request to a *different* provider is a different act** | It is permitted, it creates a second `ServiceRequest`, and it requires deliberate recipient selection and fresh confirmation. **`PV-21` constrains duplicates, never deliberate multiple requests** — which are the customer's normal path under `WA-02` and must not be discouraged, warned against, or rate-limited as if they were abuse. |

---

## 4. `verificationBasis` — what Superola is entitled to claim

> **V1's only honest claim is that control of a stated contact channel was proven at time T.** Nothing more.

| ID | Rule | Detail |
|---|---|---|
| `VB-01` | **Every trust-flavoured fact carries a `verificationBasis`** | Never a bare `verified: true`, and never a `verified` badge with no defined auditable event. `verified` is reserved for a precisely defined auditable event, and V1 has exactly one on the customer side: proven control of a contact channel. |
| `VB-02` | **No government identity verification in V1** | Superola does not check a document, a name, an address, or a legal identity, and no surface may imply that it does. **No "identity verified", no "ID checked", no verified-person badge, no shield or check-mark iconography standing in for a claim the platform cannot support.** |
| `VB-03` | **Three things stay distinct and are never merged into one indicator** | **`verified`** (control was proven), **`consented`** (permission was given), **`deliverable`** (the channel currently works). A single badge collapsing them is wrong three ways: a verified channel may be undeliverable today, and a deliverable channel may carry no marketing consent. |
| `VB-04` | **The claim is time-stamped, and stated as of that time** | Control proven at time T is a statement about T, not a permanent property. A channel changes hands. Where the basis is shown, the time is shown with it. |
| `VB-05` | **A verified-contact snapshot is taken at delivery** | So a later channel change never rewrites what the provider was told (`domain-model.md` §5.2, `ADR-003`). The provider's view of the request is stable. |
| `VB-06` | **The customer's contact channel is never publicly exposed** | It appears on no public surface, in no result slot, and in no notification body. It resolves at delivery or render time through a recorded disclosure decision (`ADR-010`), never as an attribute of the request. |
| `VB-07` | **The provider-side statement is bounded to the same claim** | What the provider may be told is that this request comes from an account whose contact channel was verified at time T. **Not that the customer is who they say they are. Not that the customer is serious, screened, or vetted.** Overstating this on the provider side is the same defect as overstating it publicly, with a more motivated audience. |
| `VB-08` | **Provider-side verification uses the same mechanism and the same honesty rule** | The provider publication gate requires at least one `VerificationFact` proving control of a **provider** contact channel. One verification mechanism serves two consumers — the publication gate and the request-delivery predicate (`A-021`) — and it is not two subsystems. **It licenses no stronger claim on the provider side than on the customer side.** |

---

## 5. What this document does not resolve

| ID | Open item | Owner | Status |
|---|---|---|---|
| `IV-O1` | **`Q-026` — the disclosure limb.** Are users told that operator access to their private content is audited? | Owner + David + counsel | **The auditability limb is answered** (`DAVID_DIRECTIVE`): operator access to customer-private content is policy-governed, least-privilege, auditable, and carries a recorded operational purpose. **The disclosure limb is OPEN.** It is a privacy-notice question rather than an architecture question, and it belongs with the versioned notice-and-consent content, not with this design. |
| `IV-O2` | **Contact-disclosure policy — `Q-008`, `A-010`, `OR-011`** | **The owner**, with counsel for `Q-030` | OPEN, and **P04 does not resolve it.** The in-platform preference is `DAVID_DIRECTIVE`; relay-without-exposing-contact is the owner's own concept (`OWNER_INTERVIEW`); the **combined** policy has never been owner-validated. See §5.1. |
| `IV-O3` | **`Q-018` — verification timing** | Owner/product + privacy/security + P04 | P04 **recommends** `VT-04` and retains `VT-03` as the compared alternative. This is a recommendation, not a resolution. **`WA-05` is `PROPOSED` / `TECHNICAL_DISCOVERY` — P04's own, not a David directive** — and `Q-018` stays OPEN. |
| `IV-O4` | **The verification-window length** | Product, from data | `POLICY PENDING` (`PV-13`). Set it from `PV-12`, not from intuition. |
| `IV-O5` | **The authentication mechanism** | `ADR-017` | Not selected here. The binding UX constraint recorded here is `R-040`: **at least one non-email credential path must exist.** |
| `IV-O6` | **`Q-020` — canonical locale for a profile and for a request conversation** | Owner + P04 + content operations + compliance | Bears directly on this document: consent text and verification messages are **versioned localized content**, the consent-text version must be recorded on the consent record, and Spanish strings run materially longer than English, so no fixed-width control on `UX-09` or `UX-16` may depend on English string length. Recorded as a recommendation elsewhere; **not resolved.** |

### 5.1 Why the `ADR-010` seam makes the UX identical under all three owner answers

Contact data is **never** an attribute of a request, a message, or a notification. It is a separately classified party attribute **resolved at delivery or render time through a recorded disclosure decision**, evaluated per (recipient, channel, field, request state).

Because of that seam, all three candidate owner answers are **the same structure with a different predicate**:

| Owner answer | What changes | What the customer and provider interfaces do |
|---|---|---|
| **Never expose** | The disclosure decision returns deny, permanently. | Unchanged. |
| **Expose after a defined request state** | One predicate is added to the same decision. | Unchanged — a contact field appears where the decision permits it, at the same point in the same surface. |
| **Relay without exposing** | A relay identity per (party, request) becomes the **resolved address** of the same decision. | Unchanged — the same field renders a relay address instead of a direct one. |

**The consequence for P04 is substantive, not stylistic: this document, the composer, and the verification step are correct under every one of the three answers, and the owner's answer can arrive late without a rewrite.** Without the seam, the policy change would touch storage, indexes, templates, logs, and **every already-sent notification, which cannot be recalled** — and `A-010` would become a retroactive blocker on P02 and P04 both.

**The honesty rule that survives every answer:** users type phone numbers into free text regardless of any policy, so request and conversation free text is classified as **possibly containing contact data regardless of policy**. **Superola must not tell owners or users that "in-platform" means contact-protected.** The contact policy is a default and an incentive, not an enforcement. This is the same discipline as the `verified` terminology rule (`VB-01`), applied to a privacy claim — and it is free to honour.

---

## 6. Consent capture at verification

Verification is the point of collection, so it is where notice and consent are captured. **Verification and consent are different things and are captured as different things:** proving control of a channel is not permission to use it for everything.

| ID | Rule | Detail |
|---|---|---|
| `CN-01` | **Consent is per (party, channel, purpose class)** | With **granted-at, source, consent-text version, and withdrawn-at**. The consent-text version is not bookkeeping: *"what did they agree to?"* is answerable **only** if the version was captured, and it is impossible to reconstruct later. |
| `CN-02` | **Two purpose classes minimum, and they are separable** | **transactional / service** — the request you sent got a reply, verification, delivery and failure notices. **marketing / reactivation** — come back, new features, legacy re-engagement. |
| `CN-03` | **They must not be bundled** | A single combined checkbox, or a marketing opt-in presented as a condition of sending the request, is a defect. **Conflating them is the mistake that makes legacy reactivation unusable and turns provider alerts into spam complaints that degrade deliverability for the transactional class too** — the class the marketplace's core loop depends on. |
| `CN-04` | **The transactional class is explained, not buried** | The customer is told, in the surface and not only in a linked document, that Superola will contact them about this request on this channel. It is the reason the channel was collected. |
| `CN-05` | **The marketing class is opt-in and skippable** | Declining it changes nothing about the request, and the interface must not suggest otherwise. |
| `CN-06` | **Unsubscribe is not delete** | Withdrawal is **recorded and effective without deleting the record**. `withdrawn-at` is set; the consent record and its version remain, because the record is the evidence of what was agreed and when it stopped. A surface implying that unsubscribing erases the account or the requests is wrong in both directions — it over-promises to the user and destroys the audit trail. |
| `CN-07` | **Consent and notice text are versioned localized content** | Governed as content with a version, in every supported locale, and the version is stamped on the consent record. This is where `Q-020` (`IV-O6`) touches this document directly. |
| `CN-08` | **Withdrawal is reachable from the account, not only from a message footer** | `UX-15` carries locale, notification, and consent settings. A withdrawal path that exists only inside a delivered message is unreachable for a user whose channel stopped working, which is exactly the user most likely to want it. |

---

## 7. Constraints this design does not lift

`DB-12`'s identity boundary is **fixed and not P04's to move**: no `ServiceRequest` reaches a provider without a verified reachable customer contact channel. `WA-05` is a **timing** recommendation inside that boundary, evidence `PROPOSED`, provenance `TECHNICAL_DISCOVERY`, and it is P04's own rather than a David directive; `Q-018` is the question it stands in for, and it is **OPEN**. The contact-disclosure policy (`Q-008`, `A-010`, `OR-011`) is the **owner's** decision and remains unmade — safely, and only because `ADR-010`'s seam is adopted. `Q-026`'s disclosure limb is **OPEN**. `G-06` is **UNSATISFIED**, and it constrains what may be said to a customer about a provider on every surface this document touches, including the moment a request is submitted to one.

**Nothing in this document is approved, and it satisfies no owner gate.**
