# Response, Conversation, and Outcome — P04 UX Architecture

> Status: `PROPOSED — OWNER VALIDATION REQUIRED`. Provenance: `TECHNICAL_DISCOVERY`. This is a P04 UX architecture proposal, not an approved design and not a visual specification.
> Working assumptions: `WA-01`–`WA-05` (see `docs/04-ux/README.md`). `G-06` is UNSATISFIED.

This document designs the second half of the V1 marketplace loop: sending a `ServiceRequest`, receiving a `ProviderResponse`, conversing about it, and reporting what happened. It is **not** a visual specification, **not** a copy deck, and **not** a booking, payment, or transaction design — `WA-03` ends V1 Phase 1 at marketplace outcome and P04 shows only where a transaction would later attach. It does **not** resolve `G-06`, the `NoResponse` window length, or the contact-disclosure policy (`A-010`, `OR-011`, `Q-008`); the disclosure seam of `ADR-010` means the surfaces below are identical under all three candidate owner answers and only the resolved value changes. No number in this document is a measurement: `SRC-006` is NOT RECEIVED and there is no traffic, usability, or response-behaviour evidence anywhere in this repository.

## 1. Scope and the loop this document owns

```text
UX-07 request composer → UX-08 review and send → UX-09 identity and channel verification (if required)
→ `ServiceRequest` `PendingVerification` (durable, invisible to the provider)
→ contact control proven AND recipient still eligible → `Delivered`
→ UX-10 submitted confirmation → UX-11 my requests → UX-12 request detail: response + conversation
→ `ProviderResponse` (`clarification` | `decline` | `offer`) OR system-observed `NoResponse`
→ UX-14 outcome capture → `Closed` with a `ReportedOutcome`
→ V1 ENDS
```

| Surface | Owner of this document's design | Rendering |
|---|---|---|
| `UX-08` Request review and send | §2 | `DOC` |
| `UX-12` Request detail: response + conversation (customer) | §3–§9 | `LOCAL` |
| `UX-19` Provider request detail and respond | §3–§7 (provider side) | `LOCAL` |
| `UX-20` Provider conversation | §8–§10 | `LOCAL` |
| `UX-35` Notification message | §11 | n/a — channel |
| `UX-14` Outcome capture | §13 | `LOCAL` |

## 2. RFQ review and send — `UX-08`

`UX-08` is the last surface where the customer can still change anything cheaply. After delivery the recipient `ServiceOffering` is immutable and the `EventContext` is append-only through `RequestAmendment` (`domain-model.md` §5.2). The review surface exists because of that immutability, not as a courtesy step.

### 2.1 What the review surface shows

| Block | Content | Rule |
|---|---|---|
| Recipient | The one selected `Business` / `ServiceOffering`, its public display name, its `Category`, its coarse `Place` label, and its current `RequestIntake` state | **Exactly one recipient.** No recipient list, no "also send to similar providers", no pre-checked companion providers (`WA-02`, `ADR-003`) |
| Service and event context | `Category`, occasion type, date or window or `flexible`, service-location constraint at the granularity the customer gave it | Every value is editable in place, returning to the owning composer stage rather than restarting the composer |
| Category-specific answers | The `CATEGORY`-class answers for this archetype only, grouped under a heading naming the archetype requirement they satisfy | A question the customer never saw is never shown as blank here |
| Budget | Shown only if given, as the band the customer chose, with an explicit currency | `QUALITY` class; optional and never required; never rendered as a commitment or as an offer |
| Free-text notes | Shown verbatim with a standing classification notice (§2.2) | Classified as possibly containing contact data regardless of policy (`ADR-010`) |
| Disclosure preview | §2.2 | Mandatory, not collapsible by default |
| Verification requirement | §2.3, shown before the send action, never after it | The customer must know a verification step is coming before they commit to it |
| Primary action | One send action with a single confirmation | Idempotent — a repeat submission must not create a second request (`ADR-003`) |

### 2.2 What will be disclosed, and what will not

This block is a first-class part of the surface, not fine print. It is the customer-facing rendering of `security-privacy-architecture.md` §12 and it is what makes the request honest at the moment of consent.

| Shown to the provider on delivery | Not shown to the provider |
|---|---|
| Your first name or chosen display name | Your full contact details, unless and until the disclosure policy resolves to expose them (`A-010` OPEN) |
| The service you asked for and the occasion | Any of your other requests, to this provider or any other |
| Your date, window, or `flexible` | Your account, your drafts, or your search history |
| The service-location constraint at the granularity you gave | — |
| Your category answers and your notes | — |
| Your budget band, if you gave one | — |

| Never leaves the authenticated surface — for anyone |
|---|
| Your event address — the highest-harm field in V1 |
| Your event date, guest count, budget, or free text in any notification body |
| Any conversation content in any notification body |
| Your contact channel in any notification body, projection, search read path, or analytics event |

**The honesty requirement is load-bearing.** This block must never say that keeping the conversation in Superola protects contact data (`ADR-010`). The permitted statement is that Superola does not put these fields in email, and that anything either party types into free text is theirs to control. Free text defeats the policy and the platform must not claim otherwise.

### 2.3 The verification step

Under `WA-05` the customer reaches `UX-08` anonymously. If no `Account` with a verified `ContactChannel` exists, the send action routes through `UX-09`.

- The request is stored as `PendingVerification` **before** the verification challenge, so verification abandonment never destroys the customer's work (`domain-model.md` §5.2).
- `PendingVerification` is **durable but invisible to the provider.** `UX-08` says so explicitly: the provider has not seen anything yet.
- The only honest V1 claim is `verificationBasis` = control of this contact channel was proven at time T. No government identity verification, no identity claim, no trust badge derived from it.
- Verification abandonment is instrumented separately from composer abandonment (`R-022`), because the fixes are different.

### 2.4 The send confirmation and the expected next step

Confirmation states exactly three facts, in this order: the request was delivered (or is awaiting verification); the provider can now see it in their inbox; where the customer will see any update. `UX-10` then hands off to `UX-11`.

The forward affordances offered at confirmation are: open the request, request another service for this event (seeding a new `RequestDraft` from the same `EventContext` and its `eventGroupingHint`; the V1 request stays single-service), or send a separate request to a different provider. **None of these is a broadcast**, and none of them is presented as a hedge against slow reply, because presenting it that way would imply a reply expectation the platform cannot support.

### 2.5 The hard rule — no response-time promise anywhere

**No surface, notification, badge, or microcopy in this product may state, estimate, imply, or average how long a provider takes to reply.** Three independent reasons, each sufficient on its own:

1. **There is no evidence.** `SRC-006` is NOT RECEIVED. There is no response-time data of any kind, first-party or supplied.
2. **`ResponsivenessObservation` is computed and retained but is internal only** (`docs/02-architecture/domain-model.md` §1.9). Publishing it as a label before policy validation is a punitive or misleading label. **There is no separate "never publicly exposed" register in this repository** — the rule lives in `docs/02-architecture/security-privacy-architecture.md` §12's classification and in `docs/02-architecture/adr/ADR-012`'s allowlist, under which a field the publication policy does not permit cannot reach a public surface at all. Response-rate and response-time badges are excluded there.
3. **The `NoResponse` window is `POLICY PENDING`** (`ADR-003`, `domain-model.md` §5.2). The platform does not yet know its own deadline, so it cannot state a customer-facing one.

| Forbidden wording | Why |
|---|---|
| "Typically replies in 2 hours" · "Usually responds within a day" | Fabricated measurement |
| "Fast responder" · "Highly responsive" · a response-rate percentage | Publishes an internal observation as a public label |
| "You should hear back soon" · "Expect a reply by tomorrow" | A platform promise about a third party's behaviour |
| "If they don't reply in 24 hours, we'll…" | States a window that is `POLICY PENDING` |
| "Awaiting response" rendered as a countdown or a progress bar | Implies a deadline by visual grammar rather than by words |

The honest alternative is **state, not forecast**. The surface describes what has happened and what the customer can do, and says nothing about what will happen.

| Situation | Permitted framing |
|---|---|
| Just delivered | "Delivered. {Business} can see your request now." |
| Waiting, before the governed window elapses | "No reply yet. Superola does not set a reply deadline and does not estimate reply times." |
| After the governed window elapses (`NoResponse`) | "No reply yet." Plus, once and non-repeating: "Providers reply on their own schedule and some do not reply at all. This is not a rating and it is not held against them." |
| What the customer can do | "You can send a separate request to a different provider at any time. You do not need to wait for this one." |
| Where updates appear | "This page always shows the current state. We will email you when something changes." |

## 3. The four user-visible outcomes of a delivered request

Three are provider actions. The fourth is not an action at all.

| Outcome | Kind | What the customer sees | What the provider sees / does | Thread and request effect |
|---|---|---|---|---|
| `clarification` | `ProviderResponse` | An anchored entry marked as a question from the provider, with the question text and the author's business name and role. A reply affordance is the primary action | A response form whose only required field is the question text. No price fields | Request `Delivered` → `InDiscussion`. **Immutable on submission** — a clarification's answer is a `Message`, never a revision (`domain-model.md` §5.3) |
| `decline` | `ProviderResponse` | An anchored entry stating plainly that the provider is not able to take this request, plus the provider's reason **only if the provider wrote one**. Never a system-derived reason, never a rating, never an apology written by Superola on the provider's behalf | An optional free-text reason field, labelled as optional and as visible to the customer, with an explicit note that it is never used to rank, score, or penalize (`ADR-005`) | Request `Delivered` → `Declined` → `Closed` on acknowledgement. **Immutable on submission** |
| `offer` | `ProviderResponse` | An anchored entry marked as an offer, with the response text and, if the provider stated a price, a `PriceStatement` rendered per §5 | A minimum-text offer form. Structured components, revision linkage, and validity appear **only** where the archetype flags enable them (§5.3) | Request `Delivered` → `InDiscussion`. Revisable only under `revisionEnabled` |
| **no response** | **not a `ProviderResponse` — a system-observed request state `NoResponse`** | The waiting state of §2.5, with no counterparty entry in the thread. A `ThreadEvent` marker may record that the governed window elapsed; it is **not** a message and is not attributed to the provider | Nothing. The provider took no action and is asked to take none by this state. The provider is never shown a penalty, a score change, a warning, or a countdown derived from it | Request `Delivered` → `NoResponse`. **Non-terminal**: a late response moves the request to `InDiscussion`. Window length is `POLICY PENDING` |

### 3.1 `NoResponse` is observed, non-punitive, and non-terminal

| Property | Design consequence |
|---|---|
| **System-observed, not authored** | It has no author avatar, no author name, no message bubble, and no position in the message count. It renders as a state on the request, plus at most one `ThreadEvent` marker |
| **Observable** | Both parties can see the request is in this state. Hiding it would leave the customer guessing, which is the failure `R-016` names |
| **Non-punitive** | No badge, no score, no ranking effect, no eligibility effect, no operator case is created by silence alone. `EligibilityDecision`'s six inputs do not include responsiveness (`ADR-006`) and must not gain it |
| **Non-terminal** | The late-response path is live. Nothing in the interface may suggest the request is over, dead, expired, or closed |
| **Window is `POLICY PENDING`** | The window is a governed configuration value. No surface renders it as a countdown, a deadline, or a promise, and P04 explicitly does not choose its length |

**`Q-032` — never present silence as a judgement.** What Superola may conclude from provider silence is an open question owned by owner plus product. Until it is answered, every rendering of `NoResponse` must be readable as *"nothing has happened yet"* and must not be readable as *"this provider ignored you"*, *"this provider is unreliable"*, or *"this provider is unavailable"*. Silence is not availability information (`ADR-005`, `WA-01`), it is not a rating, and it is not evidence about the provider. The one sentence of explanatory copy in §2.5 is the entire permitted treatment; anything longer starts editorializing about the provider.

## 4. A quote is not a booking

`WA-03` ends V1 at marketplace outcome. The response surface must not borrow the grammar of a transaction it does not have.

| Prohibition | What it forbids concretely |
|---|---|
| **No accept button that creates an obligation** | No *Accept*, *Book*, *Confirm booking*, *Reserve*, *Pay deposit*, *Hold this date*, or *Lock in this price* control. Acknowledging a response is a reading act, not a commitment, and its label must say so (§6) |
| **No transaction-protection implication** | No badge, seal, shield icon, guarantee, escrow language, buyer-protection copy, dispute-resolution promise, or refund language anywhere on this surface. Superola holds no money and mediates no transaction in V1 |
| **No money-shaped language in notifications** | A notification body carries no amount, no currency, no term, and no word implying payment (`DB-02`, `ADR-010`) |
| **No status vocabulary borrowed from commerce** | No *pending payment*, *confirmed*, *order*, *invoice*, *checkout*, *cart*, or *receipt*. `Closed` with a `ReportedOutcome` is the V1 terminal state and it means the customer told us what happened, nothing more |
| **`proceeding` is not a booking** | §13 |

The customer's real forward actions on a delivered request are exactly four, and the surface offers exactly these:

| Action | Effect |
|---|---|
| **Reply** | Appends a `Message` to the `Conversation`. A message **can never change request state** (`ADR-003`) |
| **Ask for clarification** | The same affordance from the customer's side; the customer may also amend the request (`RequestAmendment`, §7.2) |
| **Report an outcome** | `UX-14`. Available before the governed prompt trigger fires, not only after it |
| **Withdraw** | Moves the request to `Withdrawn` from `Delivered`, `InDiscussion`, or `NoResponse`. Terminal, honest, and available without giving a reason. Withdrawal is not a report and does not create an operator case |

## 5. `PriceStatement` presentation

A price without its context is a defect, not a shorthand. `PriceStatement` is a value object with six mandatory framing concepts, and V1 renders every one of them or renders none of them (`domain-model.md` §1.6).

### 5.1 Universal rendering rules

| Field | Rendering rule |
|---|---|
| **Currency** | **Always explicit, always adjacent to the amount, never implied by locale or by a bare symbol.** `USD $450` and never `$450` |
| Basis | Rendered as words next to the amount — per event, per hour, per person, per delivery, per leg — never as a suffix the reader must decode |
| Amount or range | A range is a first-class form and is not padded into a false single number. A range renders both endpoints with the same explicit currency |
| Inclusions | Listed, not summarized. An empty inclusions list renders as "not stated", never as blank |
| Exclusions | Listed with the same weight as inclusions. Travel, overtime, equipment, and setup are the exclusions that generate disputes and they are never collapsed into a footnote |
| Conditions | Rendered in the provider's own words. Superola does not paraphrase, normalize, or translate a condition into platform language |
| As-of | The date the provider stated the price. Always shown. A price with no as-of is not renderable |

**No aggregate pricing anywhere.** No "from" price, no average, no price band derived across providers, no comparison table computing a difference. Every price on this surface is one provider's statement about one request, and it is attributed to that provider.

### 5.2 What `PriceStatement` is not

It is not an invoice, not a quote document, not a contract, not a hold, and not a payment instrument. It renders inside the response entry, attributed and dated, and it carries none of the affordances of §4.

### 5.3 Archetype-gated behaviour

Structured fields, revision, and expiry are **flags on the `CategoryArchetype`, not universal product behaviour** (`domain-model.md` §5.3). Where a flag is off, the corresponding interface does not exist — it is not disabled, greyed, or hidden behind an upsell.

| Flag | When on | When off |
|---|---|---|
| `structuredFields` | The offer form exposes governed components — line items, deposit, travel, overtime, per-guest tiers, itinerary legs — and the customer sees them as a labelled breakdown | The offer is minimum text plus an optional `PriceStatement`. No breakdown UI exists |
| `revisionEnabled` | A new offer explicitly revises a named prior offer; the prior offer moves to `Superseded`; the customer is asked to acknowledge the revision | **"Current offer" is simply the most recent offer by recency.** No revision linkage, no acknowledgement machinery, no diff view, no version selector. The interface must not invent an acknowledgement step the domain does not have |
| `expiryEnabled` | The provider may set a valid-until; the customer sees it as a stated condition of the offer; `validUntilPassed` moves the response to `Expired` | **No validity or expiry field is offered and no offer expires.** Validity appears only where the archetype justifies it |
| `invalidateOnAmendment` | A `RequestAmendment` automatically invalidates an outstanding offer, and both parties see why | **An amendment notifies and does not invalidate** (§7.2) |

`Superseded` responses **remain visible to both parties.** Hiding a superseded offer would let a provider rewrite history, and the thread is the only record either party has. A superseded entry renders in place, in its original chronological position, marked as superseded and pointing to the response that replaced it.

## 6. The `ProviderResponse` lifecycle as users experience it

Domain lifecycle: `domain-model.md` §5.3.

| State | Customer view | Provider view | Entered by |
|---|---|---|---|
| `Submitted` | The response is in the thread, unacknowledged. No urgency styling, no countdown | "Sent" with the time. No delivery-receipt claim, no read receipt | Provider submits, request in `Delivered` or `InDiscussion` |
| `Acknowledged` | The customer marked it read or replied. The control is labelled as reading — *Mark as read* or *Got it* — **never** *Accept* | "The customer has seen this." A statement of fact about a marketplace event, not a commitment signal | Customer acknowledges |
| `Acknowledged` → `Submitted` (reopen) | Reopening discussion is ordinary and unremarkable. No warning dialog, no penalty, no state stigma | Same | Either party continues the discussion |
| `Superseded` | Stays visible, in place, marked as superseded, linked to its replacement | Same | A newer offer revises it — **`revisionEnabled` only** |
| `Withdrawn` | Stays visible, marked withdrawn by the provider, with the provider's reason only if the provider wrote one | The provider may withdraw an `offer` only. `clarification` and `decline` are immutable | Provider withdraws |
| `Expired` | Stays visible, marked expired with its stated valid-until | Same | `validUntilPassed` — **`expiryEnabled` and a set valid-until only** |

Two rules that apply across every state: **no response ever disappears from the thread**, and **no state transition is communicated by colour alone** (P04 accessibility baseline) — each carries text.

## 7. Clarification and amendment

### 7.1 A provider question, and the customer's answer

```text
provider submits `ProviderResponse` kind `clarification`
→ request `Delivered` → `InDiscussion`
→ `NotificationIntent` to the customer, body per §11
→ customer opens UX-12 → anchored clarification entry, reply affordance in focus order immediately after it
→ customer replies → `Message` appended to `Conversation`
→ request state UNCHANGED — a message can never move a request
```

The clarification is anchored and visually distinct from ordinary messages because it is a `ProviderResponse`, not a `Message`. The customer's answer is an ordinary `Message`. This asymmetry is real and is rendered rather than smoothed over: the question is a business record, the answer is chat.

### 7.2 `RequestAmendment`

Sometimes the honest answer to a clarification is a changed fact, not a sentence. `RequestAmendment` is the path.

| Property | Design |
|---|---|
| **Append-only** | The original request detail is never overwritten and never disappears. The request detail renders the current values with an explicit *amended* marker and the prior value available in the thread's chronology |
| **Notifies** | An amendment raises a `NotificationIntent` to the provider, with a body carrying only that an amendment occurred (§11) |
| **Does not automatically invalidate an outstanding offer** | Default and universal (`domain-model.md` §1.6, §5.2). The customer is told this plainly at the point of amending: the outstanding offer stands, and the provider decides whether to revise it |
| **Unless `invalidateOnAmendment` is set** | Where the archetype sets the flag, the amendment surface says **before** the customer commits that the outstanding offer will be invalidated, names the offer, and requires confirmation |
| **Never a state change** | An amendment does not move the request through its lifecycle and does not reset any window |

## 8. Conversation — `UX-12` and `UX-20`

The `Conversation` is asynchronous text attached to a request. It is not chat, it is not messaging, and it is not realtime (`system-architecture.md` §3; `domain-model.md` §1.7).

### 8.1 The single-thread presentation decision

P02 places the offer and the original request detail in Demand, and clarifying chat in Conversation, and states explicitly that rendering them as one continuous thread is a **presentation choice, not a boundary change** (`domain-model.md` §5.4, `ADR-003`). **P04 takes that choice.**

One chronological thread, oldest to newest, containing:

| Entry kind | Source | Rendering |
|---|---|---|
| The request itself | Demand — `ServiceRequest` | **Anchored** at the top of the thread, visually distinct, expandable to full `EventContext` and category answers, never scrolled away as an ordinary bubble |
| Each `ProviderResponse` | Demand | **Anchored**, visually distinct, kind-labelled in text (`clarification` / `decline` / `offer`), carrying its `PriceStatement` and its lifecycle state |
| Each `RequestAmendment` | Demand | Anchored, marked as an amendment, naming the changed facts |
| Each `Message` | Conversation | Ordinary thread entry |
| Each `ThreadEvent` | Conversation | System marker — §8.3 |

Why one thread: the customer's mental model is one conversation about one request, and forcing them to reconcile a "quotes" tab against a "messages" tab is a comprehension cost paid for a boundary that is internal. Why anchored and distinct: because the business record and the chat have different retention regimes, different privacy classes, and different immutability rules, and a reader who cannot tell an offer from a sentence cannot reason about either.

### 8.2 Author role is always shown

`ADR-004` allows one `Account` to be both customer and provider, and assigns the resulting interface problem to P04. Every entry therefore states its author's role, not only their name.

- A provider entry reads as the **`Business`**, with the acting person's role stated where relevant — never as a bare personal name.
- A customer entry reads as the customer's chosen display name, marked as the customer on this request.
- The composer states the current capacity above the input, persistently and not on hover: *You are replying as **{Business}*** or *You are writing as yourself*.
- An operator never appears in the thread. Operator action is a `ThreadEvent`, never an authored message (§10).

### 8.3 `ThreadEvent` markers

`ThreadEvent` is a system marker and is **structurally not a `Message`** (`domain-model.md` §1.7). The interface must honour that:

- Visually distinct from every authored entry — no avatar, no bubble, no author attribution.
- **Never counted as a message.** Unread counts, message counts, and "N new" indicators exclude `ThreadEvent` entirely.
- Never moderated as user content and never attributable to a party.
- Kept to the events that change what a party may do: the request was delivered; the governed no-response window elapsed; a response was withdrawn, superseded, or expired; the conversation was restricted, blocked, or closed; an amendment was recorded.

### 8.4 Locale and translation

`contentLocale` is explicit on every `Message` and on every provider narrative (P04 accessibility baseline; `internationalization-architecture.md`). Where content is displayed in a locale other than the one it was authored in, the surface marks the displayed text as machine-translated, names the source locale, and keeps the original one action away. **No surface presents a translation as the provider's own words.** `Q-020` — the canonical locale for a profile and for a request conversation — is a P04-named question; P04's recommendation is that the conversation's canonical locale is the locale the request was composed in, and P04 does **not** claim `Q-020` resolved.

## 9. No realtime, and what replaces it

`system-architecture.md` §3 forbids realtime transport and server-initiated push. This is an architecture constraint, not a P04 preference, and it is not negotiable at this layer.

| Absent by architecture | Consequence for this surface |
|---|---|
| WebSockets, SSE, any server-initiated push | The thread is a form post plus a server-rendered thread |
| Presence / online indicators | Never shown. The absence of a presence dot must not be replaced by a "last seen" derivative |
| Typing indicators | Never shown |
| Read receipts | Never shown. `Acknowledged` is a deliberate marketplace act on a `ProviderResponse` (§6) and is **not** a read receipt on a message |
| Attachments, images, audio, or video in a message | Not in the V1 baseline (`domain-model.md` §1.7). Provider media lives on the profile under `ADR-018` |
| SMS and mobile push | Not a V1 channel |

### 9.1 Unread

Unread state is **computed server-side from a per-participant last-read marker and rendered on navigation** — in the same request that renders the shell, on the same page load. There is no unread endpoint, no background fetch, and **no global polling.**

The arithmetic that forecloses the alternative, from `system-architecture.md` §3: **a 30-second unread poll across 200 concurrent sessions is 200 ÷ 30 ≈ 6.7 requests per second** — comparable to all public traffic at the Growth scenario, generated entirely by clients asking a question whose answer is almost always "nothing changed". At Growth that roughly doubles the read path to serve a badge.

### 9.2 If a refresh is ever added

A refresh may be added **on the open conversation surface only**, and if it is, it ships with its cost stated and it is **not the default**.

| Condition | Requirement |
|---|---|
| Scope | Only while a conversation view is open and focused. Never on the shell, never on the inbox, never on the request list |
| Cost, stated | `N` open-conversation sessions at an interval of `I` seconds is `N ÷ I` requests per second. Illustratively, and this is arithmetic rather than a measurement: 20 open sessions at 60 seconds is ≈ 0.33 req/s; the same 20 sessions at 5 seconds is ≈ 4 req/s — an order of magnitude for an interval change a reader would call cosmetic |
| Query shape | One indexed read of entries after a known sequence number, returning nothing in the common case. It must not re-render the thread, re-run authorization from scratch per entry, or fan out into per-entry queries |
| Enforcement point | The same application layer and the same single machine-access enforcement point as every other read (`ADR-011`, `ADR-012`). **No JSON endpoint built "just in case"** |
| Default | **Off.** Enabling it is a decision with a stated query cost, taken against measured need, not a default a developer flips |
| Degradation | The surface is fully usable with the refresh off. A manual refresh control and page navigation are the baseline, and they are not degraded modes |

## 10. The `Conversation` lifecycle as users experience it

Domain lifecycle: `domain-model.md` §5.4.

| State | Customer and provider view | Composer | Entered by |
|---|---|---|---|
| `Open` | Normal thread, full history, posting enabled | Enabled | Opened lazily on the first message |
| `Restricted` | Thread readable. A neutral notice states that the conversation is under review and that posting is paused. **No reporter identity, no report content, no accusation, no named party** | Disabled | Operator opens a report for review |
| `Blocked` | Thread readable to the extent policy allows. The blocked party sees that posting is disabled and a **policy-level reason only** | Disabled | Either party blocks, or an operator blocks after a confirmed violation |
| `Closed` | Read-only. The request reached a terminal state and a quiet period elapsed. History intact | Disabled | System, on terminal request state plus quiet period |
| `Retained` | Read-only or no longer listed, depending on the retention configuration. A retention clock is running | Disabled | Retention clock starts |
| `Purged` | The thread is gone. The surface says the content was removed under the retention policy and does not reconstruct it. Moderation evidence in an `EvidenceHold` is unaffected and is not shown to either party | n/a | Retention elapsed or a deletion request honoured, absent a `LegalHold` |

**The blocked-party rule, stated exactly.** A blocked party sees: that posting is disabled, and a policy-level reason — the category of policy involved, at the granularity Superola is willing to state publicly. A blocked party **never** sees who reported, whether anyone reported at all, the report's free text, the reporter's identity, the evidence, or the moderation decision's internal reasoning. Distinguishing "blocked by the other party" from "blocked by an operator" is itself an information leak about whether a report exists, and the interface must not make that distinction visible.

## 11. Notification — `UX-35`

A notification is not a message. `NotificationIntent` carries a **reference**, and rendering happens after a disclosure check (`ADR-010`, `security-privacy-architecture.md` §5).

### 11.1 The `ADR-010` body allowlist, in full

| Permitted in a notification body | Forbidden in a notification body |
|---|---|
| That an event occurred, and its type | Counterparty contact data |
| The acting party's **public** display name | Request free text |
| A non-guessable link to the authenticated surface | Event address |
| Coarse timing | Event date |
| | Guest count |
| | Budget |
| | Offer amounts or terms |
| | Any conversation content |
| | Anything identifying a private individual |

### 11.2 Why the allowlist is absolute

**A notification body escapes the platform's access control permanently.** Email lands in shared inboxes and assistants' mailboxes; messages land on lock screens. Both persist beyond any deletion request Superola can honour. **Everything in a notification body is out of scope for deletion by construction** — it is not that deletion is hard, it is that deletion is impossible, and the only control available is not putting the field there in the first place. This is why the allowlist is a construction rule rather than a content guideline, and why no per-user setting, no "include details" preference, and no future template may widen it.

### 11.3 The designed body

Synthetic example. The bracketed values are the only variable content permitted.

```text
Subject:  New activity on your Superola request
Body:     {Business display name} sent a reply about your request.
          Sent {coarse timing — e.g. "this morning"}.
          Open your request: {non-guessable link to the authenticated surface}
          You are receiving this because you sent a request through Superola.
          {consent and preference link, purpose class: transactional / service}
```

| Design rule | Reason |
|---|---|
| The **event type** is named, and nothing else about the event is | Permitted limb of the allowlist |
| The **link is the payload**. Every substantive fact lives behind authentication | The link is the only thing that can carry detail without escaping access control |
| **Coarse timing only** — "this morning", not a timestamp that pins a customer's activity | Precise timing is a behavioural fact about a private individual |
| Response **kind** is not named | Naming `offer` versus `decline` leaks the commercial outcome to anyone holding the inbox. The permitted form is *replied* |
| No amount, no currency, no money-shaped word | `DB-02` and the allowlist, jointly |
| Purpose class is **transactional / service**, separable from marketing / reactivation | `security-privacy-architecture.md` §5. Conflating them degrades transactional deliverability |
| The same allowlist applies to every notification in the product | There is no second, richer template |

### 11.4 Notification failure is visible to its own party only

`DeliveryAttempt` failure is attributable to the attempt and is **never evidence of provider non-response** (`domain-model.md` §1.8).

- A failed delivery is surfaced **to the party whose channel failed**, on their own authenticated surface, with a correction path — update the channel, re-verify, retry.
- It is **never** surfaced to the counterparty, in any form, including indirectly ("we could not reach them").
- It **never** changes request state, and it must never be rendered anywhere near the `NoResponse` state, because a reader who sees the two together will conclude that a delivery problem is a provider behaviour problem. That conclusion is exactly the confusion `R-016` and `Q-032` warn against.
- It creates an operator case in the delivery-failure queue (`OQ-07`, `docs/04-ux/operator-surfaces.md`), which is where the follow-up work lives.

### 11.5 `A-024` — a P04-owned assumption, recorded with its risk

`A-024`: *notification bodies limited to event type, public display name, and a link retain enough utility to prevent silent marketplace failure.* It is OPEN, its decision owner is **P04 plus launch measurement**, and this document is where P04 records its design.

- Evidence: `ASSUMPTION`. Provenance: `TECHNICAL_DISCOVERY`. **P04 designs to `A-024` holding and does not claim it holds.**
- **The tension is real and is not resolved by design.** `R-016` says content-free notifications get ignored; `ADR-010` says content-bearing notifications are an undeletable leak. There is no wording that satisfies both. P04 takes the leak-free side because the failure modes are asymmetric: an ignored notification is recoverable by a second notification or by the customer returning to the site, and an exfiltrated event address is not recoverable by anything.
- **Residual risk, named:** if `A-024` is false, the marketplace fails quietly — providers do not open the link, requests sit in `Delivered`, `NoResponse` rises, and the cause is invisible because the notification looked fine.
- **The measurement that would settle it**, and which does not exist today (`SRC-006` NOT RECEIVED): notification open rate and click-through to the authenticated surface, segmented by party and by event type, against time-to-first-response. If click-through is low while in-app response is high once the surface is reached, the body is the problem. **No copy change may be justified by widening the allowlist** — the permitted levers are subject line, event-type naming, send timing, and channel preference.

## 12. Report and block

| Requirement | Design |
|---|---|
| **Report intake must not require the reported content to still be visible** (`domain-model.md` §1.9) | The report affordance is reachable from the conversation, from the request, and from a standalone entry point that takes a request reference. It works when the content is already hidden, moderated, purged, or deleted. It never says "the content is no longer available, so you cannot report it" |
| **The reporter's free-text note is classified sensitive** | It is never shown to the reported party, never quoted in a notification, never exported to analytics, and never rendered in any surface the reported party can reach. Reporters describe harassment in this field |
| **A report is not a finding** | Submitting a report produces an acknowledgement that a case was opened, and nothing else. No outcome is promised, no timeline is given, and no accusation is communicated to the other party |
| **Evidence is snapshotted** | The report snapshots the specific content it concerns into a narrow, clocked, operator-only, enumerable `EvidenceHold`. This survives deletion of the underlying content and is disclosed truthfully in a data-subject request |
| **Block is available to either party** | Blocking disables posting. The blocked party sees §10's policy-level reason only. Blocking is reversible by the blocking party or by an operator |
| **Report and block are separate acts** | Blocking does not file a report; reporting does not block. Bundling them means either every block generates operator work or every report leaves the reporter exposed |

## 13. Customer outcome — `UX-14`

`ReportedOutcome` is the only V1 evidence that the marketplace produced value. It is also the easiest surface in the product to overclaim on, so its constraints are stated before its design.

### 13.1 The governed triple

| Value | Customer-facing meaning | What it is not |
|---|---|---|
| `proceeding` | The customer is going ahead with this provider, as the customer understands it today | **Not** a booking, **not** a payment, **not** a confirmation, **not** a contract, **not** a conversion |
| `not_proceeding` | The customer is not going ahead with this provider | Not a complaint, not a rating, not a report, and not visible as a mark against the provider |
| `unknown` | **An explicit answer.** The customer was asked and chose to say they do not know or would rather not say | Not silence |

| Property | Rule |
|---|---|
| Basis | `self_declared`, always. **Never derived from platform activity** — not from message count, not from acknowledgement, not from time elapsed, not from a link click (`domain-model.md` §1.6) |
| Who | **Customer-only in V1.** A provider claim would create a conflicting assertion with no adjudication mechanism. The provider sees the reported value and the time it was reported, and nothing else |
| When | Prompted on a **governed trigger**, and available to the customer at any time before it fires, from the request detail |
| Amendable | Append-only. A changed answer is a new record, and the prior answer is retained |
| Never labelled | Conversion, booking, payment, completion, or success (`domain-model.md` §1.6) |

### 13.2 `unknown` is not `Unreported`

This is the single most important distinction on the surface and it is a data-integrity requirement, not a nicety.

| | `ReportedUnknown` | `Unreported` |
|---|---|---|
| What happened | The customer was asked and answered "I don't know" or "I'd rather not say" | The prompt window elapsed with no answer |
| Who acted | The customer | Nobody |
| Rendering | An answered outcome, shown as answered | An unanswered outcome, shown as unanswered |
| In any count | Counted as a response with value `unknown` | Counted as a non-response. **Never counted as `unknown`** |

Collapsing them corrupts every marketplace-health number in the product. The interface consequence: the prompt offers `unknown` as a **visible, equally weighted third option**, never as a dismiss control, never as a close button, and never as the effect of navigating away. Dismissing the prompt leaves the outcome `Unreported`, and the surface says so. Both states remain reportable later — a late report is a first-class path (`domain-model.md` §5.5).

### 13.3 No payment or booking implication

The outcome prompt asks what happened between the customer and the provider. It does not ask about money, it does not offer to record an amount, it does not ask whether a deposit was paid, and it does not congratulate anyone. `proceeding` renders as a plain recorded fact with a plain path to change it.

### 13.4 The secondary reason — a `PROPOSED` P04 extension to `ADR-003`

> **This is a P04 extension, not existing P02.** `ADR-003` records an **optional decline reason** on `ProviderResponse`. It does **not** record an outcome reason on `ReportedOutcome`, and `domain-model.md` §1.6 lists no reason attribute there. The following is `PROPOSED` and requires approval as an amendment to `ADR-003`. Evidence: `PROPOSED`. Provenance: `TECHNICAL_DISCOVERY`. Do not read it as an accepted decision.

**The proposal.** After a customer answers `proceeding` or `not_proceeding`, offer **one optional secondary reason**, from a short governed list plus a skip:

| Reason | Applies to |
|---|---|
| Chose another provider | `not_proceeding` |
| Provider did not respond | `not_proceeding` |
| Price too high | `not_proceeding` |
| Plans changed | `not_proceeding` |
| Still deciding | `not_proceeding` |

| Constraint | Rule |
|---|---|
| **Alongside, never replacing** | The governed triple `proceeding` / `not_proceeding` / `unknown` remains the outcome enum. The reason is a **separate optional attribute**, and it must never be modelled as a wider outcome enum |
| Optional | Skipping is a visible, unpenalized action and leaves the outcome fully valid |
| Governed | A closed operator-governed list, language-neutral identifiers, localized labels. **No free text** — free text here would be customer-private content in a field designed for analytics, which §12 of `security-privacy-architecture.md` forbids |
| Not shown to the provider as a judgement | If it reaches the provider at all, it reaches them as their own aggregate over time, never as a per-request verdict attributed to a named customer. **`provider did not respond` must never render to a provider as an accusation on a specific request** |
| Never punitive | It feeds no ranking, no eligibility input, no badge, and no score. `EligibilityDecision`'s six inputs are closed (`ADR-006`) |

**The argument, by symmetry with the decline reason.** `ADR-003` calls the recorded decline reason *"the free instrument that answers the availability question from Phase-1 data at no cost"* — it converts *"do we need an availability model?"* from a debate into a measurement. The outcome reason is the same instrument pointed at the other side of the loop: it converts *"why did this marketplace fail this customer?"* from a debate into a measurement, at the same near-zero cost, using the same governed-list mechanics that already exist.

**Why this is critical marketplace-learning instrumentation, and not a survey.** Without it, `not_proceeding` is a single undifferentiated number that cannot distinguish five outcomes with five completely different responses:

| Reason | What it actually means | What Superola would do about it |
|---|---|---|
| Chose another provider | **The marketplace worked.** The customer found supply, and it was not this provider | Nothing. This is a healthy outcome and must not be counted as a failure |
| Provider did not respond | **A supply-engagement failure.** Cross-checks directly against the `NoResponse` state and against `A-024` — if `NoResponse` is high and this reason is high, the notification body is a live suspect | Supply activation, `RequestIntake` hygiene, notification design |
| Price too high | **A market-fit signal**, and the only price signal in a product with no transactions | Category and market selection; eventually, expectation-setting in the composer |
| Plans changed | **Demand-side churn.** Nothing about Superola caused it | Nothing. Excluding it from failure counts is the whole point |
| Still deciding | **The prompt fired too early.** A signal about the governed trigger, not about the outcome | Tune the trigger window |

Three of those five are not marketplace failures at all. Without the reason, all five land in the same bucket and the resulting number is unusable for every decision it would be consulted for — including the monetization gate, which depends on evidence of attributable value (`domain-model.md` §5.5).

## 14. Constraints this document is bound by and does not relax

| # | Constraint | Source |
|---|---|---|
| 1 | **Exactly one recipient per request**, immutable after delivery. No fan-out, no routing, no reroute, no shared response window | `ADR-003`, `WA-02` |
| 2 | **No response-time claim, estimate, average, or badge anywhere.** `ResponsivenessObservation` is internal only | `domain-model.md` §1.9; `SRC-006` NOT RECEIVED |
| 3 | **`NoResponse` is system-observed, observable, non-punitive, non-terminal, window `POLICY PENDING`.** Silence is never a judgement | `ADR-003`, `Q-032` |
| 4 | **A quote is not a booking.** No accept obligation, no transaction protection, no money-shaped language in notifications | `WA-03`, `DB-02` |
| 5 | **Explicit currency on every price, always.** No implicit currency, ever | `domain-model.md` §1.6 |
| 6 | **Structured fields, revision, expiry, and amendment invalidation are archetype-gated flags**, not universal behaviour. `Superseded` stays visible to both parties | `domain-model.md` §5.3, `ADR-007` |
| 7 | **A `Message` can never change request state.** A `ThreadEvent` is never a `Message` and is never counted as one | `ADR-003`, `domain-model.md` §1.7 |
| 8 | **No realtime transport, no presence, no typing, no read receipts, no push, no global polling.** Unread is computed server-side and rendered on navigation | `system-architecture.md` §3 |
| 9 | **The `ADR-010` notification body allowlist is absolute.** A notification body is out of scope for deletion by construction | `ADR-010`, `security-privacy-architecture.md` §5 |
| 10 | **Notification failure is visible to its own party only** and is never presented as counterparty non-response | `domain-model.md` §1.8 |
| 11 | **A blocked party sees a policy-level reason only** — never the reporter, never the report content. Report intake never requires the content to still be visible | `domain-model.md` §1.9, §5.4 |
| 12 | **`unknown` is an explicit answer; `Unreported` is silence. They are never collapsed** | `domain-model.md` §5.5 |
| 13 | **Free text is classified as possibly containing contact data regardless of policy, and Superola must never claim that in-platform means contact-protected** | `ADR-010` |

**Gates and open items this document does not close.** `G-06` (`Q-007`) is UNSATISFIED and P04 proceeds on `WA-01` under explicit David authorization. `Q-032` (what may be concluded from provider silence) is OPEN and owned by owner plus product; §3.1 is written so that any answer remains implementable. The `NoResponse` window length is `POLICY PENDING` and P04 does not choose it. `A-010` / `OR-011` / `Q-008` (contact disclosure) are OPEN; every surface here is identical under all three candidate owner answers. `A-024` is OPEN and is owned by P04 plus launch measurement; §11.5 records the design and the residual risk, not a resolution. `Q-020` (canonical locale) is OPEN and §8.4 records a recommendation only. **The secondary outcome reason in §13.4 is a `PROPOSED` extension to `ADR-003` and requires approval before it may be treated as a decision.**
