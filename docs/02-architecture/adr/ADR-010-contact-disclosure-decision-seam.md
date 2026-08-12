# ADR-010 — Contact data is resolved through a disclosure decision, never stored on a request, message, or notification

- Status: PROPOSED
- Date: 2026-08-11
- Decision owner: David for the architecture; **the owner decides the disclosure policy (`OR-011`, `Q-008`)**
- Related evidence/requirements: `A-010` OPEN; `OR-011`; `Q-008`; `Q-030`; `docs/00-context/interview-evidence.md` (the owner's relay-without-exposing concept); `AGENTS.md` (messaging separate from notification delivery); `docs/05-roadmap/mvp-scope.md` (minimal sensitive content); `R-005`, `R-016`; `docs/02-architecture/security-privacy-architecture.md` §5; `decision-branches.md` `DB-09`
- Supersedes / superseded by: N/A

## Problem / context

`A-010` is OPEN and the contact-disclosure policy is genuinely undecided: keeping interaction in-platform is `DAVID_DIRECTIVE`, relay-without-exposing-contact is the owner's own concept (`OWNER_INTERVIEW`), and the **combined** policy is not owner-validated (`OR-011`).

P02 must not pick the policy. It must make the pick cheap — otherwise an owner answer arriving after implementation is a rewrite.

## Constraints

`AGENTS.md` requires messaging and notification delivery to be separate capabilities. `mvp-scope.md` requires notifications to carry minimal sensitive content with visible delivery and failure state. `R-016` warns that content-free notifications get ignored, which pulls in the opposite direction. Users will type phone numbers into free text regardless of any policy.

## Options considered

### Option A — Contact data as a party attribute behind a recorded disclosure decision (recommended)

Contact data is **never** an attribute of a request, a message, or a notification. It is a separately classified party attribute resolved at delivery or render time through a **disclosure decision** evaluated per (recipient, channel, field, request state) and recorded. Conversation stores authored content plus an author **reference**. Notification receives a **reference** and renders only after a disclosure check.

### Option B — Store the customer's contact details on the request record, read by the provider interface and the notification template

Rejection factors: a policy change would then touch storage, indexes, templates, and logs — **and every already-sent notification, which cannot be recalled.** This is the option that converts an open policy question into a rewrite.

### Option C — Decide the policy now in P02 and build to it

Rejection factors: the policy is the owner's to make (`OR-011`), and P02 pre-empting it is exactly the silent promotion this repository is built to prevent.

### Option D — Put the disclosure rule inside Conversation, or inside Notification

Rejection factors: it belongs to neither. It reads party classification and request state, so placing it inside either module creates a dependency that the forbidden-edge rules prohibit and makes the messaging/notification separation cosmetic rather than meaningful.

## Decision

Recommend Option A. **The disclosure decision sits between Conversation and Notification and belongs to neither.**

All three candidate owner answers become the same structure with a different predicate: *never expose* returns deny forever; *expose after a defined request state* adds one predicate; *relay without exposing* makes a relay identity per (party, request) the resolved address of the same decision.

**Notification body allowlist.** Permitted: that an event occurred and its type; the acting party's **public** display name; a non-guessable link to the authenticated surface; coarse timing. Forbidden: counterparty contact data; request free text; event address; event date; guest count; budget; offer amounts or terms; any conversation content.

**Free-text honesty requirement.** Request and conversation free text is classified as **possibly containing contact data regardless of policy**, so it can never be exported to analytics, a search read path, or a notification body — **and Superola must not tell owners or users that in-platform means contact-protected.**

**Approval still required:** David for the seam; **the owner for the disclosure policy itself (`OR-011`, `Q-008`), and counsel for `Q-030`. The seam is designed so that answer can arrive late without a rewrite.**

## Rationale

The structural rationale for the notification allowlist is not squeamishness: **a notification body escapes the platform's access control permanently.** Email lands in shared inboxes; messages land on lock screens. Both persist beyond any deletion request Superola can honour, so **everything in a notification body is out of scope for deletion by construction.**

The consequence for the release gate is substantive: **because of this seam, `A-010` / `OR-011` / `Q-008` does not need to be settled before P02 or P04.** `owner-reconciliation-matrix.md` correctly files `OR-011` as a secondary topic; that remains safe **conditional on P02 adopting this seam**. Without it, `A-010` becomes a blocker retroactively.

## Consequences

Positive: the policy stays a policy decision; customer contact is protected by default; the messaging/notification separation becomes meaningful rather than cosmetic; relay is reachable without a new subsystem.

Negative: notification bodies are less informative, which tensions with `R-016`. Resolved by public display name plus event type plus link, then measured (`A-024`).

Operational: consent is per (party, channel, **purpose class**) with at least two separable classes — transactional/service and marketing/reactivation. Conflating them makes legacy reactivation unusable and turns provider alerts into complaints that degrade transactional deliverability.

## Cost implications

The seam: LOW — one decision point plus classification discipline. Not having it: a policy rewrite touching storage, indexes, templates, and logs, plus irrecoverable already-sent messages. Contact-pattern detection or redaction in free text is deliberately **not** recommended at this scope; the honesty requirement is free.

## Lock-in and exit implications

None vendor-side. A relay identity, if the owner chooses relay, is an internal concept and does not imply a vendor capability.

## Security and privacy implications

This is the primary privacy control in the design. Field-level classification is in `security-privacy-architecture.md` §12. The customer's event location is the highest-harm field in V1 and never appears in a notification body. Abuse-evidence preservation uses a narrow, clocked, operator-only, **enumerable** hold so a data-subject request gets a truthful answer rather than a false one; its retention basis is a legal question (`Q-024`).

## Reconsideration trigger

An owner policy decision on `OR-011`; measured off-platform contact leakage; counsel's answer on whether relay-without-exposure carries notice or consent obligations per market (`Q-030`); or measured notification ignore rates that make the minimal-body trade untenable (`A-024`).

## Validation

Verify no request, message, or notification record stores a contact value; that every notification render passes a disclosure check; that request and conversation free text reaches neither analytics nor any search read path nor any notification body; and that no user-facing or owner-facing text claims contact protection as a guarantee.
