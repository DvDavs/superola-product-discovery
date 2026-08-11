# ADR-007 — Shared core plus typed category archetypes, with governed metadata confined to attributes

- Status: PROPOSED
- Date: 2026-08-11
- Decision owner: David
- Related evidence/requirements: `docs/01-product/feature-inventory.md` — **which explicitly charters this comparison for P02**; `docs/02-architecture/domain-model.md` §3; `A-009`, `A-017`, `A-018`, `R-010`, `R-015`; `OR-002`, `G-04`; `docs/00-context/product-context.md`
- Supersedes / superseded by: N/A

## Problem / context

`docs/01-product/feature-inventory.md` states that P01 does **not** design a universal category schema, and requires P02 to compare a narrow launch-category model, a shared core with extensions, and governed metadata or configuration against complexity, operability, migration, and change cost.

The Working Decision Envelope pre-supposes one of those three answers. **P02 ran the comparison anyway**, because skipping it would mean skipping the job P01 assigned. The envelope's preference is registered as `A-018` — a `DAVID_DIRECTIVE` design preference, not a given.

**Sequence breach, stated openly.** The charter says the comparison should follow selection of launch categories and evidence. `OR-002`/`G-04` is OPEN, so this comparison ran against the P01 archetype **hypothesis table** — built with zero provider evidence — rather than against a selected cohort. That is a genuine limitation of this ADR, and it is why the reconsideration trigger below requires re-deriving the archetype list from the owner's answer.

`CONFIRMED` (`OWNER_INTERVIEW`): provider-entered category creation likely degraded legacy taxonomy quality; data became saturated. `PROPOSED`: the five archetypes, from a P01 hypothesis table built with **zero provider evidence**. `OPEN`: the launch cohort (`OR-002`, `G-04`).

## Constraints

The archetypes differ materially on service-area semantics, resource unit, pricing basis, duration, capacity, media, offer structure, and resources. `R-015` warns that a universal model misrepresents all of these. `R-010` warns that poor taxonomy undermines search, public pages, promotions, and migration simultaneously. Taxonomy governance is the **highest-ranked operator cost driver** in the product.

## Options considered

The comparison found that `feature-inventory.md`'s table conflates **two kinds of variability**, which is why all three options look partly right:

| | Shape variability | Fact variability |
|---|---|---|
| What varies | Service-area semantics, resource concept, offer structure, eligibility | Which descriptive or filterable attributes exist |
| Nature | **Behavioural** — drives evaluation logic | **Declarative** — drives filters and completeness |
| Population | Few — five in the P01 table; **no upper bound asserted, because any figure would be invented** | Many, growing per category |
| Change rate | Rare; each is a genuine design decision | Frequent; each is a governance decision |

### Option A — Narrow launch-category model

Highest shape expressiveness, but **fails the broad-category scope**: it cannot reach performers, venues, professional services, food, and transportation without N bespoke models. Worse cost curve: "norteño group" costs a release for zero behavioural difference from "mariachi". Operators are constantly blocked on engineering.

### Option B — Shared core plus typed archetype extensions (recommended)

Archetype behaviour in code with tests; taxonomy, attribute definitions, value domains, filterability, and publication requirements as governed data.

### Option C — Governed metadata or configuration as the primary mechanism

**Deceptively expressive.** A route corridor can be *expressed* as configuration but cannot be *evaluated* without code; metadata expressive enough to evaluate arbitrary geographic and temporal predicates is a query language you now own and must test. Governance is strong on paper and **fragile in practice** — a surface expressive enough to define behaviour is expressive enough to be misconfigured into a broken market with no tests and no reviewer who understands the blast radius. And it carries the **highest legacy-failure-mode risk**: one lax administration policy away from provider-writable categories, which is exactly how the legacy taxonomy died.

## Decision

Recommend **Option B as the architecture**, with governed metadata **confined to descriptive and filterable attributes within an archetype**. Metadata may never define behaviour. Option A is rejected as an architecture but **retained as a launch-scope discipline**: implement only archetypes whose eligibility semantics can actually be evaluated and tested.

`CategoryArchetype` becomes first-class between `Category` and `ServiceOffering`. **Adding a Category is a governance act; adding an Archetype is an engineering act.**

**The single structural control against `A-009`:** a provider may *request* a category, and the request becomes a `CategoryProposal` in a governed queue that is never publicly visible, never searchable, never a taxonomy node, and never a public URL until an operator creates a governed Category. **Provider input becomes a request, never a node.**

**Approval still required:** David for the mechanism; **the owner for the launch cohort (`OR-002`, `G-04`), which is the input the charter expected to precede this comparison.**

## Rationale

Separating the two variabilities resolves the false choice: shape is few and behavioural, so it belongs in code with tests; facts are many and declarative, so they belong in governed data.

The cost curve is the decisive argument. Under Option B, "norteño group" costs an operator ten minutes because it binds to the existing performer archetype, while "equipment rental with pickup windows" costs an engineering decision — because it genuinely is one. Options A and C get this wrong in opposite directions.

Finally, `A-009` was a **governance** failure, not a modelling failure. Option B defeats it structurally; Option C reintroduces the same exposure through an administration surface.

**Guard clause:** if a category needs behaviour not expressible as data, that is evidence for a narrow model *for that category* — not for a bigger metadata engine. Bending an archetype is how Option B silently degrades into Option C.

## Consequences

Positive: broad category growth without a code change per category; consistent archetype-scoped filters; taxonomy, attributes, value domains, filterability, and publication requirements are all operator-owned; migration maps legacy text to Category once and Category to Archetype once.

Negative — stated honestly: **this is the largest carried cost in V1.** Validating and querying definition-bound values is harder than fixed fields, and it needs an operator path to change definitions. It is recommended on the strength of a growth envelope that is `PENDING` owner validation, so if the owner names one archetype and stays there, a narrow model would have been cheaper and clearer.

Operational: `filterability` is governed, per-`Market`, and **revocable**, with mandatory null semantics and a coverage threshold. Those three properties are the direct structural fix for the reported legacy saturation defect.

## Cost implications

Build: MEDIUM one-time for the mechanism, then LOW per category. Operator: **the dominant driver in the whole product** — taxonomy exception volume. Variable: more configured categories means more distinct filter shapes and earlier pressure on query capability. Exit: moving specific attribute families outward is incremental.

## Lock-in and exit implications

None vendor-side. The internal exit path in either direction: collapse to a narrow model for a specific category, or move attribute families from code to governed data. Both are incremental because the archetype boundary is explicit.

## Security and privacy implications

Each attribute definition carries a **publication class**, so per-field publication policy is governed at the definition rather than in a template (`ADR-012`). Provider narrative is never filterable and never mined into facets. The proposal queue prevents provider-supplied text from reaching a public surface without operator action.

## Reconsideration trigger

Any one: a launch archetype appears whose eligibility semantics cannot be evaluated and tested within the approved engineering budget; two categories **inside the same archetype** need different *eligibility* semantics rather than different facts; operators are blocked on engineering for more than an agreed number of category additions per period; a launch category is expressible by no archetype **and** cannot justify a new one — descope the category rather than bending an archetype; or `OR-002`/`G-04` returns an archetype set materially different from the P01 five, in which case re-derive the archetype list from the owner's answer before writing any of it down as structure.

## Validation

Verify no category-name conditional exists in behaviour; that no attribute value can exist without a governing definition; that a provider cannot create a publicly visible category by any path; that every definition declares null semantics; and that filterability can be revoked per `Market` without a code change.
