# ADR-013 — Application platform: JVM/Spring server-rendered single deployable, with build-failing module-boundary enforcement

- Status: PROPOSED
- Date: 2026-08-11
- Decision owner: David (technical decision owner for this repository)
- Related evidence/requirements: `docs/03-technology/technology-evaluation.md` §1.1, §4.9, §4.10; `docs/02-architecture/p03-decision-inputs.md` `D-09`, `D-10`; `ADR-001`, `ADR-011`, `ADR-012`; `system-architecture.md` §3, §4; `domain-map.md` forbidden-dependency table; `R-007`, `R-032`, `R-034`, `R-035`, `R-036`, `R-047`; `SRC-013`
- Supersedes / superseded by: N/A

## Problem / context

`ADR-001` recommends one deployable modular application and states that the decision is only cheap **if boundaries are mechanically enforced** — *"a forbidden cross-module import must fail the build"*, without which the ADR is *"claimed rather than implemented"* (`R-032`). `ADR-001` further asks P03 to report whether enforcement cost scales with boundary count, and to revisit the **boundary set** if it does.

P03 must therefore select a language, a framework, a web-rendering approach and an enforcement mechanism together, because in practice they are one decision.

Evidence status: the marketplace loop and its invariants are `PROPOSED` (`TECHNICAL_DISCOVERY`). **There is no traffic evidence at all** — `SRC-006` is NOT RECEIVED. Team size is `CONFIRMED` small. David's platform experience is `CONFIRMED` and is an evaluation input only (`R-007`).

## Constraints

One part-time developer who also operates the system. Modelled load peaks **under about ten requests per second at the Growth scenario**, so throughput is not a discriminator. No realtime and no server-initiated push (`system-architecture.md` §3). Ten module boundaries with a 13-row forbidden-dependency table. Transactional workflows in §4 including a three-phase request submission and an atomic provider response. Machine access defaults to deny with one enforcement point (`ADR-012`). Locale-distinct public URLs with retained redirect history. Explicit cost discipline.

## Options considered

### Option A — Kotlin on Spring Boot 4.1 / Java 25 LTS, server-rendered views with progressive enhancement, one deployable (recommended)

Templates in the framework's own view layer, htmx for partial updates, client-side islands only where a genuinely stateful surface demands it. Out-of-band work as a separate process from the same artifact. Boundaries enforced by module verification plus architecture-rule tests plus a database-catalog test.

Benefits: a first-party modular-monolith product whose three verification rules map almost one-to-one onto `domain-map.md`; a framework-native transactional outbox that answers `D-04` with no new always-on component; transaction semantics matching §4 natively; the longest verified platform support horizon; server-rendered HTML makes every `D-09` requirement cheap and eliminates the version-skew bug class.

Costs: the highest memory floor of the candidates — immaterial at these volumes but front-loaded where the budget is tightest; and roughly one framework upgrade per year, forever (`R-036`).

### Option B — C# / .NET 10, ASP.NET Core

Rejection factors: no first-party equivalent to the modular-monolith or outbox products, both of which would be bespoke; a shorter platform support horizon; and a verified default that cuts against the decisive criterion — **project references are transitive by default**, so a module compiles against its dependency's dependencies. Closable, but **opt-out**, where the recommended build tool is closed by default. Given P02's own warning that *"deadline pressure attacks boundaries first"*, a default that leaks is a real difference. **This option lost narrowly and should be re-run if any of Option A's four grounds is falsified.**

### Option C — TypeScript full-stack framework doing both tiers

Rejection factors: its own vendor documentation states the backend capabilities *"are not a full backend replacement"*, which means the domain lives elsewhere — the two-deployable shape — or in a layer its vendor says is not one. A verified record of removals, renames and behaviour changes in its latest major. Self-hosting requires a shared cache handler to make caching **correct** across replicas, which P20 turns from optional into mandatory — a new always-on component.

### Option D — Separate JS frontend plus backend API (two deployables)

Rejection factors: `ADR-001` names the first additional deployable unit as by far the most expensive increment available, and this applies it to the tier where it buys least at volumes where it buys nothing. URL identity acquires two owners, and `ADR-012`'s single machine-access enforcement point acquires two candidate locations.

### Option E — Go, Python, Ruby, Elixir

Rejection factors recorded per language in `docs/03-technology/technology-radar.md`. The common thread: either no compilation step, so enforcement degrades to a lint gate; or a boundary primitive that cannot express a directed graph of ten siblings with a named forbidden-edge list; or a flagship interaction model requiring a persistent per-client connection, which contradicts §3. **GeoDjango is recorded as a genuine loss.**

## Decision

Option A, with **three enforcement layers, all failing the build**:

1. **Module verification with explicit allowed-dependency declarations on all ten modules** — a literal transcription of `domain-map.md`'s forbidden-dependency table.
2. **Architecture-rule tests** for what module verification cannot express: no module *reads* Analytics; no authorization decision in a controller or template; no module imports the intent mapper; no transactional method touching two modules' repositories.
3. **A database-catalog test asserting that no foreign key crosses a module schema boundary** (`R-034`).

Plus two disciplines: **the boundary rule files are protected**, so weakening `ADR-001` is visible in the history rather than invisible in a diff; and **the publication allowlist is a closed generated type, not a query predicate**, so the compiler enforces `ADR-012` where no static-analysis tool can.

**Approval still required:** David.

### Answer to `ADR-001`'s open scope note

**Enforcement cost does not scale meaningfully with boundary count at ten.** The marginal cost of a boundary is one annotation. The recurring cost is that every legitimate new edge requires an explicit declaration — **which is exactly the friction the ADR wants.** The answer depends on the *mechanism*, not the count, which reverses the ADR's implied direction. **The boundary set does not need revisiting on enforcement-cost grounds.**

## Rationale

**Two grounds carry this decision. An earlier draft claimed four, and adversarial review was right that two of them were padding and a third was contradicted by this phase's own `R-036`. The case is narrower than first written — and stating it narrowly is the point, because `R-007` is exactly the risk of a case that *looks* independent while resting on one leg.**

**Ground 1 — a first-party product built for exactly `ADR-001`'s shape.** Its declared-dependency rule transcribes `domain-map.md`'s forbidden-dependency table directly, module by module, at one annotation per boundary. **This is genuinely differentiating**: no other evaluated ecosystem ships a first-party modular-monolith verifier, and it is what turns P02's *"the one place P03 should be willing to spend"* from a bespoke build into a library plus a test.

**Ground 2 — the dependency configuration is closed by default.** A module compiles only against what it declares. The runner-up's project references are **transitive by default** — A compiles against C — which is closable but **opt-out**. Given P02's own warning that *"deadline pressure attacks boundaries first"*, a default that leaks is a real difference on the criterion this ADR exists to serve.

**Grounds explicitly withdrawn, because they do not discriminate:**

- *A framework-native transactional outbox answering `D-04` with no new always-on component.* **True here, and true in every candidate ecosystem** — each has a mature database-backed job library with the same property, and at least one ships it first-party. It is a reason the decision is *comfortable*, not a reason it is *right*.
- *Post-commit transactional event semantics matching §4.* **Also available everywhere**, under different names. Same status.
- *The longest platform support horizon.* **Withdrawn as stated, because it substitutes the language runtime's horizon for the framework's, and the framework is the binding constraint.** `R-036`, recorded by this same phase, states the recommended framework line carries roughly a 12-month open-source window on a ~6-month cadence — about one upgrade per year, forever, for one part-time developer — and `technology-radar.md` concedes the runner-up's cadence is gentler. **On maintenance cadence the recommendation is worse, not better, and that is recorded rather than netted out.**

**One asymmetry in the runner-up's rejection, named so it is not hidden.** The runner-up loses partly for having *"no first-party equivalent… both of which would be bespoke"* — while the recommendation itself relies on third-party architecture-rule tests, a third-party job library, and a third-party progressive-enhancement library. **The "first-party or bespoke" standard is applied to one side only, and it should not be.** Ground 1 survives that objection because a first-party *modular-monolith verifier* has no third-party equivalent in either ecosystem; the outbox and event grounds do not, which is why they are withdrawn above.

On rendering: §3 already established no realtime and no server-initiated push, which is precisely the workload server-rendered HTML is optimal for. And `ADR-001`'s asymmetry argument applies here too — **adding a JSON API and a client later is additive if `ADR-011` holds; removing one is not.** Start on the recoverable side.

**`R-007` discipline, stated honestly.** This recommendation aligns with David's background, and after the withdrawals it rests on **two** grounds rather than four. Both are properties of the tooling that anyone can check. **If Ground 1 is falsified — if the declared-dependency mechanism cannot express the forbidden-dependency table — the recommendation should fall, and familiarity must not rescue it.** Ground 2 alone would not carry a VERY HIGH reversal decision.

## Consequences

Positive: one artifact, one language, one log stream, one rollback; synchronous invariants stay simple; every `D-09` requirement is server routing plus governed data; no version-skew bug class; the enforcement gap in `ADR-001` is closed rather than asserted.

Negative: the highest memory floor of the candidates; roughly annual framework upgrades for one person (`R-036`); and a hiring pool narrower than the JavaScript ecosystem, though Java developers read Kotlin.

Operational: the boundary check runs in the ordinary test task as a **required status check**, so a violation is indistinguishable from a broken test rather than becoming background noise under deadline pressure.

Delivery: **expand/contract migration discipline is mandatory** (`R-047`) — old and new code share one schema during a rolling deploy. Forward-only migrations, a one-release gap before destructive changes, migrations as a pre-deploy step, and each migration tested against the previous release's code.

## Cost implications

Build: the ten-module scaffolding once; server-rendered templates plus progressive enhancement is less total code than a single-page application. Fixed: one instance tier, memory-driven. Variable: none added per user action. Operator: lowest of the options — one investigation surface. Recurring: roughly one framework upgrade per year, which must be **budgeted, not discovered**. Figures in `docs/03-technology/cost-model.md`.

## Lock-in and exit implications

Vendor lock-in: none — the artifact is a container and the runtime is open source. Internal lock-in: the enforcement layers are what keep module extraction cheap, and they are only effective while the rule files stay protected. **Reversal difficulty is VERY HIGH for the language and framework — effectively a rewrite — and deliberately LOW–MEDIUM for the rendering approach.** The decision therefore carries most scrutiny at approval and least tolerance for revisiting later.

## Security and privacy implications

`ADR-011` requires authorization decided in the domain, not in a route guard — and a single deployable makes a channel-level guard *look* sufficient, which is precisely the trap (`R-026`). Architecture-rule layer 2 asserts against it mechanically. The allowlist-as-a-type sharpening converts `ADR-012`'s central guarantee from an unverifiable review obligation into a build failure. **And `R-035` must be honoured: robots directives are not enforcement — one governed policy, two derived artifacts, with a deploy-time divergence check.**

## Reconsideration trigger

**Before first production deploy:** if a build-time spike shows the declared-dependency mechanism cannot express three or more edges of `domain-map.md`'s forbidden-dependency table without bespoke rules, Ground 1 is falsified and Option B should be re-run.

**After first deploy — and this is an ACCEPTED RISK, not a trigger.** `AGENTS.md` requires a measurable reconsideration trigger for every material decision, and `p03-decision-inputs.md` requires one that is *"a condition someone could observe."* **This ADR does not have one after deploy, and that inverts the risk gradient: every LOW-reversal decision in P03 carries a permanent measured trigger, while the VERY HIGH one carries a pre-deploy spike.** Stating it as a trigger would be dishonest, because no observable condition would realistically justify a rewrite at this team size.

**So it is recorded as an accepted risk with a named consequence instead:** the platform choice is treated as irreversible after first deploy and is *managed* — through the boundary enforcement in the Decision, which is precisely what keeps module extraction available if the deployment shape ever needs to change, and through budgeting the `R-036` upgrade cadence rather than discovering it. **What P03 buys with this ADR is not the ability to change the platform later; it is the ability to change everything else.** The approval decision should be taken on that basis.

Rendering, any one: a named V1 surface requires client state surviving navigation, evidenced by a specific journey **and** a failed progressive-enhancement attempt; measured p75 interaction latency on request submission above the approved budget, attributable to full-page round trips; or the count of client-side islands exceeding five.

Enforcement: more than two boundary violations reach the main branch in a quarter, or a second developer joins — either promotes the highest-pressure boundaries to build-tool subprojects, which compose with the current mechanism.

## Validation

Verified 2026-08-11 from primary sources: the framework line's current release and support baseline; the module-verification library's current release; and that its verification call **throws, failing the build**, on module cycles, internal-package references, and undeclared dependencies. Required instrumentation: a counted log of boundary-rule failures at the check stage, a counted log of violations detected post-merge, per-flow interaction latency, and a counted island inventory in the build.
