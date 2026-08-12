# Security & Privacy Architecture Constraints — P02

> **Status:** `PROPOSED`. Provenance: `TECHNICAL_DISCOVERY`.
>
> **Standing qualifier:** `DAVID WORKING ASSUMPTION — OWNER VALIDATION PENDING` (`SRC-013`). **Scenario stamp: S-1.**
>
> **Hard boundaries.** No vendor, product, or technology is selected — no identity provider, key manager, firewall, monitoring product, or payment processor. **Nothing here is a legal opinion**; legal questions are named as legal questions with a decision owner. Controls are **proportional to P01 scope**: a pre-launch two-sided lead-generation marketplace with no payments in Phase 1, a small team, exception-only manual operations, and explicit cost discipline. **Over-specifying controls is a real failure mode here**, so §10 records what was deliberately not recommended.

## 1. The five requirements that carry almost all the value

Everything else in this document is secondary.

1. **Contact data is never an attribute of a request, a message, or a notification.** It is a party attribute resolved at delivery or render time through a **disclosure decision** evaluated per (recipient, channel, field, request state) and recorded. This is what makes the open contact policy a policy decision instead of a rewrite.
2. **The public projection is allowlist-derived and rebuildable.** A field is public because it is explicitly marked publishable; unknown fields are **absent, not passed through**.
3. **Ownership is a revocable relation, and every action records the acting Account plus the Business acted for.** The staff-membership *capability* may be deferred; actor attribution may not.
4. **Authorization is decided in the domain against actor, action, resource, and resource state — never in the delivery channel.**
5. **Legacy records are default-ineligible, and are structurally not provider profiles.** Publication, contact, claim, and request eligibility are separate gates, and request eligibility is last.

## 2. Identity and the authentication boundary

Three separate concepts, three separate identifiers, from day one: `Account` (a login principal), `Business` (a commercial party that outlives the person who created it), and `Person` (a concept only — never a record, because Superola cannot prove human identity in V1).

**What the domain must never assume about the authentication mechanism:**

- That authentication proves identity. It proves control of a channel or credential at a point in time.
- That a session or token carries role or permission. Role comes from a membership lookup.
- That a contact channel is unique, stable, or singular per Account.
- That credential type is fixed.
- **That customer and provider are disjoint populations.** A DJ hiring a photographer is routine in this market. A hard account-type partition makes the dual-role case a migration (`Q-028`, `ADR-004`).

### Two verification threat models, one mechanism

`docs/01-product/feature-inventory.md` treats provider and customer verification as one item. They are different threats.

| | Provider account and contact control | Customer just-in-time (`A-014`) |
|---|---|---|
| Threat | False, duplicate, or impersonating supply enters public discovery and receives real demand | An unreachable or spam request burns provider attention; Superola is used to push messages at a third party's channel |
| Gate point | Before publication **and** before request eligibility | Before request **delivery** — not before browsing |
| Revalidation | On material change, on bounce, on report | Per delivery decision, against a current fact |
| Attaches to | Business publication state | The request delivery predicate |

Proportional structure: **one `VerificationFact` type** — subject, claim type, method class, evidence reference, verified-at, verified-by, revoked-at — with two consumers. Not two subsystems.

**What "verified" may and may not mean (`Q-014`).** `feature-inventory.md` correctly reserves the word for a precisely defined auditable event. The structural consequence it stops short of drawing: **verification is never a boolean on a Business.** It is a set of typed, expiring, revocable facts, and a public label may be rendered only where that fact type has an approved public label. V1's only honest fact is *control of this contact channel was proven at time T*. Not honest in V1: identity verified, business verified, background checked, licensed, verified booking.

Three things routinely conflated and here kept distinct: **verified** (control proven), **consented** (permission given), **deliverable** (the channel currently works).

## 3. Ownership and authorization

**Ownership is a `BusinessMembership` relation** — Account × Business × role × granted-by × granted-at × revoked-at — **not an owner reference on Business.** A reference cannot express transferred, disputed, provisionally granted pending claim review, or revoked; every one of those states is already implied by the claim path. The relation is the cheap version: one relation, one role value in V1.

If V1 ships single-owner businesses only, nothing in the request loop breaks and deferring the *capability* is correct. What breaks if ownership is a reference rather than a relation: ownership transfer, claim approval superseding a provisional grant, and the question **"who could read this customer's request at time T?"** That last one is the real cost — **attributing an action to a Business instead of to an Account destroys accountability, and once messages have been sent under an ambiguous actor it cannot be retro-fitted.**

**Authorization placement.** One domain-owned decision answering: *may this Account, acting for this Business, perform this action on this resource, in this resource state?* It must not live in the delivery channel, for three reasons drawn from the repository:

1. `docs/05-roadmap/mvp-scope.md` mandates operator exception paths — a second caller by design, and exception handling is where authorization is habitually skipped.
2. `docs/07-research/ai-discoverability.md` and `R-013` both anticipate a future authenticated read path. A rule living in a web route is silently absent in the second channel. That is the textbook authorization bypass.
3. Permissions here are **state-dependent** — whether a provider may read a request depends on delivery and request state. Channel guards cannot see state.

Delivery channels enforce authentication and pass the actor through. **Every deny is loggable.** What matters is *placement*, not sophistication: two roles, one product, small team.

## 4. Profile claiming — the highest-risk capability adjacent to V1

Stated without softening: **a claim grant hands an authenticated stranger control of a record containing a third party's business data, plus the inbound demand addressed to that business.** And the imported record's contact data is stale by construction — the owner reports it.

### Abuse cases

| Abuse | Mechanism | Cost to Superola |
|---|---|---|
| Wrong claimant | Control of a stale channel, or no channel proof at all | The wrong party receives requests carrying event date, location, and budget |
| Competitor claim | A weak evidence bar | Demand theft; suppression or redirection; defamation exposure |
| **Stale contact** | A recycled email or phone number. **Channel-control proof is structurally insufficient here** — controlling a recycled channel proves nothing about the business | Silent wrong-party takeover. Hardest to detect, hardest to unwind |
| Deceased or closed business | No liveness signal on the record | Zombie supply; customers misled (`R-016`) |
| Disputed co-ownership | First-wins race | A legitimate party locked out; requires reversal |
| Claim then vandalize | Claim, change name, category, or area, then spam or defame | Public-page trust damage under Superola's own domain |
| Claim farming | One actor claims many records to resell or aggregate | Supply integrity collapse; poisons the whole monetization hypothesis |
| **Claim as reconnaissance** | The claim interface reveals record contents — even masked — before a decision | An enumeration oracle over third parties' contact domains across the whole imported set. **Easy to build by accident:** *"we sent a code to j•••@example.com"* is a data leak |

### Proportional evidence-of-control model

Applies only if a cohort is approved at all.

- **Tier 1 — necessary, never sufficient for a legacy record:** control of a contact channel **already present on the imported record**. Necessary because it is the only thing tying the claimant to the source data. Insufficient because of recycling.
- **Tier 2 — cheap confidence lift:** challenge the claimant with record facts that are **not disclosed to them before they answer**. Challenge, never disclose.
- **Tier 3 — operator exception queue:** mismatch, dispute, or no working channel. A human decides; the decision is recorded.
- **Structural and high-value: a post-claim change window.** Material public changes — legal name, category, service area, contact — on a freshly claimed record are held or flagged and are reversible. This defeats claim-then-vandalize without a moderation team.

**Why claim must be conditional on an audited lawful cohort.** Beyond the policy requirement: **claim is an authorization grant over a record whose lawful basis for existing has not been established.** If the record should not be there, the grant is a second wrong layered on the first — and it is the one that becomes visible to a third party. Therefore a record is claim-eligible only if it carries an affirmative lawful-basis marker **and** an approved-cohort marker. **Default is not-eligible, not eligible-unless-flagged. Default-deny is the control.**

**What the architecture must record:** claim attempt (Account, record, time, channel, outcome); evidence type, channel used, and challenge outcome, **by reference, not raw copy**, where the evidence is a third party's data; decision, decider, time, and basis; **the record state at grant time**, so post-claim edits are attributable and reversible; revocation with restoration of the pre-claim public projection; and **failed attempts per record and per actor** — nothing else detects claim farming or enumeration.

**What V1 must not do:** auto-approve any claim on channel proof alone · let a claim affect the public projection before the grant decision · disclose record contents, including masked contact data, to an unauthenticated or unverified claimant · allow claim outside the approved cohort, including "just for the pilot" · treat a claim grant as identity verification or render any `verified` label from it · **open claim before deletion-on-request and suppression work** (§7) — a claim dispute where the losing party demands erasure is the first hard case and will arrive early · import externally acquired or scraped records into the claimable pool (`R-006`, `AGENTS.md`).

## 5. Contact disclosure and messaging

`A-010` is OPEN: the in-platform preference is `DAVID_DIRECTIVE`, relay-without-exposing-contact is the owner's concept, and the combined policy is **not** owner-validated. P02 does not pick. It makes the pick cheap.

> **Contact data is never an attribute of the message, the request payload, or the notification. It is a separately classified party attribute, resolved at delivery time through a disclosure decision evaluated per (recipient, channel, field, request state) and recorded.**

Consequences:

- Conversation stores authored content plus an author **reference**. Never a structured contact field.
- Notification receives a **reference** — who, about what, a link. Never a pre-rendered body containing counterparty contact data. Rendering happens **after** a disclosure check.
- Owner says *expose after a quote* → one new predicate. Owner says *never* → the decision returns deny forever. Owner says *relay without exposing* → a relay identity per (party, request) becomes the resolved address of the same decision. **Three owner answers, one seam.**
- What would be a rewrite: a customer contact field on the request record, read by the provider interface and the notification template. A policy change would then touch storage, indexes, templates, and logs — **and every already-sent notification, which cannot be recalled.**

**Because of this seam, `A-010` / `OR-011` / `Q-008` does not need to be settled before P02 or P04 — conditional on P02 adopting the seam.** Without it, `A-010` becomes a blocker retroactively.

### What may appear in a notification body

| Allowed | Forbidden |
|---|---|
| That an event occurred, and its type | Counterparty contact data |
| The acting party's **public** display name | Request free text; event address; event date; guest count; budget |
| A non-guessable link to the authenticated surface | Offer amounts or terms |
| Coarse timing | Any conversation content; anything identifying a private individual |

The rationale is structural: **a notification body escapes the platform's access control permanently.** Email lands in shared inboxes and assistants' mailboxes; messages land on lock screens. Both persist beyond any deletion request Superola can honour. **Everything in a notification body is out of scope for deletion by construction.**

This tensions with `R-016` — content-free notifications get ignored. Resolve with public display name, event type, and a link, then measure (`A-024`).

### The leak the architecture cannot stop

Users type phone numbers into free text. Structurally:

1. Request and conversation free text is classified as **possibly containing contact data regardless of policy**, so it can never be exported to analytics, a search read path, or a notification body.
2. **The contact policy is a default and an incentive, not an enforcement.** Superola must not tell owners or users that in-platform means contact-protected. This is the same discipline as the `verified` terminology rule, applied to a privacy claim.
3. Pattern detection and redaction of contact data in free text is over-engineering at this scope. The honesty requirement is free.

### Consent

Consent is per (party, channel, **purpose class**) with granted-at, source, consent-text version, and withdrawn-at. **Two purpose classes minimum, separable:**

- **transactional / service** — the request you sent got a reply;
- **marketing / reactivation** — come back, new features, legacy re-engagement.

Conflating them is the mistake that makes legacy reactivation unusable and turns provider alerts into spam complaints that degrade deliverability for the transactional class too. Withdrawal is recorded and effective **without deleting the record** — unsubscribe is not delete.

### Abuse evidence versus deletion rights

These genuinely conflict. Proportional resolution: a report **snapshots the specific content it concerns** into a separate access-restricted evidence hold with its own retention clock. The hold must be **narrow** (only reported items), **clocked**, **operator-only**, and **enumerable** — so a data-subject request gets a truthful answer ("we retain X for abuse-report purposes until Y") rather than a false one ("all deleted"). Retention basis and period is a **legal question** (`Q-024`).

**Where the disclosure decision lives:** Conversation owns content; Notification owns delivery. **The disclosure decision sits between them and belongs to neither** — it reads party classification and request state. `AGENTS.md` already requires messaging and notifications to be separate capabilities; the disclosure decision is the missing third piece that makes the separation meaningful rather than cosmetic.

## 6. Public versus private fields

**The public projection is allowlist-derived.** The projection builder emits only fields in the declared publishable set; anything it does not know about is **absent, not passed through**. That inversion is the entire defence against *"we added a field and it leaked."*

**What makes a field publishable — all six required:**

1. Classification permits it — not customer-private, not operational, not sensitive.
2. It is a **provider** fact, not a third party's fact. Legacy import breaks this routinely: an imported record's "business phone" is often a natural person's personal number (`A-022`).
3. Its source has a lawful basis and, if imported, an approved-cohort marker.
4. It is accurate and fresh enough, with a correction path.
5. Media carries a rights acknowledgement.
6. It does not exceed what the human page shows — no machine-only claims.

**Governance.** The publishable field set is a **versioned governed product artifact with a named owner** — not a per-developer decision and not a per-provider toggle in V1. Providers control *values* and may suppress optional fields; providers do not control *classification*.

**Staying honest when classification changes.** The projection is **rebuildable from source at any time**, and the field set is **versioned** so a change forces a rebuild of every affected projection. A projection written only on edit will never propagate a de-classification to records nobody edits — which is most of them. The field-set version is recorded per projection so *"was this field public on date D?"* is answerable.

**Deactivation and deletion.** Deactivation removes the record from the projection and from discovery eligibility, and is reversible. Deletion removes the projection — but the downstream copies are the problem: caches, delivery networks, sitemaps, and, if indexing was ever approved, third-party indexes and model caches. Structurally: an unambiguous gone response a crawler can act on, a sitemap drop, and **a suppression record preventing re-publication by a later re-import.** Honest statement to record: Superola controls its own surfaces and **cannot guarantee removal from third-party indexes or corpora it authorized** — which is a reason to keep the crawler gate closed **until** the deletion path exists, not after (`Q-027`).

**Crawler and indexing as a separate gate, structurally.** Publication-to-humans and machine-access authorization are two independent switches, and **the crawler switch defaults to deny.** The structural implication: the projection must be servable to humans while machine access is denied — **no design in which indexability is an emergent property of being publicly reachable.** Indexability is explicit, per-surface, per-crawler-class configuration under the same governance as the publishable field set, with **one enforcement point and one owner**, because bot mitigation must not silently contradict the approved policy.

## 7. Legacy data provenance

| # | Requirement | Why structural rather than procedural |
|---|---|---|
| 1 | **Provenance per record *and per field*** — source system, source record identifier, batch, extracted-at, transformation | A migration mixes imported values with provider-corrected values in one record. After that, *"is this data ours or theirs?"* is unanswerable without field-level origin — which is the difference between a deletion request you can honour and one you cannot |
| 2 | **Lawful-basis marker per record** — basis class, decision, decider, time | Default absent means not usable. **Never inferred from presence in the export** |
| 3 | **Cohort marker plus separate gates** — publication, contact, claim, and **request eligibility last** | One "approved" flag collapses gates that legitimately fail independently |
| 4 | **No request eligibility before gates pass — as a domain invariant on the delivery predicate**, the same predicate as customer verification | A UI filter is bypassed by the operator tool and by any future channel. Note that in P02's model a legacy record is **structurally not a profile**, so this is belt and braces rather than the only defence |
| 5 | **Batch separability and reversibility** — batch identity plus the ability to enumerate everything a batch created or modified | *Unimport this batch* is not a backup restore. A restore also reverts legitimate post-import provider edits — a second incident |
| 6 | **Suppression list — checked before record creation** | Deletion without suppression is undone by the next pass, and repeated staged passes are anticipated. **This is the single most likely privacy incident in the plan.** Awkward and worth naming: a suppression list is personal data retained specifically to honour a deletion (`Q-024`) |
| 7 | **Deletion-on-request per record**, propagating to projection, caches, and delivery surfaces, recorded as done, **without deleting a whole batch** | |
| 8 | **Never in this repository** — and the **audit output is also derived from personal data.** Findings returning to this repository must be aggregates and counts with definitions, **not row-level examples**; synthetic examples must be labelled synthetic | `AGENTS.md` covers the export. It does not yet cover the findings, and P05 will want to paste examples. Say no now |

## 8. Multi-jurisdiction

**Structurally possible now — cheap to build, expensive to retrofit:**

| Requirement | Why now |
|---|---|
| **Data-subject request capability** — locate all data for a subject, produce, correct, delete, suppress | Hardest to retrofit. It requires a **subject reference** instead of contact strings duplicated across request, conversation, notification, and import records. **That single normalization is the whole battle** |
| Retention and deletion **per record**, parameterizable | Not one global policy |
| Lawful basis per record | Not per collection |
| **Jurisdiction attribute per data subject, with `unknown` as a real value mapping to the strictest handling among the working markets** | Legacy jurisdiction is frequently unknown (`A-025`) |
| **Locale-specific consent and notice text as versioned content, with the version recorded on the consent record** | *"What did they agree to?"* is answerable only if the version was captured. Impossible to reconstruct later |
| Notice and consent capture at point of collection, with source recorded, for both sides | |

**Premature at this scope:** data residency or regional processing separation (defer, but do not foreclose) · impact-assessment programme · a maintained processing register · an appointed privacy officer · transfer-mechanism paperwork · a consent platform · a per-jurisdiction policy engine · per-country deployments · **treating GDPR as in scope** — no working-scenario market requires it, and adopting it "to be safe" imports a cost this scope cannot justify. Flag it if the EU enters the scenario.

**Legal questions, each needing a named owner — owner plus qualified counsel per market. P02 does not opine on any of them.**

| Question | Feeds |
|---|---|
| Which regimes apply to legacy records by data-subject location, and what notice or basis permits contacting or migrating them? | `A-007`, `Q-005`, `OR-009`, `G-09`, `Q-029` |
| May preloaded third-party business records exist and be published pre-claim, per market? | `OR-008`, `R-006`, `Q-025` |
| What is the retention basis and period for abuse-evidence snapshots and suppression lists? | `Q-024` |
| Does relay-without-exposure carry notice or consent obligations per market? | `A-010`, `OR-011`, `Q-030` |

## 9. Auditability, moderation, agents, and the future payment boundary

**Audit.** Three record types kept distinct (`domain-model.md` §2.23). The eleven audit-worthy V1 actions are enumerated in `domain-model.md` §8. Granularity: actor (Account plus the Business acted for, or operator role), action type, subject **reference**, outcome, basis reference, timestamp with zone, channel. **Reference, not copy** — an audit record that copies request text becomes a second store of customer-private data with a *longer* retention than the original. Application-level append-only is proportional; cryptographic immutability and write-once storage are not. Two hard constraints: **audit records must not be deletable by the same path that deletes domain data**, or a deletion request erases the evidence that it was requested; and audit needs **a stated retention period** — "forever" is not a policy, it is unbounded liability that collides with deletion rights. Readable by an operator role only, and **that read is itself audited**. A data subject's right to know is served by an answer *derived from* audit, not by handing over the log.

**Moderation.** Report intake must not require the reported content to still be visible. The reporter's free-text note is **sensitive**. **Content state and actor state are two lifecycles** — conflating them means hiding one message suspends a business, or suspending an actor leaves their content live; and a Business's participation state is distinct from an Account's, so restricting an Account must not orphan a Business with independent obligations. Every state change is reversible with the prior state recoverable, and the reversal is audited. Deletion removes or anonymizes domain data while leaving the audit record of the decision and the narrow evidence hold, each with a stated basis and clock, plus a suppression record so re-registration cannot re-create the same public record.

**Structurally required operator capability** — must exist; the interface may be crude: hide and restore content; restrict, suspend, and reinstate an actor; approve, deny, or escalate a claim; unpublish and republish a profile; execute a data-subject request; reverse an import batch. **The line is that each must be possible without a database console**, because a console action has no actor, no reason, and no audit record. Manual exception queues are correct for duplicate resolution, taxonomy exceptions, stale supply, notification-failure follow-up, claim disputes, and report triage. No case-management system, service-level engine, automated classifier, or appeals workflow.

**Future agent or programmatic read access.** The one structural boundary that matters: any future external read path consumes **the same public projection humans get** — no privileged fields, no separate query path into source data. If an agent path can read something a logged-out human page cannot, the projection has been bypassed and a second unreviewed classification decision now exists. Machine access is a *crawler class* under §6's policy, not a special case. Any **authenticated** agent read routes through the same domain policy point as §3 — that is the concrete payoff for not putting authorization in the channel. **Delegated authority requires the acting Account to be distinguishable from the acting channel**: if V1 records only that the Business did something, a future delegated action is unattributable. Free now, invasive later. No write path in V1; the only structural preparation is that actions have identity so a retry cannot create a second request, and **provider opt-out is a Business-level fact, not a channel setting**.

**Future payment boundary.** It attaches **after** the V1 endpoint, to a booking-commitment concept that does not exist in V1, in a new module consuming an accepted-offer fact. It does **not** attach to the request, the offer, or the conversation. Before it attaches: an auditable booking event exists with a defined meaning (`Q-014`); **payout identity is established as a separate verification subject from marketplace identity**; retention and deletion machinery already works per record, because payment records carry retention obligations that conflict with deletion rights and the parameterization must exist *before* the conflict arrives; per-market legal and tax review has a named owner; and dispute handling has an operator owner and measured capacity.

**What V1 must avoid doing that would make it harder:** store anything payment-adjacent — no bank details, tax identifiers, or card data, not "for later", and **not via legacy import**, so classifying and excluding it must be an explicit P05 rule rather than a discovery mid-migration · conflate identity verification with payout identity, because `verified` on a Business must never be silently reused as *cleared to receive money* — typed verification facts are precisely what prevents this drift · give an offer payment-like semantics such as authorization, capture, or hold · put money-shaped language in the public projection or notifications, which creates a consumer-protection claim Superola cannot back · model ownership as a single reference, because payout is a legal-entity question.

## 10. Proportionality check — controls deliberately not recommended

| Not recommended | Why not, at this scope | Revisit when |
|---|---|---|
| Government-ID, business-registry, or document-review provider verification | No payments, no payout, no identified high-risk category. `feature-inventory.md` already rejects universal proofing. The cost lands directly on supply acquisition in a cold-start market — the worst place to add friction | A category with bodily or minor-contact risk launches, or the payout boundary attaches |
| Encryption of message content beyond ordinary at-rest and in-transit protection — per-conversation keys, end-to-end | It would break moderation, abuse-evidence preservation, report review, and operator exception handling — **all of which V1 explicitly requires.** End-to-end encryption and the V1 trust minimum are mutually exclusive; choosing it would silently delete a required capability | Never for this product shape, unless the policy changes to no moderation |
| Automated abuse detection, contact-pattern scanning, or redaction of free text | Cost and false-positive support burden at zero volume with no measured abuse. Report intake plus a manual queue is the correct first step | Report volume per request crosses the operator-capacity threshold the operating envelope already requires be defined |
| A policy language, external authorization service, or fine-grained permission matrix | Two roles, one product, small team. The §3 finding is about **placement**, which is free. Sophistication buys nothing and costs operability | Staff memberships ship **and** a second channel exists |
| A compliance programme — impact-assessment register, processing records, appointed officer, per-jurisdiction policy engine, per-state breach runbooks, consent platform, certification track | Pre-launch. No users, no revenue, no enterprise buyer demanding it. What is needed is the **structural capability** so a programme can be built on it when there is something to protect. Building the programme first is the failure mode this scope was warned about | A first real user cohort, a partner requirement, or counsel's answer demanding it |
| Write-once audit storage, security event management, a formal legal-hold workflow engine | Application-level append-only, restricted read, and a stated retention period answer every question this scope will realistically be asked | Regulated payment flows, or a dispute volume that makes evidentiary weight matter |
| Full session recording, endpoint agents, privileged-access management | Operator capabilities behind audited entry points, plus auditing operator reads of customer-private content, is the proportional version | Insider incident, or a counted number of unresolvable access questions |

## 11. What the pre-P02 trust minimum leaves structurally undefined

Direct assessment of `docs/01-product/feature-inventory.md`'s "Trust minimum for the proposed V1". The list is good for its length — items 3, 5, 7, and especially 8 are well judged. The problems are omissions, and two are substantive.

| Item | Assessment |
|---|---|
| 1 — verify account and contact control | Correct and well scoped. Missing: verification is a **revocable typed fact**, not a state; where the fact lives; and that a previously verified channel that later bounces is still verified but no longer deliverable |
| 2 — business and profile control; conditional claim | Right conditionality. **Substantive omission:** it applies one implicit verification bar to fresh registration and legacy claim, and those are not the same threat — a legacy record's channels are stale by construction, so channel proof cannot carry a claim alone. Also silent on the enumeration oracle, the post-claim change window, claim reversal and projection restoration, and the requirement that legacy records be a separately addressable thing |
| 3 — duplicates | Fine and proportional |
| 4 — publication quality, prohibited content, media rights, report intake, moderation state, deactivation | Good list. **"Moderation state" is singular where it must be two** (content versus actor). And it omits **evidence preservation entirely** — which is exactly where the deletion-rights collision lives |
| 5 — track receipt, response, no-response, notification failure, freshness | Good, and the no-punitive-labels instinct is right. Minor gap: these are *operational* facts and it does not say they must not reach the public projection beyond an approved label |
| 6 — minimize contact disclosure, preserve the thread, record the open policy | **Weakest item.** "Minimize" is a posture, not a structure. It omits the one thing that makes the open policy safe — contact data must not be an attribute of request, message, or notification — says nothing about **notification body content, the actual leak channel**, and does not acknowledge that free text defeats the policy, so the platform must not *claim* contact protection |
| 7 — sponsored separate from organic | Fine. One privacy-adjacent note: sponsored eligibility data is operational and must not enter the public projection or organic ranking inputs |
| 8 — reserve `verified` | **Best item in the list.** Gap: it stops at terminology and never draws the structural consequence — typed revocable facts instead of a boolean, and no reuse of a marketplace verification as payout clearance |

**Undefined across the whole section — the real gaps:** no authorization model at all, which is the largest hole for P03 and P04 because a channel-level route guard is the default thing a small team builds and exactly what breaks on the second channel · no data classification, so per-field publication can be dropped as a discovery detail · **no audit concept, not one line**, which makes claim, moderation, and deletion decisions indefensible later · no data-subject request capability, so it reads as deferrable while being among the most expensive things here to retrofit · no suppression concept · no consent model separating transactional from marketing, which legacy reactivation depends on · no jurisdiction dimension, now a live gap · and operator power undefined — nothing says operator actions are attributed, audited, and reversible, or that operator access to customer-private content is itself a controlled event.

None of these requires an enterprise programme. Four are design placement; three are one small set of records each; one is a single attribute.

## 12. Data classification

Classes: **public** / **provider-private** / **customer-private** / **operational** / **sensitive**. "May appear in" is an allowlist — anything not listed is prohibited for that element.

| Data element | Class | May appear in | Retention concern |
|---|---|---|---|
| Provider public business name, category, service-area labels | public | projection, search read path, notification body as display name, analytics | Live while published; dropped on deactivation or deletion |
| Provider base location — precise | provider-private | **the eligibility evaluation**; audit | **Readable by the eligibility computation, never emitted to a projection, a search result, a notification, or analytics.** Mobile-archetype eligibility is "resolves within the radius of the base", and an imprecise coordinate may not drive distance eligibility — so classification must permit the computation it depends on, and restrict only the *output*. Publishing uses the coarse derivative below |
| Provider base location — coarse publishable derivative | public | projection, search read path, analytics | Rebuild on classification change |
| Provider media | public, with rights acknowledgement | projection, search read path | The acknowledgement must survive; deletion must reach caches and delivery surfaces |
| Provider business contact channel | provider-private; **public only if explicitly marked and proven provider-owned** | disclosure-decision output; audit as reference | Legacy imports frequently carry a natural person's number here — treat as sensitive until proven business-owned (`A-022`) |
| Customer name | customer-private | audit as reference; disclosure-decision output | Never in projection or search read path |
| **Customer contact channel** | **customer-private / sensitive** | disclosure-decision output only; audit as reference | Never in projection, search read path, notification body, or analytics. Notification-body appearance is **undeletable by construction** |
| Event date | customer-private | authenticated surface; analytics only coarsened | Combined with location it identifies a real event and household |
| **Event location or address** | **sensitive** | authenticated surface only; audit as reference | **Highest-harm field in V1.** Never a notification body |
| Guest count, budget | customer-private | authenticated surface; analytics bucketed | Commercially and personally sensitive |
| **Request free text** | **customer-private; assume it contains contact data** | authenticated surface; evidence hold when reported | Never search read path, never notification body, never analytics. Deletion must reach it |
| **Conversation message text** | **customer-private; assume it contains contact data** | authenticated surface; evidence hold when reported | Same. Retention policy owed before launch |
| Offer amount and terms | provider-private plus customer-private (bilateral) | authenticated surface; analytics as aggregate | Never a notification body; never the public projection |
| **Notification body content** | **derived; treat as permanently exfiltrated once sent** | the external channel | **Out of scope for deletion by construction.** This is the whole reason for §5's allowlist |
| Notification delivery, bounce, complaint state | operational | operator surface; analytics | Distinct from consent and from verification. Short clock |
| VerificationFact | operational; public label only if the fact type is approved | operator surface; audit; projection only as an approved label | Expiry and revocation must be real, not decorative |
| ConsentRecord | operational | operator surface; audit | Must outlive withdrawal — unsubscribe is not delete |
| BusinessMembership | operational | operator surface; audit | Revocations retained; needed to answer "who could see this at time T" |
| Audit record | operational; **references, never payloads** | operator surface only | Needs a stated retention period. Must not be deletable by the domain-deletion path |
| Legacy provenance and lawful-basis marker | operational | operator surface; audit | Must persist for the record's whole life; per-field origin required |
| Report plus evidence snapshot | **sensitive** | operator surface only | Narrow, clocked, enumerable. Retention basis is a legal question |
| Suppression record | **sensitive** — personal data retained to honour a deletion | import gate; operator surface | Self-referential retention problem. Keep minimal |
| Search result projection | derived; **public only** | search results | In V1 the query reads source records directly, so **the publication allowlist must be applied as a predicate inside that query.** No field may reach a result that the policy does not permit |
| Analytics event | operational; pseudonymous, no free text, no contact data | analytics | The most common accidental personal-data export path in this product shape |
| Operator note or exception-queue annotation | operational; **may quote customer-private content** | operator surface; audit | Frequently overlooked in data-subject request scope. Enumerate it |

## 13. Threats

| Threat | Cost to Superola | Proportional structural mitigation | Over-engineered response avoided |
|---|---|---|---|
| Wrong party claims an imported record | A third party's demand and business data handed to a stranger; the incident that ends the legacy strategy | Default-deny claim eligibility; channel proof necessary but not sufficient; non-disclosing challenge; operator decision on mismatch; post-claim change window; full claim audit; revocable grant | Document-review pipeline, registry lookup, notarization |
| Claim flow used to enumerate imported records | Third-party contact records leaked via masking hints across the whole set | Disclose nothing pre-decision; per-record and per-actor failed-attempt records; operator review on volume | Bot-detection platform, challenge vendor stack |
| Customer contact leaks via notification body | Contact policy broken irreversibly; a sent message cannot be recalled | Notification receives a reference; body limited to event type, public display name, and link; render after a disclosure check | Outbound content-scanning |
| Customer contact leaks via provider interface, search path, or logs | Same, plus `A-010` becomes a rewrite | Contact is a party attribute behind a disclosure decision; never on the request or message; allowlist projection; search path fed only from the projection | Per-field encryption with key management |
| A private field appears in the public projection after a schema change | Provider trust damage; possible third-party data exposure | Allowlist projection: unknown fields absent; versioned field set; rebuildable projection | Per-provider per-field privacy controls |
| Deleted provider resurrected by the next import pass | An honoured deletion silently reversed — **the most likely privacy incident here** | Suppression record checked **before** record creation; batch reversibility; per-record deletion recorded | Global immutable deletion ledger |
| Content indexed by a crawler never approved | `Q-015` decided by accident; irreversible on third-party surfaces | Machine access is an explicit per-surface per-crawler switch defaulting to deny, with one enforcement point and one owner | Full bot-management programme |
| Provider spammed, or a request from an unreachable customer | Provider abandonment (`R-016`, `R-022`) | Just-in-time channel-control proof as a **delivery predicate in the domain**; abuse counters per actor | Fraud scoring, device fingerprinting, reputation services |
| Harassment via in-platform conversation | A safety incident with no evidence to act on | Report intake independent of content visibility; narrow clocked operator-only evidence hold; separate content and actor states, reversible | Discovery tooling, write-once archive, classifier |
| Deletion request destroys abuse evidence, or evidence retention defeats a deletion right | Legal exposure in both directions | Evidence hold separate from domain data with basis, clock, enumerability, restricted access; truthful data-subject answers | Legal-hold workflow engine |
| Operator reads customer requests or conversations with no record | Cannot answer "who saw this"; insider risk unbounded | Operator capabilities behind audited entry points; **no database console as the operating path**; audit operator reads of customer-private content | Session recording, endpoint agents, access-management platform |
| A second channel bypasses authorization | Silent data exposure across every rule that lived in a web route | Authorization decided in the domain against actor, action, resource, and state | Policy language, external authorization service |
| `verified` overclaims, or is reused as payout clearance | Consumer-protection and misrepresentation exposure; `Q-014` answered by accident | Typed, expiring, revocable verification facts; public labels only for approved fact types; no boolean on Business | Formal trust-scoring framework |
| Legacy lawful basis assumed from presence in the export | The whole imported cohort becomes unusable, or is used unlawfully | Lawful-basis marker per record, default absent means unusable; separate independent eligibility gates | Full consent-management platform |

## 14. Blockers and non-blockers

**Explicitly not a blocker, and saying so de-risks the gate:** `A-010` / `OR-011` / `Q-008` (contact disclosure policy) **does not need to be settled before P02 or P04**, conditional on P02 adopting §5's seam. `OR-011` is correctly filed as a secondary meeting topic; that remains safe on that condition.

**Blockers:**

| # | Blocker | Why it blocks | Owner | Cost to settle |
|---|---|---|---|---|
| 1 | `Q-028` — may one Account act in both customer and provider roles? | P02 fixes the party model. A hard account-type partition makes the routine dual-role case a migration. **P02 recommends the non-partitioned answer** (`ADR-004`) and needs ratification | David plus product | **Low.** One decision, no research |
| 2 | `G-09` / `OR-009` plus the legacy lawful-basis legal questions, **if claim is in P02 scope** | Already blocking in the release gate; this sharpens why. Default-deny eligibility only functions if someone owns setting the flag. If the answer is *no lawful basis in any working market*, **claim leaves V1 entirely and the legacy boundary collapses to a suppression list** — a materially smaller P02. That is a scope answer, not a compliance detail | Owner plus qualified counsel per market | Medium. Needs counsel, not just an owner answer |
| 3 | `Q-026` — is operator access to customer-private content auditable, and disclosed? | Read attribution is invasive to retrofit and P02 designs the audit surface | David plus product; no owner input needed | Low |
| 4 | `Q-027` — what removal commitment applies to surfaces Superola does not control, and does it gate crawler approval? | Must be settled **before** `Q-015`, not after. Approving a crawler before the deletion path exists makes the first deletion request permanently unanswerable on third-party surfaces. **The ordering is the blocker; the answer is cheap** | Owner plus David plus legal | Low, if sequenced correctly |

**Sharpened, not new:** `G-05` is already gated. Multi-jurisdiction in the working scenario means the accepted bounded scenario set must **name jurisdictions, not just markets**, or the per-subject jurisdiction attribute has no domain of values and `A-025` cannot be assessed.

**Not blockers:** notification body policy (P04 design time) · claim evidence tier (P02 and P05) · retention *periods* — they need counsel, but the machinery does not wait on the numbers, so build parameterizable and fill in later · data residency (defer, do not foreclose) · everything in §10.

## Sources

`AGENTS.md` · `docs/00-context/product-context.md` · `docs/00-context/interview-evidence.md` · `docs/00-context/assumptions.md` (`A-007`, `A-010`, `A-014`, `A-021`–`A-025`) · `docs/01-product/feature-inventory.md` · `docs/01-product/user-journeys.md` · `docs/01-product/actors.md` · `docs/01-product/owner-reconciliation-matrix.md` (`OR-007`–`OR-009`, `OR-011`) · `docs/05-roadmap/mvp-scope.md` · `docs/05-roadmap/risks.md` (`R-006`, `R-013`, `R-016`, `R-022`) · `docs/06-migration/legacy-data-strategy.md` · `docs/07-research/ai-discoverability.md` · `docs/07-research/messaging.md` · `docs/07-research/notifications.md` · `SRC-013`.
