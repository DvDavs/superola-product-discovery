# ADR-017 — Authentication is application-owned, with credentials in the marketplace store

- Status: PROPOSED
- Date: 2026-08-11
- Decision owner: David (technical decision owner for this repository)
- Related evidence/requirements: `docs/03-technology/technology-evaluation.md` §4.8; `build-vs-buy.md` §2.3; `p03-decision-inputs.md` `D-08`; `ADR-004` (ACCEPTED), `ADR-011`, `ADR-014`; `security-privacy-architecture.md` §2, §3, §9; `A-014`, `A-021`, `R-022`, `R-040`; `SRC-013`
- Supersedes / superseded by: N/A

## Problem / context

`D-08` requires proving control of a credential or channel at a point in time, serving **two consumers from one mechanism** (`A-021`): provider publication gating and just-in-time customer request gating. **The domain must not assume** that authentication proves identity, that a session carries role or permission, that a contact channel is unique or singular, or that credential type is fixed. **Authorization is decided in the domain** (`ADR-011`), so the mechanism must not be asked to carry it.

`ADR-004` is now `ACCEPTED` on a `DAVID_DIRECTIVE`: **one Account may act as both customer and provider, and mutually exclusive account types must not be created.**

## Constraints

Realistic monthly active accounts: ~300 / ~3,000 / ~25,000. One part-time operator. Verification abandonment is a measured cost (`R-022`). Operators must be able to restrict, suspend and reinstate an actor (§9). Verification is *"a set of typed, expiring, revocable facts"*, never a boolean on a business (§2).

## Options considered

### Option A — Application-owned authentication on the framework's established security library, credentials in the marketplace store, verification mail through the selected notification provider (recommended)

Password hashing, session management, email verification, password reset, rate limiting and lockout, built on primitives that are mature in every candidate framework.

### Option B — A hosted identity vendor

**Price is not the objection, and that is the finding.** At the three modelled tiers, two leading vendors are **$0 / $0 / $0** and a third is $25 flat. **The ordinary case for buying — cost relief at scale — is absent**, so the decision falls to architecture fit and exit, where the buy side fails four independent tests:

1. **A cliff already scheduled.** One vendor's free tier ends at exactly **25,000 monthly active users — the Growth target.** Building identity on a free tier whose ceiling *is* the plan is choosing a cliff already on the roadmap.
2. **Commercial pressure running opposite to the architecture.** `ADR-011` places authorization in the domain against actor, action, resource **and resource state** — which no vendor can see. `BusinessMembership` is a domain relation with grant and revocation times, needed to answer *"who could read this customer's request at time T?"*. **One vendor prices exactly the forbidden model at $100/month.** A future engineer under deadline pressure will see a solved problem rather than a boundary violation.
3. **The `VerificationFact` mismatch.** §2 requires typed, expiring, revocable facts. **Every vendor ships a verified-email boolean on the user.** Superola would shadow it immediately — reimplementing the vendor's flagship feature on day one, **with the vendor's copy being the one that cannot expire or be revoked.** When you must reimplement a vendor's core feature the day you adopt it, the boundary is in the wrong place.
4. **Revocation.** With a session in Superola's own store, revocation is a delete and is immediate. With a vendor-issued bearer token, revocation is bounded by token lifetime unless you call the vendor on every request — discarding the reason the token was chosen. **A safety suspension that takes effect "within 15 minutes" is not a suspension.**

**One vendor is recorded as runner-up** because it stores users in a queryable schema in the same engine family, so *"export the hashes"* is a query — a categorically better exit than any hosted-only option. **That case rests on one unverified fact: whether the password-hash column is readable and exportable on the paid plan.**

### Option C — A self-hosted identity server

Rejection factor: an always-on component with its own upgrade path, database and security-patch cadence, operated by one person, to replace something costing $0–25 per month. **A hiring decision, not a technology decision.**

## Decision

**Option A**, with three binding constraints:

1. **No mechanism may force role-typed accounts** or partition users into disjoint populations (`ADR-004`, ACCEPTED). This is an elimination criterion, not a preference.
2. **Marketplace authorization and business-membership semantics are never outsourced.** The mechanism proves channel or credential control; the domain decides everything else.
3. **At least one non-email credential path must exist** (`R-040`). See below.

**Approval still required:** David.

### `R-040` — the interaction neither `D-05` nor `D-08` noticed

`D-05` names the free cost lever: **make in-app request state authoritative and complete, so notification failure degrades speed, not correctness.** That lever requires users to reach in-app state **without email**.

**If authentication is magic-link or email-one-time-code only, then when email is degraded nobody can log in to see the in-app state that email failure was supposed to survive.** The cheap mitigation for one decision problem is silently cancelled by an apparently unrelated choice in another.

This also qualifies `R-022`'s friction-minimisation instinct: **the lowest-friction mechanism concentrates all access on the single channel the architecture already treats as unreliable.**

## Rationale

The decision is not that building is free. It is **8–12 engineering days initially and ~2 days per year** — real money and real risk. The decision is that **building is the reversible option, and the asymmetry is large.**

**BUILD → BUY is documented and supported:** vendors publish bulk import accepting hashed passwords, with unsupported algorithms requiring a reset on first login.

**BUY → BUILD may be impossible.** Password-hash exportability **could not be verified for any evaluated vendor** — and that is the single most important unverified item in the entire P03 evaluation. The documentary observation stands on its own: **vendors document how to get users *in* and are silent on how to get hashes *out*.** That asymmetry is itself evidence about where the commercial incentive points.

If no export exists, migrating 25,000 accounts means forcing every user to reset. The send is trivial; **the abandonment is not.** Against `R-022`, which already treats verification abandonment as a measured cost, that would be **the largest deliberate abandonment event in the product's life.** The mitigation is not a vendor choice — it is owning the credential.

`ADR-004` adds a second reason: the correct answer there was *the absence of a field*, at near-zero cost. **With no vendor there is no account-type model to get wrong** — the dual-role case is structurally impossible to break.

And a third: **no second email vendor.** `D-05` warns that each additional channel is *"a second copy of the whole reliability apparatus."* A vendor that sends its own verification mail creates exactly that, with its own deliverability reputation and its own bounce handling outside Superola's suppression model. **Option A satisfies this by construction rather than by a feature check.**

## Consequences

Positive: one identity, one consent set, one verified-channel set per human; cross-side abuse detection possible; immediate revocation; authentication availability collapses into store availability, so **no second failure domain for zero cost saving**; and the exit stays open because Superola holds the hashes.

Negative: 8–12 engineering days that produce no differentiating feature, plus a permanent obligation to keep current with the framework's security advisories. Social login, if ever wanted, is work rather than a toggle.

Operational: account recovery and locked-out users are the recurring support line — **and that volume is the same regardless of vendor**, which is another reason price was not the decider. Expect dual-role confusion (*"I am logged in as my business but I want to hire someone"*), which `ADR-004` predicts as an interface problem for P04, not an authentication problem.

## Cost implications

Recurring: **~$0/month at every scenario**, against $0–$225 for the evaluated vendors. One-time: **8–12 engineering days**, roughly $4,800–$7,200 at an assumed loaded rate — two to three years of the most expensive evaluated vendor, and it never becomes an exit problem. Operator: 1–3 hours per month at Growth.

## Lock-in and exit implications

**The best exit position available in this category, and it is the reason for the decision.** Superola holds its own hashes, sessions and verification facts, exportable by the same bulk export as everything else. Moving *to* a vendor later is a documented import path. **Moving *from* one may not be possible at all**, and could not be verified for any candidate.

## Security and privacy implications

Password storage uses the framework's current recommended algorithm with parameters recorded as configuration, so they can be raised without a migration. Sessions are server-side and opaque, which is what makes §9's suspension requirement real. **Authorization is decided per action against actor, resource and resource state** (`ADR-011`) — an account-type shortcut is exactly the coarse guard that breaks when one human legitimately holds two roles.

Verification facts are typed, expiring and revocable, with `A-021`'s two consumers served from one mechanism. **A previously verified channel that later bounces is still verified but no longer deliverable** — three concepts (*verified*, *consented*, *deliverable*) kept distinct per §2.

Not required and not built (§10): government-identity proofing, mandatory second factors for customers, device fingerprinting, enterprise federation, per-request step-up.

## Reconsideration trigger

Any one: **a signed requirement for enterprise federation from a named paying counterparty** — the one thing genuinely worth buying and genuinely painful to build; **or** account-recovery support cases exceeding 2% of monthly active users per month, measurable from the operator case records `D-11` already requires, which would indicate the self-built flows underperform; **or** a second delivery channel ships, making cross-client token lifecycle real work.

**And one that should reopen the runner-up rather than the decision:** if password-hash exportability is verified for a specific vendor, the exit asymmetry that decides this ADR weakens and Option B deserves a re-run.

## Validation

Verified 2026-08-11 from official pricing pages: per-user pricing and free-tier thresholds for four vendors, including the exact monthly-active-user threshold where one free tier ends; the priced organizations add-on; and the documented bulk-import path accepting hashed passwords. **Explicitly NOT VERIFIED: password-hash export for any vendor — the axis on which this ADR is recommended.** Eleven further vendors were not scored; that is a recorded gap.

Acceptance checks: a single Account can own a request and hold a business membership simultaneously without conflict; no authorization decision depends on an account type; an operator suspension takes effect on the next request; and **a user can authenticate without receiving an email** (`R-040`).
