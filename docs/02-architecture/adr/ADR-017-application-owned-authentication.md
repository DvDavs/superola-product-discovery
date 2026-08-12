# ADR-017 — Authentication is application-owned, with credentials in the marketplace store

- Status: **PROPOSED — `HOLD`. Do not approve in the current approval packet.**
- Reconciliation disposition (P03.1): **`HOLD`.** The ADR's own reconsideration trigger — *"if password-hash exportability is verified for a specific vendor, the exit asymmetry that decides this ADR weakens and Option B deserves a re-run"* — **has fired.** See §*The P03.1 verification and what it changed* below.
- Decision owner: David (technical decision owner for this repository)
- Related evidence/requirements: `docs/07-research/authentication-vendor-verification.md` (P03.1 primary-source evidence); `docs/03-technology/technology-evaluation.md` §4.8; `build-vs-buy.md` §2.3; `cost-alternatives.md` §8; `p03-decision-inputs.md` `D-08`; `ADR-004` (ACCEPTED), `ADR-011`, `ADR-014`, `ADR-016`; `security-privacy-architecture.md` §2, §3, §9; `A-014`, `A-021`, `R-022`, `R-040`; `SRC-013`
- Supersedes / superseded by: N/A

## The P03.1 verification and what it changed

P03 recommended this ADR on an exit asymmetry and recorded the fact carrying it as unverified:

> *"**BUY → BUILD may be impossible.** Password-hash exportability **could not be verified for any evaluated vendor** — and that is the single most important unverified item in the entire P03 evaluation."*

**P03.1 verified it, against official vendor documentation only, for the four named vendors. The claim is FALSIFIED for two of them.** Full evidence with quotes, URLs and access dates: `docs/07-research/authentication-vendor-verification.md`, all sources accessed **2026-08-12**.

| Vendor | Password-hash export | Migrate away without forcing resets |
|---|---|---|
| **Supabase Auth** | **VERIFIED YES** — bcrypt in `auth.users.encrypted_password`; **egress is a `select`** | **VERIFIED YES** (project→project quoted; portability to a self-built system follows from bcrypt, not from a vendor claim) |
| **Clerk** | **VERIFIED YES** — self-service dashboard CSV *"includes their hashed passwords"*, admin-gated only. **Hash format NOT VERIFIED** | **VERIFIED YES in substance** |
| **Auth0** | **GATED** — support case plus a PGP public key. Not self-service; **SLA unpublished** | GATED, via that path only |
| **Amazon Cognito** | **VERIFIED NO mechanism.** An explicit prohibition statement is **NOT VERIFIED** — absence of a documented path is not proof of impossibility | **VERIFIED NO** via any vendor mechanism |

**This is recorded as a correction, not as a footnote, because it removes the load-bearing leg of the original recommendation.** *"Vendors document how to get users in and are silent on how to get hashes out"* was a fair reading of the evidence available in P03. **It is no longer accurate for Supabase or Clerk, and it must not be repeated.**

### Why the disposition is `HOLD` and not a reversal

**The exit axis no longer discriminates between BUILD and two named vendors. The remaining objections are not all equally healthy, and they must be re-argued rather than defended.**

| Original objection to BUY | Status after verification |
|---|---|
| **1. A cliff already scheduled** (a free tier ending at exactly 25,000 MAU) | **Vendor-specific, not general.** True of Auth0. **Not true of Supabase** (free 50,000 MAU; Pro USD $25 to 100,000 MAU) or **Clerk** (free 50,000 MRU). **Weakened** |
| **2. Commercial pressure toward vendor-owned authorization** | **Holds for Clerk and Auth0** — roles are first-class vendor objects, and Auth0 puts RBAC behind a paid tier. **Does not hold for Supabase**, which ships no authorization model and documents roles living in customer-owned tables. **Weakened for one vendor** |
| **3. The `VerificationFact` mismatch** — §2 requires typed, expiring, revocable facts; vendors ship a verified-email boolean | **Holds, but was NOT re-verified in P03.1** — it was outside the six bounded questions. Carried forward as a P03 claim, not as a P03.1 finding |
| **4. Revocation** — a suspension that takes effect "within 15 minutes" is not a suspension | **HOLDS, and is now sharply documented rather than asserted.** Supabase: the session row is deleted immediately, but a JWT stays valid to `exp` (default 1 hour) unless the application checks `auth.sessions` **on every request — which discards the reason the token was chosen.** Cognito: *"revoked tokens will still be valid if they are verified using any JWT library"*, with a 5-minute TTL floor. Auth0: deletion is *"asynchronous, eventually consistent"*, and whether issued access tokens survive is **NOT VERIFIED**. **No vendor of the four clears a next-request bar; Clerk comes closest at a ~60-second window, and whether that lifetime is configurable is NOT VERIFIED** |
| **5. `ADR-004` dual-role accounts** (not originally an objection at this altitude) | **New and sharper than anything P03 recorded.** Clerk and Auth0 both bind a session to **one active organization**; Auth0 tokens carry a single `org_id`. **If marketplace roles were ever modelled that way, a session could be customer OR provider and never both** — the exact shape `ADR-004` (ACCEPTED) forbids. Avoidable, but the products' grain pushes toward it. **Cognito and Supabase impose nothing here** |

**The uncomfortable result, stated rather than smoothed:** **the two vendors with the best verified exit sit at opposite ends of the dual-role axis.** Supabase imposes nothing on authorization or account shape but has the weakest revocation story of the two; Clerk has the only genuinely immediate revocation and the sharpest conflict with `ADR-004`.

**And one repository-internal constraint that the vendor comparison does not surface.** Supabase's exit property exists *because* the hashes live in **Supabase's** Postgres. `ADR-014` selects **one** PostgreSQL cluster with schema-per-module and `ADR-016` places it on the selected managed host. **Adopting Supabase Auth therefore means either a second store or moving the marketplace store to Supabase — a deployment decision wearing an authentication decision's clothes**, and it is not priced anywhere.

### What closes this ADR

**Not more vendor research.** Three bounded items, in order:

1. **Clerk's exported hash format**, obtainable by generating one export from a test instance. It converts Clerk's exit from *"hashes leave"* to *"hashes are usable elsewhere"*, or does not.
2. **A decision on whether immediate revocation is a hard requirement.** `security-privacy-architecture.md` §9 requires operators to suspend an actor. **If the bar is "effective on the next request", only BUILD qualifies** — a server-side session is deleted and the next request fails. **Every vendor of the four leaves a bounded window**, and Clerk's is the shortest at ~60 seconds; Supabase's default is 1 hour unless the application checks `auth.sessions` per request; Cognito's floor is 5 minutes. **If a bounded window is acceptable, the field reopens and Clerk becomes a serious candidate on this axis — and that is a policy question for David, not a research question.**
3. **Auth0's support-case export SLA**, if Auth0 is to remain a candidate at all.

**Until those are answered, the working recommendation below stands and is unchanged: application-owned authentication on the framework's security library.** It is preserved because it satisfies every axis without a vendor negotiation, not because the alternatives were shown to be worse — **which is exactly the distinction P03 got wrong once already and `technology-evaluation.md` §4.1's PostgreSQL wording gets right.**

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

**Supabase Auth is recorded as runner-up** because it stores users in a queryable schema in the same engine family, so *"export the hashes"* is a query — a categorically better exit than any hosted-only option. **P03 recorded that this case rested on one unverified fact. P03.1 verified it: the hashes are bcrypt in `auth.users.encrypted_password`, readable by SQL, self-service.** The runner-up case is therefore **stronger than P03 recorded**, and the objections that survive against it are revocation semantics and the second-store consequence, not exit.

### Option C — A self-hosted identity server

Rejection factor: an always-on component with its own upgrade path, database and security-patch cadence, operated by one person, to replace something costing $0–25 per month. **A hiring decision, not a technology decision.**

## Decision

**Option A**, with three binding constraints:

1. **No mechanism may force role-typed accounts** or partition users into disjoint populations (`ADR-004`, ACCEPTED). This is an elimination criterion, not a preference.
2. **Marketplace authorization and business-membership semantics are never outsourced.** The mechanism proves channel or credential control; the domain decides everything else.
3. **At least one non-email credential path must exist** (`R-040`). See below.

**Approval: NOT SOUGHT in the current packet. `HOLD`.** This ADR must not be approved as BUILD on the strength of an exit argument that verification has weakened. Option A remains the **working recommendation** — what it is not, yet, is a settled decision.

### `R-040` — the interaction neither `D-05` nor `D-08` noticed

`D-05` names the free cost lever: **make in-app request state authoritative and complete, so notification failure degrades speed, not correctness.** That lever requires users to reach in-app state **without email**.

**If authentication is magic-link or email-one-time-code only, then when email is degraded nobody can log in to see the in-app state that email failure was supposed to survive.** The cheap mitigation for one decision problem is silently cancelled by an apparently unrelated choice in another.

This also qualifies `R-022`'s friction-minimisation instinct: **the lowest-friction mechanism concentrates all access on the single channel the architecture already treats as unreliable.**

## Rationale

The decision is not that building is free. It is **8–12 engineering days initially and ~2 days per year** — real money and real risk. The decision is that **building is the reversible option, and the asymmetry is large.**

**BUILD → BUY is documented and supported:** vendors publish bulk import accepting hashed passwords, with unsupported algorithms requiring a reset on first login.

**BUY → BUILD, corrected in P03.1 and this is the correction that matters most in this record.** P03 wrote that hash exportability *"could not be verified for any evaluated vendor"* and inferred a general documentary asymmetry — *"vendors document how to get users in and are silent on how to get hashes out."* **That inference was drawn from unverified silence, and verification falsified it for two of four vendors.** Supabase documents the hashes as bcrypt in a table the customer owns; Clerk documents a self-service export that *"includes their hashed passwords"* and its changelog records removing the previous support-gate. **The asymmetry is real for Auth0 (gated) and Cognito (no documented mechanism) and false for the other two.**

**The consequence for this ADR is precise: the exit argument no longer decides it.** What survives is a narrower and still-real point — **where no export exists, migrating 25,000 accounts means forcing every user to reset.** The send is trivial; **the abandonment is not.** Against `R-022`, which already treats verification abandonment as a measured cost, that would be the largest deliberate abandonment event in the product's life. **That risk is now vendor-specific rather than categorical, and it must be stated that way.**

`ADR-004` adds a second reason: the correct answer there was *the absence of a field*, at near-zero cost. **With no vendor there is no account-type model to get wrong** — the dual-role case is structurally impossible to break.

And a third: **no second email vendor.** `D-05` warns that each additional channel is *"a second copy of the whole reliability apparatus."* A vendor that sends its own verification mail creates exactly that, with its own deliverability reputation and its own bounce handling outside Superola's suppression model. **Option A satisfies this by construction rather than by a feature check.**

## Consequences

Positive: one identity, one consent set, one verified-channel set per human; cross-side abuse detection possible; immediate revocation; authentication availability collapses into store availability, so **no second failure domain for zero cost saving**; and the exit stays open because Superola holds the hashes.

Negative: 8–12 engineering days that produce no differentiating feature, plus a permanent obligation to keep current with the framework's security advisories. Social login, if ever wanted, is work rather than a toggle.

Operational: account recovery and locked-out users are the recurring support line — **and that volume is the same regardless of vendor**, which is another reason price was not the decider. Expect dual-role confusion (*"I am logged in as my business but I want to hire someone"*), which `ADR-004` predicts as an interface problem for P04, not an authentication problem.

## Cost implications

**Three distinct quantities, kept distinct — see `cost-alternatives.md` §8.**

- **One-time engineering cost:** **8–12 engineering days, roughly USD $4,800–$7,200** at an assumed loaded rate. An estimate, not a quote.
- **Monthly vendor cost:** **USD $0/month at every scenario** for Option A, against **USD $0 / USD $0 / USD $0–225** for the evaluated vendors at ~300 / ~3,000 / ~25,000 monthly active accounts (Cognito Essentials reaches USD $225.00 at 25,000 MAU; Supabase Pro is USD $25.00 flat; Auth0 and Clerk are free at these volumes, with Clerk's B2B add-on at USD $100/month if organizations are ever used).
- **Migration and lock-in risk:** **LOW outbound for Option A.** For vendors, now vendor-specific rather than uniform — see the verification table above.

Operator: 1–3 hours per month at Growth, **and that volume is the same regardless of vendor**, which is another reason price was never the decider.

## Lock-in and exit implications

**Option A holds the best exit position available — but it is no longer uniquely good, and it is no longer the reason for the decision.** Superola holds its own hashes, sessions and verification facts, exportable by the same bulk export as everything else. Moving *to* a vendor later is a documented import path.

**Moving *from* a vendor is now vendor-specific and verified** (`docs/07-research/authentication-vendor-verification.md`, accessed 2026-08-12): **self-service for Supabase and Clerk**, **gated behind a support case with unpublished SLA for Auth0**, and **no documented mechanism for Cognito** — where the honest classification is *no path found*, not *proven impossible*. **The blanket claim that BUY→BUILD may be impossible is withdrawn.**

## Security and privacy implications

Password storage uses the framework's current recommended algorithm with parameters recorded as configuration, so they can be raised without a migration. Sessions are server-side and opaque, which is what makes §9's suspension requirement real. **Authorization is decided per action against actor, resource and resource state** (`ADR-011`) — an account-type shortcut is exactly the coarse guard that breaks when one human legitimately holds two roles.

Verification facts are typed, expiring and revocable, with `A-021`'s two consumers served from one mechanism. **A previously verified channel that later bounces is still verified but no longer deliverable** — three concepts (*verified*, *consented*, *deliverable*) kept distinct per §2.

Not required and not built (§10): government-identity proofing, mandatory second factors for customers, device fingerprinting, enterprise federation, per-request step-up.

## Reconsideration trigger

Any one: **a signed requirement for enterprise federation from a named paying counterparty** — the one thing genuinely worth buying and genuinely painful to build; **or** account-recovery support cases exceeding 2% of monthly active users per month, measurable from the operator case records `D-11` already requires, which would indicate the self-built flows underperform; **or** a second delivery channel ships, making cross-client token lifecycle real work.

**And the one that has already fired:** *"if password-hash exportability is verified for a specific vendor, the exit asymmetry that decides this ADR weakens and Option B deserves a re-run."* **P03.1 verified it for Supabase and Clerk. The trigger fired, the re-run is owed, and that is why the status is `HOLD` rather than `RECOMMEND ACCEPT`.**

## Validation

Verified **2026-08-11** from official pricing pages: per-user pricing and free-tier thresholds for four vendors, including the exact monthly-active-user threshold where one free tier ends; the priced organizations add-on; and the documented bulk-import path accepting hashed passwords.

Verified **2026-08-12** in the bounded P03.1 task, from official vendor documentation only, for Supabase Auth, Auth0, Clerk and Amazon Cognito: bulk export, **password-hash export**, migration without forced resets, session-revocation semantics and access-token lifetimes, MAU/MRU pricing cliffs, and structural constraints against application-owned authorization and dual-role accounts. **Full evidence, quotes and URLs: `docs/07-research/authentication-vendor-verification.md`.**

**Still NOT VERIFIED after P03.1**, and each stated as unverified rather than assumed either way: Clerk's exported hash **format**; whether Auth0 access tokens survive session revocation; Auth0's support-case export SLA; an explicit Cognito reference-doc statement on hash export (absence of a mechanism is well-evidenced, an explicit prohibition is not); whether Clerk's 60-second token lifetime is configurable. **Eleven further vendors remain unscored, deliberately — the P03.1 mandate forbade expanding the list.**

Acceptance checks: a single Account can own a request and hold a business membership simultaneously without conflict; no authorization decision depends on an account type; an operator suspension takes effect on the next request; and **a user can authenticate without receiving an email** (`R-040`).

---

*Record dates — ADR authored 2026-08-11 (P03). Status moved to `HOLD` and the exit argument corrected 2026-08-12 (P03.1) following bounded vendor verification. Pricing evidence accessed 2026-08-11; capability and terms evidence accessed 2026-08-12. Re-verify before any purchase.*
