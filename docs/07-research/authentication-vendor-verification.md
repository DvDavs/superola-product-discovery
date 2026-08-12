# Authentication vendor verification — bounded P03.1 task

> **Status:** `OBSERVED` — primary-source vendor documentation, read directly. Provenance: `TECHNICAL_DISCOVERY`.
>
> **Scope:** ONE bounded verification of six questions across FOUR named vendors, run in P03.1 to close or precisely bound the single most important unverified item in P03. **The vendor list was not expanded and no other product was evaluated.** This document records facts and gaps. It makes no recommendation — the decision lives in `ADR-017`.
>
> **Dated research snapshot: all sources accessed 2026-08-12.** Vendor terms and prices change; re-verify before any purchase.

## Why this task existed

`ADR-017` recommends application-owned authentication, and its Rationale rests on an exit asymmetry:

> *"**BUY → BUILD may be impossible.** Password-hash exportability **could not be verified for any evaluated vendor** — and that is the single most important unverified item in the entire P03 evaluation."*

`cost-model.md` §12 carried the same gap. **A recommendation whose deciding axis is an unverified fact is not settled — in either direction.** P03.1 therefore verified the fact rather than approving around it.

## Classification vocabulary

- **VERIFIED YES / VERIFIED NO** — stated on an official vendor page.
- **GATED** — documented as available, but behind a support case or sales conversation rather than self-service.
- **NOT VERIFIED** — no official page found. **This never means "impossible."** It means unverified, and no inference was drawn from silence.

## 1. Summary

| | Q1 Bulk export | Q2 **Password-hash export** | Q3 Migrate away without forced reset | Q4 Immediate revocation | Q5 First pricing cliff |
|---|---|---|---|---|---|
| **Supabase Auth** | **VERIFIED YES** — SQL / CLI dump against your own Postgres | **VERIFIED YES** — bcrypt in `auth.users.encrypted_password`, self-service | **VERIFIED YES** (project→project quoted verbatim) | **PARTIAL** — session row deleted immediately; JWT valid until `exp` (default 1 h, configurable) unless the app checks `auth.sessions` | Free 50,000 MAU → Pro USD $25 + 100,000 MAU, then USD $0.00325/MAU |
| **Auth0 (Okta)** | **VERIFIED YES** — dashboard + Management API export job | **GATED** — support case plus a PGP public key | **GATED** — only via that support-case path | **PARTIAL / NOT VERIFIED** — deletion documented as *asynchronous, eventually consistent*; no official statement on already-issued access tokens | Free 25,000 MAU → Essentials B2C USD $35/mo, **B2B USD $150/mo**; **RBAC not on Free** |
| **Clerk** | **VERIFIED YES** — dashboard CSV + Backend API | **VERIFIED YES** — CSV *"includes their hashed passwords"*, self-service, admin-gated only. **Hash format NOT VERIFIED** | **VERIFIED YES in substance**; the verbatim vendor phrasing NOT VERIFIED | **VERIFIED YES — best of the four.** Session token lifetime **60 seconds** | Free 50,000 MRU → Pro USD $25 + 50,000 MRU, then USD $0.02/MRU; **B2B add-on USD $100/mo** |
| **Amazon Cognito** | **VERIFIED YES but weak** — `ListUsers` only, 60/page max, *"eventually consistent"*, no export job | **VERIFIED NO mechanism** — `UserType` carries no password field; no documented egress path. An explicit prohibition statement is NOT VERIFIED | **VERIFIED NO** via any vendor mechanism | **PARTIAL, with a documented hole** — revoked tokens *"still be valid if verified using any JWT library"* | Lite 10,000 MAU free then USD $0.0055/MAU; **Essentials** 10,000 free then **USD $0.015/MAU**; Plus **no free tier**, USD $0.020/MAU |

## 2. Evidence — Supabase Auth

**Q2 — VERIFIED YES.** *"Supabase Auth uses bcrypt, a strong password hashing function, to store hashes of users' passwords."* … *"The hash is stored in the `encrypted_password` column of the `auth.users` table."* — [supabase.com/docs/guides/auth/password-security](https://supabase.com/docs/guides/auth/password-security), accessed 2026-08-12.

**Q1 — VERIFIED YES, self-service, no ticket.** *"query the `auth.users` and `auth.identities` table via the `SQL Editor` tab to extract all users"* — [supabase.com/docs/guides/auth/managing-user-data](https://supabase.com/docs/guides/auth/managing-user-data). CLI path `supabase db dump` documented at [supabase.com/docs/guides/platform/migrating-within-supabase/backup-restore](https://supabase.com/docs/guides/platform/migrating-within-supabase/backup-restore). Both accessed 2026-08-12.

**Q3 — VERIFIED YES (project→project).** *"You can migrate all tables in the auth schema—including users and their hashed passwords—from one Supabase project to another."* … *"This means users do not need to reset or recreate their passwords after migration."* — [supabase.com/docs/guides/troubleshooting/migrating-auth-users-between-projects](https://supabase.com/docs/guides/troubleshooting/migrating-auth-users-between-projects), accessed 2026-08-12. **Honesty note:** the page addresses Supabase→Supabase. That the same bcrypt hashes verify in a self-built system is a property of bcrypt, not a claim on this page.

**Q4 — PARTIAL.** *"When a user signs out, the sessions affected by the logout are removed from the database entirely. You can check that the `session_id` claim in the JWT corresponds to a row in the `auth.sessions` table."* Default access-token expiry 1 hour, configurable; values below 5 minutes discouraged — [supabase.com/docs/guides/auth/sessions](https://supabase.com/docs/guides/auth/sessions), accessed 2026-08-12. **Reading: database revocation is immediate; JWT-only verification leaves a window up to the token TTL. Immediate enforcement requires a per-request check against `auth.sessions`.**

**Q5 —** Free 50,000 MAU; Pro/Team 100,000 MAU then USD $0.00325/MAU; SAML SSO 50 included then USD $0.015/MAU — [supabase.com/pricing](https://supabase.com/pricing), accessed 2026-08-12.

**Q6 —** No vendor organization or tenant primitive encountered. The documented RBAC pattern stores roles in customer-owned Postgres tables surfaced through a Custom Access Token Auth Hook — [supabase.com/docs/guides/database/postgres/custom-claims-and-role-based-access-control-rbac](https://supabase.com/docs/guides/database/postgres/custom-claims-and-role-based-access-control-rbac), accessed 2026-08-12.

## 3. Evidence — Auth0

**Q1 — VERIFIED YES.** *"You can bulk export user data out of Auth0."* — [auth0.com/docs/manage-users/user-migration/bulk-user-exports](https://auth0.com/docs/manage-users/user-migration/bulk-user-exports), accessed 2026-08-12. **No password or hash field is offered on the bulk-export page.**

**Q2 — GATED.** *"Open a support case requesting a password hash or MFA secrets export. Include the following in your request: The specific tenant name. Your PGP public key."* — [auth0.com/docs/manage-users/user-migration/export-password-hashes-and-mfa-secrets](https://auth0.com/docs/manage-users/user-migration/export-password-hashes-and-mfa-secrets), accessed 2026-08-12. Format NDJSON; the support article's examples begin `$2b$10$` (bcrypt) but **bcrypt is not stated as guaranteed**, metadata is excluded, and re-import needs conversion.

**Q4 — PARTIAL / NOT VERIFIED on the load-bearing detail.** *"Delete session operations run asynchronously, and are eventually consistent."* and *"Refresh tokens can remain active after a session has expired or been deleted, or after the user logs out."* — [auth0.com/docs/manage-users/sessions/manage-user-sessions-with-auth0-management-api](https://auth0.com/docs/manage-users/sessions/manage-user-sessions-with-auth0-management-api), accessed 2026-08-12. **NOT VERIFIED:** any official sentence stating whether already-issued access tokens survive revocation. The two token-revocation pages were checked and are silent. **Only community-forum posts assert it, which is not acceptable evidence — and silence is not evidence of the negative either.**

**Q5 —** Free up to 25,000 MAU; Essentials B2C USD $35/mo, **B2B USD $150/mo**; Professional B2C USD $240/mo, B2B USD $800/mo. **RBAC is not included in Free.** — [auth0.com/pricing](https://auth0.com/pricing), accessed 2026-08-12.

**Q6 —** Tokens carry a single `org_id`; *"permissions reflect only the roles held within the active Organization for that session"* — [auth0.com/docs/manage-users/organizations/using-tokens](https://auth0.com/docs/manage-users/organizations/using-tokens), accessed 2026-08-12.

## 4. Evidence — Clerk

**Q1 + Q2 — VERIFIED YES, self-service. This is the load-bearing finding of the task.** *"Users who are either admins or are in their personal workspace can export and download a CSV file containing a list of their application's users that **includes their hashed passwords**."* — [clerk.com/docs/guides/development/migrating/overview](https://clerk.com/docs/guides/development/migrating/overview), accessed 2026-08-12. Corroborated by the vendor's own changelog recording that this moved **out** of support-gating: *"if you needed hashed passwords, you had to rely on our support team to trigger a user export"* — [clerk.com/changelog/2024-10-23-export-users](https://clerk.com/changelog/2024-10-23-export-users), accessed 2026-08-12.

**Hash algorithm/format in the export: NOT VERIFIED.** No official page fetched documents the CSV's hash column format. **"Hashes are exported" is verified; "those hashes are in a format another system can verify" is not.** Closing it requires generating a real export from a test instance.

**Q4 — VERIFIED YES, strongest of the four.** Session token *"Expiration: 60 seconds"* — [clerk.com/docs/guides/how-clerk-works/overview](https://clerk.com/docs/guides/how-clerk-works/overview); revocation via `revokeSession()` — [clerk.com/docs/reference/backend/sessions/revoke-session](https://clerk.com/docs/reference/backend/sessions/revoke-session). Both accessed 2026-08-12. **Worst-case suspension window ≈ 60 seconds.** Whether 60 s is configurable: NOT VERIFIED.

**Q5 —** Hobby free to 50,000 MRU; Pro USD $25/mo with 50,000 included then USD $0.02 each; **B2B Authentication add-on USD $100/mo** (100 organizations, then ~USD $1/org) — [clerk.com/pricing](https://clerk.com/pricing), accessed 2026-08-12. **The MAU cliff is far away; the real cliff is the USD $100/mo add-on the moment organizations are needed.**

**Q6 —** *"Users can belong to multiple Organizations, and Clerk provides the Organization context (memberships, Roles, and the Active Organization) in each session."* … *"The Organization that a user is currently viewing is called the **Active Organization**."* Roles and permissions are defined at application level **inside Clerk** — [clerk.com/docs/guides/organizations/overview](https://clerk.com/docs/guides/organizations/overview), accessed 2026-08-12.

## 5. Evidence — Amazon Cognito

**Q1 — VERIFIED YES but weak.** `ListUsers` returns `UserType`; *"This operation is eventually consistent."*; `Limit` *"Maximum value of 60."* — [docs.aws.amazon.com/cognito-user-identity-pools/latest/APIReference/API_ListUsers.html](https://docs.aws.amazon.com/cognito-user-identity-pools/latest/APIReference/API_ListUsers.html), accessed 2026-08-12. No bulk-export job exists; AWS's own answer is a Step Functions reference architecture — [docs.aws.amazon.com/solutions/user-profiles-export-with-amazon-cognito](https://docs.aws.amazon.com/solutions/user-profiles-export-with-amazon-cognito/), accessed 2026-08-12.

**Q2 — VERIFIED NO mechanism; explicit prohibition NOT VERIFIED.** `UserType` contains no password or hash field. The nearest official statement is a vendor **blog**, and it concerns *import*: *"you cannot securely export passwords in cleartext form from an existing user store and import them into a Cognito user pool"* — [aws.amazon.com/blogs/security/approaches-for-migrating-users-to-amazon-cognito-user-pools](https://aws.amazon.com/blogs/security/approaches-for-migrating-users-to-amazon-cognito-user-pools/), accessed 2026-08-12. **No AWS reference-doc sentence saying hashes cannot be exported was located. The classification is therefore "no documented mechanism", not "documented as impossible."**

**The asymmetry, recorded because it is exactly the pattern `ADR-017` predicted.** Cognito documents hash *import* with `BCRYPT`, `SCRYPT`, `ARGON2ID` and `PBKDF2_SHA256`, and imported users *"can sign in immediately with their existing passwords, without a password reset"* — [docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-pools-using-import-tool.html](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-pools-using-import-tool.html), accessed 2026-08-12. **Hashes flow in and not out.**

**Q4 — PARTIAL, with a documented hole.** *"User pool JWTs are self-contained… Revoked tokens can't be used with any Amazon Cognito API calls that require a token. However, revoked tokens will still be valid if they are verified using any JWT library that verifies the signature and expiration of the token."* — [docs.aws.amazon.com/cognito/latest/developerguide/token-revocation.html](https://docs.aws.amazon.com/cognito/latest/developerguide/token-revocation.html). Access-token expiry configurable *"between 5 minutes and 1 day"* — [docs.aws.amazon.com/cognito/latest/developerguide/amazon-cognito-user-pools-using-the-access-token.html](https://docs.aws.amazon.com/cognito/latest/developerguide/amazon-cognito-user-pools-using-the-access-token.html). Both accessed 2026-08-12. **For an application verifying JWTs locally — the normal pattern — a suspended user stays authenticated for up to the token TTL, floor 5 minutes.**

**Q5 —** Lite 10,000 MAU free then USD $0.0055/MAU; Essentials 10,000 free then USD $0.015/MAU; **Plus has no free tier**, USD $0.020/MAU — [aws.amazon.com/cognito/pricing](https://aws.amazon.com/cognito/pricing/), accessed 2026-08-12.

**Q6 —** Authorization surfaces as a `cognito:groups` **array** claim, so multiple simultaneous roles are representable. Claim customization requires a pre-token-generation Lambda, itself **feature-plan-gated to Essentials or Plus**.

## 6. Does any vendor materially weaken the BUILD-side exit asymmetry?

**Yes. The P03 position — that hash exportability could not be verified for ANY vendor — is FALSIFIED for two of the four. This must be stated plainly rather than softened.**

- **Supabase — falsified, most strongly.** The hashes are bcrypt in a column of a Postgres database the customer already has direct SQL access to. **There is nothing to request; egress is a `select`.**
- **Clerk — falsified, cleanly.** Self-service dashboard CSV including hashed passwords, admin-gated only, with the vendor's own changelog recording the removal of the previous support-gate. **Residual gap: the hash format is NOT VERIFIED.**
- **Auth0 — not falsified; confirmed as GATED**, which is a distinct documented state. Exit exists but is not self-service and its timing is outside Superola's control. **The support-case SLA is unpublished, which is what determines whether GATED is a real exit or a theoretical one.**
- **Cognito — not falsified, and the negative is close to confirmed** on mechanism, though not on an explicit prohibition.

**What this does NOT establish.** It does not establish that BUY is now correct. It establishes that **the single axis on which `ADR-017` was decided no longer discriminates between BUILD and two named vendors**, and that the decision must be re-argued on the axes that survive rather than defended on the one that did not.

## 7. Structural conflicts with Superola-owned authorization and dual-role Accounts

`ADR-011` places authorization in the domain. `ADR-004` (**ACCEPTED**) requires that one Account may act as both customer and provider, and forbids mutually exclusive account types.

**(a) Application-owned authorization**

| Vendor | Conflict |
|---|---|
| **Supabase** | **Lowest.** No vendor authorization model; the documented pattern keeps roles in customer-owned tables |
| **Cognito** | **Moderate, plus a paywall.** Groups are the native primitive; overriding claims needs a Lambda gated to Essentials/Plus |
| **Clerk** | **High structural pull.** Roles and permissions are first-class Clerk objects defined in Clerk's dashboard |
| **Auth0** | **High commercial pull.** RBAC absent from Free; from USD $150/mo on the B2B track |

**(b) One Account holding customer AND provider roles simultaneously**

| Vendor | Conflict with `ADR-004` |
|---|---|
| **Clerk** | **Direct.** Users may belong to many Organizations, but a session has one **Active Organization**, and each tab maintains its own. **If marketplace roles were modelled as Organizations, a session could only ever be customer OR provider** — precisely the account-typed-by-role shape `ADR-004` forbids. Avoidable by not modelling roles as Organizations, but the product's grain pushes the other way |
| **Auth0** | **Direct.** One `org_id` per token; permissions reflect only the active Organization. Same failure mode, and B2B pricing encourages Organizations |
| **Cognito** | **None on this axis.** `cognito:groups` is an array |
| **Supabase** | **None.** No org or tenant primitive; the claim shape is entirely Superola's to define |

**The net result is uncomfortable and should be recorded as such: the two vendors with the best verified exit properties sit at opposite ends of the dual-role axis.** Supabase imposes nothing; **Clerk's Active-Organization model is the sharpest structural conflict with `ADR-004` found anywhere in this survey.**

## 8. What remains unresolved after this task

1. **Clerk's exported hash format/algorithm.** Not documented on any page fetched. Closing it needs a real export from a test instance.
2. **Auth0: whether already-issued access tokens survive session revocation.** No official statement located. Material, because `security-privacy-architecture.md` §9 requires suspension to be effective.
3. **Auth0's hash algorithm guarantee**, and behaviour for users created under other connection types.
4. **Auth0 multi-organization login semantics** — how a user belonging to several organizations selects one.
5. **Cognito: an explicit reference-doc statement on hash export.** Absence of mechanism is well-evidenced; explicit prohibition is not.
6. **Whether Clerk's 60-second session-token lifetime is configurable**, which would widen the suspension window.
7. **Supabase `admin.signOut` scope semantics** (global / local / others) — the reference page is a stub.
8. **Auth0's support-case export SLA, frequency limits, and repeat-export permission.** This is what decides whether GATED is a real exit.
9. **Clerk's MRU definition mechanics** — whether browse-heavy one-time signups count, which materially changes effective cost for a marketplace.

**A repository-internal constraint this task did not evaluate, recorded here because it bears on the same decision:** Supabase Auth's exit property comes from the hashes living in **Supabase's** Postgres. `ADR-014` selects **one** PostgreSQL cluster with schema-per-module, and `ADR-016` places it on the selected managed host. **Adopting Supabase Auth therefore means either a second store or moving the marketplace store to Supabase — a deployment decision, not an authentication decision.** That is a `TECHNICAL_DISCOVERY` inference from repository documents, not a vendor claim.

---

*Method note: four vendors, six questions, official vendor documentation and pricing pages only. Third-party blogs, forums, StackOverflow and search-engine summaries were rejected as evidence, and every place a claim rested only on such a source is marked NOT VERIFIED above rather than filled in. No figure or capability in this document was written from memory.*

*Record dates — research performed and all sources accessed 2026-08-12 (P03.1). This is a dated snapshot: vendor terms and prices change and must be re-verified before any purchase.*
