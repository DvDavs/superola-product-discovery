# ADR-020 — Web rendering strategy: server rendering with progressive enhancement and three named client islands

- Status: **PROPOSED — P04 EVIDENCE DELIVERED; RECOMMENDS OPTION A; DAVID APPROVAL REQUIRED**
- Reconciliation disposition: `HOLD` — set by P03.1, **partially discharged by P04**. P04 delivered **three of the four** per-surface items this ADR required; the fourth — what a progressive-enhancement implementation cost — is **unavailable until code exists** and is argued rather than measured. The decision is therefore a normal `PROPOSED` recommendation awaiting the human decision owner, **not a closed evidence question.**
- Decision owner: David (technical). The interaction requirements that decide it were produced by P04.
- Scope: how Superola's web surfaces are rendered and how much client-side behaviour they carry. **Not** the platform (`ADR-013`), **not** the number of deployables (`ADR-001`, `ADR-013` Option D).
- Related evidence/requirements: `ADR-001`, `ADR-011`, `ADR-012`, `ADR-013`; `docs/04-ux/surface-inventory.md`, `docs/04-ux/rendering-evidence.md`, `docs/04-ux/customer-journey.md`, `docs/04-ux/request-intake.md`, `docs/04-ux/accessibility-and-responsive.md`, `docs/04-ux/map-decision.md`; `docs/02-architecture/p03-decision-inputs.md` `D-09`, `D-10`; `system-architecture.md` §3; `docs/03-technology/technology-evaluation.md` §4.9; `docs/03-technology/p03-decision-reconciliation.md`; `R-022`, `R-036`; `SRC-013`
- Supersedes / superseded by: **splits the rendering portion out of `ADR-013`**, which is platform-only

## Problem / context

P03's `ADR-013` packaged six decisions into one approval: Kotlin, Spring Boot, the runtime baseline, mechanical module-boundary enforcement, one deployable, **and** server rendering with htmx and React-style islands.

The first five have VERY HIGH reversal cost. **The sixth has LOW–MEDIUM reversal cost, is decidable per surface, and depended on interaction requirements that did not yet exist.** P03.1 therefore split it out and deferred it, refusing to settle a product question by architecture side effect — the same discipline `ADR-016` rule 1 applies to the deployment region.

**P04 has now run.** It produced thirty-nine named surfaces with states, interaction requirements, and degraded cases (`docs/04-ux/surface-inventory.md`), a customer journey and a provider journey, an accessibility baseline, and a per-surface rendering analysis (`docs/04-ux/rendering-evidence.md`). This ADR records what that evidence supports.

**What has still not changed:** `G-06` — what "available" promises a customer — remains UNSATISFIED. P04 proceeded on the working assumption `WA-01` rather than on an owner answer, and its surface set is stamped accordingly. There is still **no usability evidence and no traffic evidence** (`SRC-006` NOT RECEIVED). **Nothing has been implemented, so no rendering comparison in this record is a measurement.** Every implementation-cost statement below is an argued estimate and is labelled as one.

Evidence status: `PROPOSED` / `TECHNICAL_DISCOVERY`.

## Constraints

`system-architecture.md` §3 establishes no realtime transport and no server-initiated push — which removes the single most common reason a marketplace reaches for a rich client. `ADR-011` requires authorization decided in the domain, never at a route guard or in a template. `ADR-012` requires one machine-access enforcement point. `ADR-001` and `ADR-013` fix one deployable artifact. `D-09` requires locale-distinct public URLs with retained redirect history and server-readable public pages. `ADR-018` fixes a small enumerated set of pre-generated image derivatives with an explicit processing state, and forbids audio and video upload. One part-time developer.

## Options considered

### Option A — Server-rendered views plus progressive enhancement, with client islands only where a surface demands it

Templates rendered by the framework's view layer; partial updates over HTML fragments; client-side components confined to named surfaces.

Benefits: every `D-09` requirement reduces to server routing plus governed data; no client/server version-skew bug class; least total code for form-and-list surfaces; public discovery pages are readable without executing script, which is what the acquisition hypothesis needs.

Costs: interaction-heavy surfaces are more work than in a component framework; each island is a second rendering model to maintain; the partial-update library is a third-party dependency inside the recommended stack.

**P04 evidence:** of the **35 rendered V1 surfaces, 24 are `DOC` and 11 are `LOCAL`** — every one is document-and-navigation or carries only local interactive state, **with one recorded exception: `UX-07` holds navigation-persistent anonymous browser-local state during the pre-`Account` window opened by `WA-05`.** P04 classes it `LOCAL` because that state has a named island (`I-2`), a named domain owner once an `Account` exists (`RequestDraft`), and a working no-script degradation — not because the state is absent. See `docs/04-ux/rendering-evidence.md` §4.1. (`UX-35` is a notification body, not a rendered surface; `UX-37` and `UX-38` are `FUTURE` and deliberately unclassified.) The three islands named in the budget below span six of those surfaces; they are the only places where plain HTML is genuinely insufficient.

### Option B — A richer client application for specific authenticated surfaces, served from the same deployable

A component-framework client for named surfaces — plausibly the request composer, the provider response/quote surface, the conversation view, or the operator case queue — with public discovery pages still server-rendered.

Benefits: client state surviving navigation, optimistic interaction, and multi-step composition are cheap rather than bespoke.

Costs: a second rendering model; a JSON contract to version; the version-skew bug class returns on those surfaces; public SEO surfaces must stay server-rendered regardless, so the codebase carries both.

**P04 evidence — the four candidate surfaces were examined individually and none of them qualified:**

- **The request composer** is the strongest case and the one P04 expected to justify Option B. Its state must survive navigation, and it fails Option B on a specific ground: **that state is already a domain aggregate.** `RequestDraft` exists in `ADR-003` precisely so a customer's pre-delivery content is durable, reusable, and recoverable — and before an `Account` exists, browser-local storage covers the anonymous window opened by `WA-05`. Solving draft survival with a client framework would duplicate an aggregate the domain already owns, on the client, where `ADR-011` says validation may not live.
- **The provider response surface** is a form with archetype-conditional fields and at most one optional `PriceStatement`. Conditional reveal is the same problem the composer solves.
- **The conversation view** has no realtime transport by architecture. Without presence, typing, read receipts, or push, it is a thread plus a composer — a form post and a server-rendered list.
- **The operator case queues** are filtered tables with audited actions. They are the surface class server rendering is best at.

### Option C — A full client-side application for all surfaces

Costs: the public discovery surface — the one carrying the acquisition hypothesis — becomes script-dependent, and `D-09`'s URL and redirect requirements move into client routing.

**P04 evidence:** P04 strengthens rather than weakens the case against C. The public surfaces (`UX-01`–`UX-06`, `UX-16`) are the acquisition hypothesis and are bound by rule 4 below; moving them into client routing trades the one thing the product cannot afford to lose for a benefit no surface asked for.

### Option D — Separate frontend deployed independently

**Out of scope here — already rejected by `ADR-013` Option D on deployment-unit grounds, not rendering grounds.**

## Decision

**Recommend Option A: server-rendered views plus progressive enhancement, with exactly three named client islands. `PROPOSED — DAVID APPROVAL REQUIRED`.**

1. **Public surfaces are server-rendered and remain readable without executing script.** Non-negotiable under any option.
2. **Authenticated surfaces are server-rendered with progressive enhancement.** Partial-HTML updates where they earn it.
3. **Three client islands are proposed as a budget, each with a required degradation path.** Like everything else in this record they are a recommendation, not something already approved:

| Island | Surfaces | Why progressive enhancement alone is insufficient | Degradation |
|---|---|---|---|
| `I-1` Governed type-ahead picker (`Category`, `Place`) | `UX-01`, `UX-04`, `UX-07`, `UX-17`, `UX-22` | Per-keystroke lookup against the governed list with ARIA combobox semantics; a full round trip per keystroke is a latency and read-path cost problem | A plain select or a submit-and-choose page over the same governed list |
| `I-2` Composer step controller and local draft persistence | `UX-07` | Anonymous pre-`Account` draft survival under `WA-05`, plus category-conditional question reveal without a round trip per answer | One server-rendered step per POST, draft persisted server-side once an `Account` exists |
| `I-3` Media upload manager | `UX-17`, `UX-23` | Multi-file selection, per-file progress, and the `ADR-018` pre-generated-derivative processing state have no HTML-form equivalent | Single-file form upload with the processing state shown on reload |

4. **Three constraints bind every option and are not negotiable:** authorization is decided in the domain, never in a route guard, a template, or a client (`ADR-011`, `R-026`); there is exactly one machine-access enforcement point (`ADR-012`), and **a JSON endpoint built for an island is a machine-access surface** that must pass through it; and **public discovery pages must remain server-readable**, because the acquisition hypothesis depends on it. This is a product constraint, not a rendering preference.
5. **No JSON API is built speculatively.** `R-012`'s anti-inflation rule binds: no deliverable whose only consumer is a possible future decision.
6. **Option B remains available per surface** and requires no reversal of this record. The bar is stated in the reconsideration trigger.

## Rationale

**The evidence this ADR asked for was produced, and it did not point at a rich client.** The four items required per surface — the interaction requirement evidenced by a named journey, whether client state must survive navigation, whether progressive enhancement was attempted and at what cost, and whether the surface is public — are recorded for **all thirty-seven classified surfaces** in `docs/04-ux/rendering-evidence.md`; `UX-37` and `UX-38` are `FUTURE` and are left unclassified rather than guessed at. **Item three is the honest weakness of this record, and it is why the `HOLD` is only partially discharged:** nothing has been implemented, so "what progressive enhancement cost" is an argued estimate in every row. **Three of four items were delivered; the fourth cannot be delivered before code exists.** That is stated once rather than hedged thirty-seven times, and it is why this remains `PROPOSED` rather than a settled question awaiting only a signature.

**The deciding factor is that the architecture already removed the reasons a rich client usually wins.** No realtime transport means no live thread. No availability model means no calendar widget. No rendered map (`ADR-019` Level 3, recommended in `docs/04-ux/map-decision.md`) means no tile surface — and a map would have been the only plausible surface in the product requiring a **rich** client state model surviving navigation, one with no domain owner and no honest no-script degradation. (`UX-07`'s anonymous browser-local draft also survives navigation, but it has both, which is why it stays `LOCAL`.) That decision and this one are linked and were taken on independent grounds.

**Cost is not the deciding factor and is not presented as one.** All three options render from the same one deployable, on the same instance tier, with the same database. Rendering choice moves no line in `cost-model.md` §3 or §4. What it moves is developer-days and, on interaction-heavy surfaces, the abandonment rate — and `R-022` already records verification abandonment as a measured cost. **A rendering decision taken to save infrastructure money would be optimising the smallest quantity in the model.**

**What genuinely decided it:** rule 4 on the public surfaces; accessibility of dynamic question reveal, which P04 identifies as the hardest interaction problem in the product and which is not made easier by a component framework; `ADR-011` keeping validation in the domain, which removes the "client-side validation is nicer" argument by making a client copy non-authoritative anyway; and one part-time developer maintaining however many rendering models exist.

**Three islands against a ceiling of five is deliberate.** It leaves headroom for one genuine discovery without leaving room for accumulation, and it makes the existing reconsideration trigger measurable from the first build.

## Consequences

Positive: one rendering model plus three bounded exceptions; public acquisition surfaces stay server-readable by construction; the two invariants that matter (`ADR-011`, `ADR-012`) remain binding rather than implied.

Negative: interaction-heavy work on `UX-07`, `UX-19`, and `UX-23` will be more effortful than in a component framework, and that effort is real and recurring. **If P04's judgement about the composer is wrong, it will show up as request-composition abandonment on a specific step** — which is exactly the instrumentation required below, and is the honest way for this decision to be found wrong.

Operational: each island is a second rendering model. The island inventory must be counted by the build, not by memory.

Delivery: build the first surfaces under Option A. Add an island only against its named justification, and never as a convenience.

Migration: none. No surface has been implemented.

## Cost implications

**No infrastructure line changes under any option.** Fixed and variable cost in `cost-model.md` is identical across A, B and C: same deployable, same instance tier, same store, same delivery layer.

Developer-day direction only, and these are informed ranges, not measurements: Option A is the cheapest for form-and-list surfaces, which is most of the inventory; Option B adds roughly a component-framework toolchain plus a versioned contract per surface it covers; Option C additionally re-implements `D-09`'s URL and redirect requirements in client routing. **No dollar figure is stated, because none was verified and none would be honest.**

## Lock-in and exit implications

**LOW–MEDIUM, and per-surface rather than global.** No vendor lock-in under any option. The binding asset is `ADR-011`: as long as authorization and the write path live in the domain behind one application layer, a surface can change rendering model without touching the marketplace logic.

The one genuinely expensive mistake available here is putting an authorization decision into a client or a route guard. That is not a rendering trade-off; it is `R-026`, and it is forbidden under every option.

## Security and privacy implications

A richer client changes where data lands, not who may see it. Under every option: the publication allowlist is applied inside the read (`ADR-012`, sharpened by `ADR-013` into a closed type); contact data resolves through the disclosure decision (`ADR-010`) and never reaches a client that has not passed it; and precise provider base location is never emitted to any projection.

**The rendering-specific trap, restated because the island budget makes it live:** each of `I-1`, `I-2` and `I-3` will want a data endpoint. `I-1` in particular reads the governed `Category` and `Place` lists per keystroke. Those endpoints are machine-access surfaces and must sit behind the same single enforcement point as every other machine access, or `ADR-012`'s deny-by-default guarantee acquires a second door. `I-1`'s endpoint additionally needs the bot mitigation that guards any anonymous, cheap, repeatable read.

## Reconsideration trigger

**This ADR is not closed as an evidence question** — item 3 stays unavailable until code exists, and the first implementation is itself the missing evidence. It is open as an approval, and the standing triggers below apply once approved. Once approved, the standing triggers that reopen a *selected* rendering approach are:

1. A named V1 surface requiring client state that survives navigation, evidenced by a specific journey in `docs/04-ux/` **and** a failed progressive-enhancement attempt with its cost recorded. Option B for that surface, not globally.
2. Measured p75 interaction latency on request submission above the approved budget, attributable to full-page round trips.
3. **A client-island count exceeding five**, which indicates the baseline has been outgrown by accumulation rather than by decision.
4. **New for P04:** approval of a rendered geographic surface under `ADR-019` Level 3. That surface would be the first plausible candidate for client state that survives navigation, and it re-opens this record for that surface alone.
5. Request-composition abandonment concentrating on a single composer step, which would falsify the `RequestDraft`-solves-it argument in Option B above.

## Validation

Required instrumentation once any surface ships: per-flow interaction latency at p75 and p95, tagged with the surface name; **a counted island inventory produced by the build**, against the budget of three and the ceiling of five; and request-submission abandonment segmented by surface **and by composer step**, because `R-022` makes abandonment the measurable consequence a rendering choice actually has, and because trigger 5 depends on the step-level segmentation.

Acceptance checks: no authorization decision exists in a template, a route guard, or a client; every JSON endpoint added for an island passes through the single machine-access enforcement point; every public discovery URL renders its governed content without executing script; and each of the three islands has a demonstrated working degradation path.

---

*Record dates — ADR created 2026-08-12 (P03.1), splitting the rendering portion out of `ADR-013` (authored 2026-08-11, P03). Substantively rewritten 2026-08-12 (P04) when the per-surface interaction evidence it required was delivered; status moved from `PROPOSED — DEFERRED, PENDING P04 UX VALIDATION` to a `PROPOSED` recommendation of Option A. No new vendor or price research was performed for this record, and no surface has been implemented.*
