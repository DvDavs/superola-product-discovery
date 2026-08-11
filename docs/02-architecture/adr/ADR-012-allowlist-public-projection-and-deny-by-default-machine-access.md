# ADR-012 — The public projection is allowlist-derived and versioned; machine access defaults to deny

- Status: PROPOSED
- Date: 2026-08-11
- Decision owner: David for the architecture; **the owner decides indexability and each crawler class (`Q-015`)**
- Related evidence/requirements: `docs/07-research/ai-discoverability.md`; `Q-015`, `Q-022`, `Q-027`; `docs/01-product/feature-inventory.md` human-public distribution row; `R-011`, `R-013`; `A-022`; `docs/02-architecture/security-privacy-architecture.md` §6; `decision-branches.md` `DB-13`
- Supersedes / superseded by: N/A

## Problem / context

Public provider pages are central to the acquisition hypothesis, and the same projection is the future consumption surface for search crawlers, AI-search crawlers, and any external agent. P02 must decide how a field becomes public, who governs that, and whether being publicly reachable implies being machine-accessible.

`PROPOSED` (`DAVID_DIRECTIVE` plus `TECHNICAL_DISCOVERY`): useful human-rendered public pages with permissioned facts. **`OPEN`:** indexability and each crawler class (`Q-015`) — and `docs/07-research/ai-discoverability.md` is explicit that human-public publication does not authorize crawling, and that training access is decided independently.

## Constraints

Machine-readable claims must never exceed visible human claims. Per-field publication and freshness with a correction path are required. Bot mitigation must not silently contradict the approved crawler policy. Imported records frequently carry a natural person's contact data in a business field (`A-022`). `R-011` warns that thin or duplicate public pages convert a discovery strategy into search-quality debt.

## Options considered

### Option A — Allowlist-derived, versioned, rebuildable projection with deny-by-default machine access (recommended)

The projection builder emits **only** fields in the declared publishable set; anything it does not know about is **absent, not passed through**. The field set is a **versioned governed artifact with a named owner**. The projection is **rebuildable from source at any time**, and its field-set version is recorded per projection. Machine access is an explicit per-surface, per-crawler-class switch that **defaults to deny**, with **one enforcement point and one owner**.

### Option B — Denylist: public unless marked private

Rejection factors: **"we added a field and it leaked" is the entire failure mode**, and a denylist guarantees it. Every new field is public until someone remembers.

### Option C — Projection written only on edit, not rebuildable

Rejection factors: a de-classification never propagates to records nobody edits — **which is most of them.** And *"was this field public on date D?"* becomes unanswerable, which a moderation case or a data-subject request will eventually need.

### Option D — Indexability as an emergent property of public reachability

Rejection factors: it decides `Q-015` by accident, and irreversibly on third-party surfaces. Publication-to-humans and machine-access authorization are two independent switches.

### Option E — Per-provider per-field privacy controls in V1

Rejection factors: over-engineering at this scope. Providers control **values** and may suppress optional fields; providers do not control **classification**.

## Decision

Recommend Option A, with six conditions for a field to be publishable, **all required**: classification permits it; **it is a provider fact, not a third party's fact**; its source has a lawful basis and, if imported, an approved-cohort marker; it is accurate and fresh enough with a correction path; media carries a rights acknowledgement; and it does not exceed what the human page shows.

Additionally: **no field may reach a search result or any public surface that the publication policy does not permit.** In V1 the search query reads source records directly (`ADR-006`), so the allowlist is enforced **as a predicate inside that query** rather than by the existence of a separate store. Stated precisely because the two are easy to conflate: the requirement is *allowlist enforcement*, not *avoidance of source reads*.

**Approval still required:** David for the projection mechanism; **the owner for indexability and each crawler class (`Q-015`), and only after `Q-027` is settled.**

## Rationale

The inversion — absent rather than passed through — is the whole defence, and it costs nothing to adopt at the start.

Versioning plus rebuildability is what keeps the projection honest over time. Without both, a classification change is a promise rather than a fact.

Deny-by-default machine access has an **ordering consequence** that is easy to miss: **`Q-027` must be settled before `Q-015`.** Superola controls its own surfaces but **cannot guarantee removal from third-party indexes or corpora it authorized**, so approving a crawler before the deletion path exists makes the first deletion request permanently unanswerable. Keep the gate closed until the deletion path exists — not after.

## Consequences

Positive: new fields cannot leak; classification changes propagate; historical publication is provable; a locale or surface expansion does not implicitly approve indexing of it; and any future agent read path consumes exactly what a logged-out human sees.

Negative: publishing a new public field requires a governance step rather than a code change. That is the intended friction.

Operational: the publishable field set and the machine-access policy each need a named owner and an audit record on change. Deactivation removes the record from the projection and from discovery eligibility and is reversible; deletion additionally requires an unambiguous gone response, a sitemap drop, and a **suppression record preventing re-publication by a later re-import**.

## Cost implications

Build: LOW — a declared field set plus a rebuild path. Operator: LOW; two governance artifacts. Variable: media egress per anonymous public view is the dominant public-surface cost line, and it is the one line that grows with **acquisition success** rather than with revenue-bearing requests. Keeping the crawler gate closed is NEAR-ZERO; opening it adds a per-locale content inventory that must be differentiated and maintained (`R-011`).

## Lock-in and exit implications

None vendor-side. Third-party index and corpus persistence is a real one-way door, which is precisely why the gate defaults to deny.

## Security and privacy implications

This is the second primary privacy control after `ADR-010`. It prevents three distinct leaks: a newly added private field appearing publicly; the search read path bypassing classification; and a future agent path reading something a logged-out human page cannot — which would mean the projection has been bypassed and a second unreviewed classification decision now exists. Imported business-contact fields are treated as sensitive until proven business-owned (`A-022`).

## Reconsideration trigger

Per-field policy: an owner-approved public-acquisition hypothesis with maintenance economics. Machine access: per-crawler-class approval, **after** `Q-027` is settled. Per-provider field controls: measured provider demand.

## Validation

Verify the projection emits no field absent from the declared set; that a field-set version change forces a rebuild of every affected projection; that the field-set version is recorded per projection; **that no search result or public surface can contain a field the publication policy does not permit, including when the query reads source records directly**; that machine access has exactly one enforcement point; and that a deleted record cannot be re-created by a later import pass.
