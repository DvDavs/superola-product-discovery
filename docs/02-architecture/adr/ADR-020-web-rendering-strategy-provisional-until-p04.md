# ADR-020 — Web rendering strategy remains provisional until P04 establishes interaction requirements

- Status: **PROPOSED — DEFERRED, PENDING P04 UX VALIDATION**
- Decision owner: David (technical), with P04 owning the interaction requirements that decide it
- Scope: how Superola's web surfaces are rendered and how much client-side behaviour they carry. **Not** the platform (`ADR-013`), **not** the number of deployables (`ADR-001`, `ADR-013` Option D).
- Related evidence/requirements: `ADR-001`, `ADR-011`, `ADR-012`, `ADR-013`; `docs/02-architecture/p03-decision-inputs.md` `D-09`, `D-10`; `system-architecture.md` §3; `docs/03-technology/technology-evaluation.md` §4.9; `docs/03-technology/p03-decision-reconciliation.md`; `R-036`; `SRC-013`
- Supersedes / superseded by: **splits the rendering portion out of `ADR-013`**, which is now platform-only

## Problem / context

P03's `ADR-013` packaged six decisions into one approval: Kotlin, Spring Boot, the runtime baseline, mechanical module-boundary enforcement, one deployable, **and** server rendering with htmx and React-style islands.

The first five have VERY HIGH reversal cost. **The sixth has LOW–MEDIUM reversal cost, is decidable per surface, and depends on interaction requirements that do not yet exist.** P04 — UX Architecture — has not started. `G-06` (what "available" promises a customer) is unresolved and is the gate that blocks it. Neither the customer request flow, the provider response flow, nor the operator case surfaces have a designed interaction model.

**Approving rendering now would settle a product question by architecture side effect.** This repository already refuses exactly that move for the deployment region (`ADR-016` rule 1: *"select the host now; select the region at the geography gate"*). The same discipline applies here.

Evidence status: `PROPOSED` / `TECHNICAL_DISCOVERY`. **There is no interaction evidence, no usability evidence, and no traffic evidence** (`SRC-006` NOT RECEIVED).

## Constraints

`system-architecture.md` §3 establishes no realtime transport and no server-initiated push. `ADR-011` requires authorization decided in the domain, never at a route guard or in a template — **which binds every rendering option equally and is the constraint that must not be traded away.** `ADR-012` requires one machine-access enforcement point. `ADR-001` and `ADR-013` fix one deployable artifact. `D-09` requires locale-distinct public URLs with retained redirect history and server-readable public pages. One part-time developer.

## Options considered

### Option A — Server-rendered views plus progressive enhancement, with client islands only where a surface demands it (current provisional baseline)

Templates rendered by the framework's view layer; partial updates over HTML fragments; client-side components confined to named surfaces.

Benefits: every `D-09` requirement reduces to server routing plus governed data; no client/server version-skew bug class; least total code for form-and-list surfaces; public discovery pages are readable without executing script, which is what the acquisition hypothesis needs.

Costs: interaction-heavy surfaces are more work than in a component framework; each island is a second rendering model to maintain; the partial-update library is a third-party dependency inside the recommended stack.

### Option B — A richer client application for specific authenticated surfaces, served from the same deployable

A component-framework client for named surfaces — plausibly the request composer, the provider response/quote surface, the conversation view, or the operator case queue — with public discovery pages still server-rendered.

Benefits: client state surviving navigation, optimistic interaction, and multi-step composition are cheap rather than bespoke. **This is a legitimate outcome and P04 must be allowed to reach it.**

Costs: a second rendering model; a JSON contract to version; the version-skew bug class returns on those surfaces; public SEO surfaces must stay server-rendered regardless, so the codebase carries both.

### Option C — A full client-side application for all surfaces

Costs: the public discovery surface — the one carrying the acquisition hypothesis — becomes script-dependent, and `D-09`'s URL and redirect requirements move into client routing. **Not rejected on evidence; simply the option with the least support from what is currently known**, and P04 would need a specific reason.

### Option D — Separate frontend deployed independently

**Out of scope here — already rejected by `ADR-013` Option D on deployment-unit grounds, not rendering grounds.** Options A, B and C all live inside one deployable.

## Decision

**No rendering decision is made in P03/P03.1.**

1. **The working baseline is Option A** — server rendering plus progressive enhancement, with client islands where justified. It is what the cost model and the platform evaluation assume, and it is the cheapest starting point that does not foreclose the others.
2. **The baseline is PROVISIONAL and must not be treated as accepted, irreversible, or as an input P04 has to design around.** P04 may conclude that a React/Next-style richer client is justified for specific surfaces, and that conclusion is a legitimate P04 output rather than a reversal of an approved decision.
3. **Two constraints bind every option and are NOT provisional:** authorization is decided in the domain, never in a route guard, a template, or a client (`ADR-011`, `R-026`); and there is exactly one machine-access enforcement point (`ADR-012`). A richer client must consume the same application layer, not a parallel path.
4. **Public discovery pages must remain server-readable** under any option, because the acquisition hypothesis depends on it. This is a product constraint, not a rendering preference.

**Approval:** none sought. **P03.1 disposition: `HOLD` — decided by P04.**

## Rationale

**The asymmetry argument in `ADR-013`'s first draft was real but was applied at the wrong altitude.** *"Adding a JSON API and a client later is additive if `ADR-011` holds; removing one is not"* is a good reason to **start** on Option A. It is not a reason to **approve** Option A before the interaction requirements exist — starting somewhere and deciding something are different acts, and the first draft conflated them.

**The cost difference between the options is not the deciding factor and should not be presented as one.** All three render from the same one deployable, on the same instance tier, with the same database. Rendering choice moves no line in `cost-model.md` §3 or §4. What it moves is developer-days and, on interaction-heavy surfaces, the abandonment rate — and `R-022` already records verification abandonment as a measured cost. **A rendering decision taken to save infrastructure money would be optimising the smallest quantity in the model.**

**What P04 must produce to close this ADR**, per surface rather than globally:

1. The interaction requirement, evidenced by a specific journey in `docs/04-ux/`.
2. Whether client state must survive navigation on that surface.
3. Whether a progressive-enhancement implementation was attempted and what it cost.
4. Whether the surface is public and therefore bound by rule 4.

## Consequences

Positive: the platform can be approved on its own merits without silently approving a UX decision; P04 keeps a genuine choice; the two invariants that actually matter (`ADR-011`, `ADR-012`) are stated as binding rather than implied by a rendering style.

Negative: one named decision stays open into P04, which is a real cost in planning certainty. **It is the correct cost** — the alternative is certainty purchased by prejudging.

Operational: if P04 selects Option B for any surface, a client/server contract-versioning discipline becomes required on that surface and must be budgeted, not discovered.

Delivery: build the first surfaces under the baseline. **Do not build a JSON API "just in case"** — `R-012`'s anti-inflation rule binds: no deliverable whose only consumer is a possible future decision.

## Cost implications

**No infrastructure line changes under any option.** Fixed and variable cost in `cost-model.md` is identical across A, B and C: same deployable, same instance tier, same store, same delivery layer.

Developer-day direction only, and these are informed ranges, not measurements: Option A is the cheapest for form-and-list surfaces; Option B adds roughly a component-framework toolchain plus a versioned contract per surface it covers; Option C additionally re-implements `D-09`'s URL and redirect requirements in client routing. **No dollar figure is stated, because none was verified and none would be honest.**

## Lock-in and exit implications

**LOW–MEDIUM, and per-surface rather than global.** No vendor lock-in under any option. The binding asset is `ADR-011`: as long as authorization and the write path live in the domain behind one application layer, a surface can change rendering model without touching the marketplace logic. **That is what makes this decision safe to defer** — and it is also why rule 3 is not provisional.

The one genuinely expensive mistake available here is putting an authorization decision into a client or a route guard. That is not a rendering trade-off; it is `R-026`, and it is forbidden under every option.

## Security and privacy implications

A richer client changes where data lands, not who may see it. Under every option: the publication allowlist is applied inside the read (`ADR-012`, sharpened by `ADR-013` into a closed type); contact data resolves through the disclosure decision (`ADR-010`) and never reaches a client that has not passed it; and precise provider base location is never emitted to any projection.

**One rendering-specific trap, named so it is not discovered later:** a JSON endpoint built for a client island is a machine-access surface. It must sit behind the same single enforcement point as every other machine access, or `ADR-012`'s deny-by-default guarantee acquires a second door.

## Reconsideration trigger

This ADR does not need a trigger to reopen — **it is not closed.** It closes when P04 delivers, per surface, the four items listed in the Rationale.

Once P04 has closed it, the standing triggers that would reopen a *selected* rendering approach are: a named V1 surface requiring client state that survives navigation, evidenced by a specific journey **and** a failed progressive-enhancement attempt; measured p75 interaction latency on request submission above the approved budget, attributable to full-page round trips; or a client-island count exceeding five, which indicates the baseline has been outgrown by accumulation rather than by decision.

## Validation

Required instrumentation once any surface ships: per-flow interaction latency at p75 and p95, tagged with the surface name; a counted island inventory produced by the build; and request-submission abandonment segmented by surface, because `R-022` makes abandonment the measurable consequence a rendering choice actually has.

Acceptance checks: no authorization decision exists in a template, a route guard, or a client; every JSON endpoint added for a client passes through the single machine-access enforcement point; and every public discovery URL renders its governed content without executing script.

---

*Record dates — ADR created 2026-08-12 (P03.1), splitting the rendering portion out of `ADR-013` (authored 2026-08-11, P03). No new vendor or price research was performed for this record.*
