# ADR-004 — An Account is not typed by marketplace role; customer and provider are roles

- Status: ACCEPTED
- Date: 2026-08-11
- Accepted: 2026-08-11 by David, at P03 start, as an explicit `DAVID_DIRECTIVE` (`SRC-014`). See "Ratification" below.
- Decision owner: David (product decision; no owner input required)
- Related evidence/requirements: `docs/02-architecture/domain-model.md` §1.1, §2.9, §2.10, §6; `security-privacy-architecture.md` §2; `docs/00-context/interview-evidence.md` (owner-reported overlapping performer categories); `Q-028`; `Q-006`
- Supersedes / superseded by: N/A

## Problem / context

Nothing in the repository states whether one human may act as both customer and provider. No assumption or question covered it before P02. The decision fixes the party model, and getting it wrong makes the common case a data migration.

`CONFIRMED` (`OWNER_INTERVIEW`): registrations included overlapping performer categories — bands, norteño groups, mariachis, DJs — which strongly suggests people who both supply and buy event services. `PROPOSED`: the party model itself.

## Constraints

A venue owner planning her daughter's quinceañera, or a DJ hiring a photographer, is routine in this market. Superola cannot prove human identity in V1, so `Person` is a concept and not a record. Notification consent and contact-channel verification attach to the Account. Cross-side abuse detection needs one actor identity.

## Options considered

### Option A — Untyped Account; customer and provider are roles derived from what the Account does (recommended)

Role in the marketplace is derived: acting as a customer means owning a request; acting as a provider means holding a `BusinessMembership`.

### Option B — Typed accounts, partitioned at registration

Rejection factors: forces duplicate accounts for the dual-role case; splits notification preferences, consents, and verified channels across two identities; makes cross-side abuse detection impossible; and **retrofitting is a migration** touching identity, consent, and every authorization decision.

### Option C — One Account with an explicit mutable "account type" flag

Rejection factors: a flag that must be flipped is a partition with extra steps. It still splits behaviour on a field the domain does not need, and the flip becomes a support case.

## Decision

Option A. There is no `Customer` entity, no `Provider` entity, and no account type. `Account` is the access principal; `Business` is the commercial party; role is derived from relationships and actions.

### Ratification — 2026-08-11

`Q-028` was open only for ratification. David ratified it at P03 start as an explicit `DAVID_DIRECTIVE`, in these terms:

> One Account MAY act as both customer and provider. Do not create mutually exclusive customer/provider account types.

This ADR therefore moves from `PROPOSED` to `ACCEPTED`. It is **the first accepted ADR in the repository.** Provenance is `DAVID_DIRECTIVE`, not owner validation — the decision was David's to make and the ADR index named no other approver.

**Scope of what the ratification settles, and what it does not.** It settles the party model. It does not settle contact-disclosure policy (`A-010`, `OR-011`), verification timing (`Q-018`, a P04 comparison), or business-membership capability (`DB-14` defers multi-user businesses while keeping ownership a revocable relation).

**Binding consequence for P03 technology selection:** no authentication mechanism may be selected that forces role-typed accounts, partitions users into disjoint populations, or makes the dual-role case require two identities. This is now a hard elimination criterion in `D-08`, not a preference.

## Rationale

The dual-role case is normal in this market, not an edge case. The cost of Option A now is essentially zero — it is the *absence* of a field. The cost of reversing Option B later is a migration across identity, consent, verification, and authorization. Asymmetric costs with the cheap option also being the correct model.

`Q-028` remains open only for ratification, not for design: P02 recommends this answer and David can settle it without owner input.

## Consequences

Positive: one identity, one consent record set, one verified-channel set per human; cross-side abuse detection is possible; no migration risk.

Negative: interface design must make the current role obvious, or a user can be confused about which capacity they are acting in. That is a P04 concern.

Operational: an operator investigating abuse sees one actor rather than two unlinked ones.

## Cost implications

Build: NEAR-ZERO — the decision is not to add a field. Operator: lower, because dual-role users do not generate account-linking support cases. Exit: reversal would be expensive, which is an argument for getting it right now.

## Lock-in and exit implications

None vendor-side. Internally, partitioning later is possible but would be a migration; the reverse — unifying two typed populations — is worse.

## Security and privacy implications

One Account means one verified-channel set and one consent set, which is *simpler* to reason about for data-subject requests than two linked identities. Authorization must therefore be decided per action against actor, resource, and state (`ADR-011`) rather than by an account-type shortcut — an account-type check is exactly the kind of coarse guard that breaks when the same human legitimately holds two roles.

## Reconsideration trigger

Evidence that dual-role users are vanishingly rare **and** a concrete requirement that only a partition satisfies. Neither exists, and the owner-reported category overlap is evidence against the first.

## Validation

Verify that no authorization decision depends on an account type, and that a single Account can own a request and hold a business membership simultaneously without conflict.
