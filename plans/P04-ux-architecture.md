# P04 — UX Architecture

## Metadata

- ID: P04
- Title: UX Architecture
- Owner: David
- Status: COMPLETED
- Authorization: explicit David authorization (`SRC-015`), granted **with `G-06` still unsatisfied** and with a standing instruction to design the primary working UX scenario without waiting for the remaining owner answers.
- Baseline: P03.1 complete at commit `79b1f56`. P00, P00.1, P01, P01.1, P02, P03, P03.1 COMPLETED. P05–P07 NOT STARTED.
- UX status produced by this phase: `PROPOSED`. **This phase approved nothing and satisfied no owner gate.**

## Objective

Design how Superola's V1 marketplace actually works for customers, providers, and marketplace operators — translating P01's launch slice and P02's domain architecture into journeys, information architecture, a surface inventory, interaction and state design, and the accessibility and responsive baseline.

Produce the UX evidence that the technology track deliberately withheld from itself: `ADR-020` (web rendering) and `ADR-019` Level 3 (whether any geographic surface is rendered), plus a product disposition on AI-assisted request intake.

**Architecture before aesthetics.**

## Non-goals

- Final visual design. No color, typography, branding, iconography, or component library was selected, and none may be inferred from any P04 artifact — including the wireframes and the HTML walkthrough.
- Database tables, schemas, or implementation.
- Changing `ADR-013`'s platform decision.
- Resolving `G-06`, `Q-007`, `Q-008`, `Q-026`'s disclosure limb, `Q-031`, or contact-disclosure policy.
- Designing booking, payment, payout, refund, dispute, or review workflows. P04 shows where they would attach and stops.
- Designing the legacy migration. P04 records its UX impacts for P05 and stops.
- Selecting an LLM, a map vendor, or any other vendor.
- Starting P05, P06, or P07. **The polished Decision & Cost Explorer remains a P07 deliverable and was not built.**
- Pushing, creating or modifying remotes, or touching historical stashes.

## Context and authoritative inputs

| Input | Why it is authoritative here |
|---|---|
| `docs/05-roadmap/mvp-scope.md` | The B0 envelope. It charters three P04 comparisons **by name**: pre-submit versus guest-then-verify verification, the universal versus category-gated response shape, and the operating envelope's queue requirements. |
| `docs/01-product/user-journeys.md` | The `PROPOSED` product states P04 turns into surfaces. Its own header assigns interaction architecture to P04. |
| `docs/02-architecture/domain-model.md` | Entity names, lifecycle states, and transition vocabulary. **P04 renders these; it does not rename them.** |
| `docs/02-architecture/adr/ADR-020` | States the four per-surface items P04 must deliver to close it. |
| `docs/02-architecture/adr/ADR-019` Level 3 | The map question, moved to P04 by P03.1 with the invariant retained and the product conclusion withdrawn. |
| `docs/03-technology/ai-evaluation.md` §8.1 | The assisted-intake experiment, explicitly for P04 to accept or reject, with the precondition that the guided form must ship first. |
| `docs/01-product/owner-reconciliation-matrix.md` | The release gate. **P04 read it as a readiness test and recorded that it did not pass it.** |
| `AGENTS.md` | Evidence labels, provenance, language policy, prefix policy, currency, and the multi-agent rule that specialist output is advisory until integrated. |

## Known constraints and evidence labels

- **`G-06` is UNSATISFIED and P04 did not treat it as satisfied.** The gate's own text says it must be resolved *"before P04 designs profile and request surfaces."* P04 designed them anyway, on David's explicit authorization, against the working assumption `WA-01`, and **every artifact records that this is an assumption and not an answer**. `Q-007` remains OPEN.
- **The rest of the gate map is unchanged from P02 and P04 did not move it.** Per `docs/02-architecture/decision-branches.md`: satisfied — `G-02`, `G-03`, `G-08`, **and satisfied through the bounded-scenario clause rather than through owner answers**; partial — `G-01`, `G-04`, `G-05`, `G-07`, `G-09`; unsatisfied — `G-06`, `G-10`. `G-07` matters most to P04: it fixes the customer identity **boundary** (`DB-12`), and the **timing** limb inside it (`Q-018`) is exactly what `WA-05` proposes and does not settle.
- **`SRC-004` — the owner's existing UI/UX materials — is NOT RECEIVED.** `OR-016` requires reconciliation before any P04 authorization; that reconciliation has not happened. **No design claim in P04 derives from prior owner UX work, and owner reconciliation may confirm, extend, contradict, or replace any of it.**
- **`SRC-006` is NOT RECEIVED.** There is no traffic evidence and no usability evidence. **Nothing was implemented and no user was observed**, so every interaction-cost and friction claim in P04 is an argued estimate, and P04 says so in each document rather than hedging every sentence.
- Five working assumptions carry the phase: `WA-01`–`WA-05`, recorded in `docs/04-ux/README.md`. Four are `DAVID_DIRECTIVE`; `WA-05` is P04's own recommendation and is the one most likely to change.
- The GigSalad request-flow capture is **comparative input supplied by David**: Evidence `OBSERVED`, Provenance `DAVID_DIRECTIVE`, registered as part of `SRC-015`, recorded at step-shape granularity only, **not independently re-verified against first-party sources during P04**, and **no Superola requirement derives from it**.

## Tasks and delegation strategy

The primary agent was the canonical writer and integrator. Fifteen advisory agents ran: three input-synthesis agents, ten writer agents on disjoint files, and two independent blind adversarial reviewers. **All writer output was advisory until integrated, and the primary agent corrected it where it was wrong** — see the integration corrections below.

- [x] Synthesize P01, P02, and P03 inputs into a binding canon (vocabulary, constraints, decisions, house style) so that ten parallel writers could not diverge.
- [x] Create branch `p04-ux-architecture`.
- [x] Write `docs/04-ux/README.md` — the working-assumption register and artifact map.
- [x] Write the customer journey and its thirteen exception journeys.
- [x] Write the request-intake architecture: three intake models compared, the GigSalad disposition table, the `RequestDraft` user model, the universal-versus-category classification, and per-archetype question sets.
- [x] Write discovery, results, zero-result and degraded-input behaviour.
- [x] Compare five customer-verification timings and recommend one.
- [x] Write the provider profile, onboarding, and workspace architecture, including the dual-role UX that discharges `ADR-004`'s assigned consequence.
- [x] Write RFQ send, provider response, conversation, notification bodies, and outcome capture.
- [x] Write the operator surfaces and the anti-inflation test.
- [x] Write the information architecture and the thirty-nine-surface inventory.
- [x] Produce the per-surface rendering evidence and recommend an `ADR-020` option.
- [x] Decide `ADR-019` Level 3.
- [x] Write the accessibility and responsive baseline.
- [x] Run a cost and complexity review against P04's own proposals.
- [x] Produce low-fidelity structural wireframes and a self-contained HTML walkthrough.
- [x] Produce owner-facing bilingual diagrams and the bilingual UX preview.
- [x] Write the P05 and P06 handoff.
- [x] Update `ADR-020`, `ADR-019`, the ADR index, the assumption, risk, question and source registers, `plans/README.md`, `README.md`, and `CHANGELOG.md`.
- [x] Remove the four P04 placeholder files, including `vendor-flow.md`, whose name the glossary had already flagged as retired vocabulary.
- [x] Run two independent blind adversarial reviews, resolve CRITICAL and HIGH, and record every finding rather than summarising it away.
- [x] Validate; run `git diff --check`; commit. **Do not push.**

## Decisions made or ADRs required

**No approvals.** Every decision below is `PROPOSED` and requires David's individual approval.

| Decision | Outcome | Record |
|---|---|---|
| Request intake model | **Progressive**, governed by `CategoryArchetype`. Traditional universal intake `REJECTED` as the primary model. | `docs/04-ux/request-intake.md` |
| `RequestDraft` user model | Local and anonymous at the first answer; promoted to a server draft when an `Account` exists. Account creation is never required to browse, search, view a profile, or begin a draft. | `docs/04-ux/request-intake.md` §4 |
| Customer verification timing | **Guest-then-verify** (`WA-05`). Recommendation against `Q-018`; does not resolve it. | `docs/04-ux/identity-and-verification.md` |
| Web rendering | **`ADR-020` recommends Option A** — server rendering plus progressive enhancement plus three named client islands. **No V1 surface requires client state that survives navigation.** | `ADR-020`, `docs/04-ux/rendering-evidence.md` |
| Rendered geographic surface | **`ADR-019` Level 3 answered: no rendered map in V1**, deferred with three named triggers. | `ADR-019`, `docs/04-ux/map-decision.md` |
| AI-assisted request intake | **`FUTURE`, not V1.** Worth testing later under four named conditions. Does not reverse `ai-evaluation.md` §10. | `docs/04-ux/request-intake.md` §9 |
| Facet counts | **Recommendation: none live in V1.** `Q-035`'s owner is P04 **plus David**; P04 recommends and David decides. | `docs/04-ux/discovery-and-results.md` |
| Dual-role UX | Two persistent contexts in one `Account`. No mode toggle, no account type, no second account. | `docs/04-ux/provider-workspace.md` |

**No new ADR was created.** `ADR-020` was rewritten to record the evidence it asked for; `ADR-019` Level 3 was answered inside the existing record. A new ADR would have created a second place where the same decision lives.

### The decision P04 most expected to go the other way

**P04 expected the request composer to justify a rich client, and it did not.** The composer is the one V1 surface whose state must survive navigation. It fails to justify Option B for a specific reason: **that state is already a domain aggregate.** `RequestDraft` exists in `ADR-003` precisely so pre-delivery content is durable and reusable, and browser-local storage covers the anonymous window `WA-05` opens. Solving it with a client framework would duplicate an aggregate the domain already owns, on the client, where `ADR-011` forbids validation from living.

**The second-order consequence is the interesting one.** Three architecture decisions taken for unrelated reasons — no realtime transport (`system-architecture.md` §3), no availability model (`ADR-005`), and no rendered map (`ADR-019` Level 3) — removed the three surfaces that most often force a rich client on a marketplace: the live thread, the calendar, and the tile map. **Option A is not winning on its merits so much as on the absence of the surfaces that would beat it**, and that is worth stating plainly, because it also names exactly what would reopen the decision.

## Open questions and approval gates

All prior gates unchanged. **P04 resolved no question in the register.** It recorded recommendations against `Q-018`, `Q-020`, and `Q-035`, and raised five new questions.

| ID | Question | Owner |
|---|---|---|
| `Q-036` | How long does a `ServiceRequest` remain in `PendingVerification` before it becomes `Abandoned`? | David + P06 |
| `Q-037` | May a `ReportedOutcome` carry an optional secondary reason, and who approves the vocabulary? | David, on the `ADR-003` record |
| `Q-038` | Is browser-local storage of an anonymous `RequestDraft` acceptable, does it require disclosure, and where does it count as processing? | David + counsel |
| `Q-039` | What triggers the outcome prompt, how often, and at what point does asking again become pressure? | David + P06 |
| `Q-040` | May a provider-declared price indication appear on a public surface? | David |

**`Q-040` is a conflict P04 found rather than inherited.** `DB-02` says no money-shaped language appears on public pages; `domain-model.md` gives `ServiceOffering` an optional price indication as a V1 attribute. Two P02 documents disagree, no P03 document noticed, and **P04 records its reading without adopting it as settled.** `R-056` carries the presentation rules that bind either way.

**Items owed to a named owner before this work is built:** the `NoResponse` window (`POLICY PENDING` since P02), the `RequestIntake` decay window, the freshness and stale-escalation windows, `Q-036`'s verification window, and — from `mvp-scope.md`'s existing operating-envelope requirement — a named owner, escalation path, backlog-age measure, resolution-time measure and stop rule **for every one of the nine operator surfaces**, before launch approval rather than after (`R-053`).

## Validation and acceptance criteria

| Check | Result |
|---|---|
| One primary customer journey and one provider journey exist. | **PASS.** `docs/04-ux/customer-journey.md` with thirteen exception journeys mapped to the `ServiceRequest` lifecycle; the provider journey is carried by `provider-onboarding.md` and `provider-workspace.md` and drawn in `diagrams/journeys/provider-journey.md`. |
| `RequestDraft` UX is defined without designing a database. | **PASS.** `request-intake.md` §4 — when a draft begins, when it becomes a server draft, resumability, category-change behaviour. No table, no schema. |
| `EventType != ServiceCategory` survives, and a second service for the same event costs no re-entry. | **PASS.** `request-intake.md` §5 reseeds a new draft from the prior `EventContext` with `eventGroupingHint`. **No `Event` aggregate and no `Event` box in any diagram.** |
| Universal versus category-specific questions are defined and governed. | **PASS.** Five classes, a master table, and per-archetype question sets derived from `CategoryAttributeDefinition` rather than hand-built forms. The class formerly called `FUTURE` was renamed `NOT-V1` on review, because a class value must not collide with a governed evidence label. |
| Zero-results experience is defined. | **PASS.** *No supply in this category* and *no supply in this area* are separately recorded; relaxation names the relaxed constraint; unmet demand is captured with **no notification promise**, because `Q-031` is unratified. **A query is never converted into a broadcast.** |
| Provider profile architecture exists, with the privacy invariant intact. | **PASS.** Element-by-element allowlist; precise base location never exposed; EXIF stripped; `Suspended` indistinguishable from `Deactivated` to a customer. |
| RFQ, quote, conversation and outcome surfaces exist. | **PASS.** Including `NoResponse` as a **system-observed fourth outcome rather than a provider action**, and no accept button that creates an obligation. |
| Provider onboarding separates account from publication. | **PASS.** Account is one verified contact channel; discovery requires the full archetype-aware `publicationGateMet`. |
| Dual-role UX exists. | **PASS.** Two persistent contexts, no toggle, no account type — discharging the consequence `ADR-004` assigns to P04 by name. |
| Map decision made or precisely deferred. | **PASS.** `ADR-019` Level 3 answered: no rendered map in V1, three named triggers, and the privacy invariant explicitly **not** used as the reason. |
| AI-assisted intake has a product disposition. | **PASS.** `FUTURE`, four conditions, `ai-evaluation.md` §10 not reversed, no model selected. |
| `ADR-020` has a recommendation based on actual surfaces. | **PASS, with a stated limit.** Option A, argued per surface over thirty-nine surfaces. **Three of the ADR's four required items were delivered; the fourth — what progressive enhancement cost — is unavailable until code exists**, so the ADR records the `HOLD` as *partially* discharged rather than closed. |
| Responsive and accessibility requirements exist. | **PASS**, with no compliance certification claim anywhere. The provider request-response surface is named as the highest-risk mobile surface; operator queues are stated as the one desktop-first class rather than assumed. |
| Visual diagrams exist. | **PASS.** Four bilingual owner-facing sources with Mermaid plus precise Excalidraw build specifications, and one hand-authored `.excalidraw` scene **validated as JSON by both Node and Python**. The repository has no diagram toolchain; **the Mermaid was reviewed by hand and not machine-validated**, and that is recorded rather than implied. |
| Low-fidelity wireframes exist. | **PASS.** Ten surfaces at desktop and phone width, grayscale ASCII, with a seventeen-row constraint table. No color, typography, or component library. |
| Owner-facing bilingual preview exists. | **PASS.** `presentation/ux-preview-v0.1.md`, business language, registered in `presentation/sources.md`, and carrying **no price, percentage, response time, or traffic figure**. |
| P05 and P06 handoff exists. | **PASS.** `H5-01`–`H5-10` for P05; counts rather than estimates for P06, with an explicit prohibition on deriving developer-days from them. |
| No final branding invented. | **PASS.** Zero hex values, font names, spacing scales, or component-library names in `docs/04-ux/`. The two exceptions are declared: the diagram build specifications and the HTML prototype, both of which now state that their values are neutral placeholders and that **no product surface may take a color, font, or measurement from them.** |
| No payment or booking UX entered V1 silently. | **PASS.** `UX-38` exists as a `FUTURE` attachment point with no design behind it. |
| Human and infrastructure cost never summed in a headline. | **PASS**, after a fix. Three P04 locations published the modelled operator hours without the adjacent rate and workload assumptions `AGENTS.md` requires; all now carry them, and the handoff names its single reproduced figure as an exception rather than contradicting its own prohibition. |
| `git diff --check` passes. | **PASS.** |
| Adversarial review resolved at CRITICAL and HIGH. | **PASS on the second pass. Both reviews returned FAIL on the first.** See below. |

### Adversarial review — two independent blind reviews, both FAIL on the first pass

Two reviewers ran concurrently and without sight of each other: one on UX substance, one on cross-document truth and repository discipline. **The UX reviewer returned one CRITICAL and twelve HIGH. The discipline reviewer returned five CRITICAL and twenty-one HIGH.** Findings are recorded rather than summarised away.

| Finding | Severity | Resolution |
|---|---|---|
| **P04 cited a binding document that existed only in chat.** Sixty-three normative citations pointed at "the P04 canon" §2, §4, §5.1–§5.18, §6 — the source of the rendering-class definitions, the wireframe constraint set, and the map argument. `AGENTS.md`: *"Repository state outranks chat history."* | **CRITICAL** | Persisted as `docs/04-ux/design-canon.md` with the cited section numbering intact, and every citation rewritten to a resolvable path. **The alternative — re-anchoring sixty-three citations to upstream records — would have lost the one document that records what the parallel writers were actually told.** |
| **`rendering-evidence.md` quoted `ADR-020` "verbatim" from the version P04 itself had overwritten in the same phase.** Six quoted strings returned zero hits in the current record and one hit each in `HEAD`. Two of them are the justifications for declining Option B and burying Option C. | **CRITICAL** | Requoted from the current ADR and verified by grep. **This is the failure mode the repository's own discipline exists to prevent, committed by the phase that rewrote the record.** |
| **The `CHANGELOG` deliverable was claimed and not written**, while the plan carried a checked task saying it had been. | **CRITICAL** | Written. |
| **`p05-p06-handoff.md` forbade itself three times and then published the forbidden figure forty-three lines later** — "no developer-day, hour, or currency figure appears in this section", followed by the modelled operator hours inside that section. | **CRITICAL** | The exception is now named in the prohibition itself: the figure is **reproduced** from `cost-model.md` §5.2, is not P04's, was not derived from P04's queue set, and carries its rate and volume assumptions. |
| **Report and block were drawn as actions on anonymous public surfaces with no surface record behind them**, while `domain-model.md` requires a `Report` from an authenticated participant — leaving an unresolved choice between a login wall on the acquisition surface and an operator queue unbounded by design. | **CRITICAL** | `UX-39` added: the public affordance routes through sign-in. **An authentication requirement on a reporting action, not a login wall on discovery** — and it is what bounds `UX-27`. Every count in the phase was recomputed from the actual rows. |
| **The phase's one quantitative claim was computed on an uneven basis.** "6–9 answers" counted Superola **net** of identity against a competitor comparator **gross** of it. By `request-intake.md` §6.1's own class definition, the identity items block submission. | **HIGH** | **Withdrawn rather than defended.** The corrected figures are 5 composer answers on the discovery path, up to 8 on direct arrival, and **10–11 total blocking items**, propagated to nine documents including the owner-facing preview. **The corrected number is worse for P04's argument and it is the true one.** |
| **The friction target silently assumed one entry path.** A customer arriving directly at a provider profile — the acquisition hypothesis — has none of the three reused answers. | **HIGH** | Both entry paths stated separately, with abandonment instrumented by entry path. |
| **A usability finding asserted from a capture that asserts nothing** — "the highest-friction question in the observed competitor capture", from material recorded at step-shape granularity and never usability-tested. | **HIGH** | Superlative deleted in both places. The three structural arguments stand without it. |
| **The capture was cited by the owner-facing preview while `request-intake.md`'s own guard said it must be filed first.** | **HIGH** | Filed as `docs/07-research/gigsalad-request-flow-capture.md`, and registered in `presentation/sources.md`, which had been bypassed for the phase's only owner-facing document. |
| **The gate map was wrong in both directions** and silently dropped `G-07` — the customer identity boundary, the gate `WA-05` sits inside. | **HIGH** | Replaced with `decision-branches.md`'s statuses verbatim, noting they were satisfied through the bounded-scenario clause rather than by owner answers, plus an explicit `G-07` row. |
| **`WA-05` was stamped as a David working assumption** in three places, converting P04's own recommendation on the funnel's most consequential choice into a directive. | **HIGH** | Restamped `PROPOSED` / `TECHNICAL_DISCOVERY` everywhere, and the status column's invented value replaced with the governed `ASSUMPTION` label. |
| **`ADR-020` said three islands "are approved"** in a record whose status is `PROPOSED — DAVID APPROVAL REQUIRED`. | **HIGH** | "are proposed". **The one sentence in the corpus a reader could have taken as an approval David gave.** |
| **`ADR-020` misstated its own evidence** — 33 of 35 surfaces where the inventory says 34 of 35, `I-1` scoped to four surfaces where the evidence says five, and a completeness claim covering two `FUTURE` surfaces that carry no record. | **HIGH** | All corrected against recounted rows: 39 surfaces, 36 V1, 35 rendered, 24 `DOC` + 11 `LOCAL`, 37 classified. |
| **The rendering decision's load-bearing question was answered "Yes" in one document and "No" in the other**, and the contradiction was dissolved by redefining "client state" mid-argument — the exact circularity a reviewer attacks. | **HIGH** | One sentence, identical in both: state survives navigation, it is held by `RequestDraft` plus browser-local storage, therefore no *client-held* state must survive. |
| **The anti-monetization invariant was cited to the wrong ADR** in two places, including the P06 gate table, and the six `EligibilityDecision` inputs were cited ten-plus times to a record that does not enumerate them. | **HIGH** | Corrected to `ADR-008` and to `domain-model.md` §1. **Correct claims, wrong records — which is worse than an unsourced claim, because it survives a spot check.** |
| **The map document's strongest section opened on a count it did not support** — "four of the five archetypes" followed by three. | **MEDIUM** | Corrected, and `MR-01`'s absolute "the customer never picks a provider by scanning a map" softened to the argument the same document later concedes it is. |
| **`ADR-019` Level 3 read as decided rather than recommended**, with the `PROPOSED` qualifier 114 lines below the decision heading. | **HIGH** | Reframed as a recommendation at the heading. |
| **Two diagram sources contradicted their own scope statement**, denying they decided color or typography while supplying hex values and font sizes; one cited a file P04 had deleted; two used non-enum values in an evidence column. | **HIGH** | Build specifications rescoped as monochrome reproduction specs whose values no product surface may take, made English-only per the internal-production-support rule, the deleted-file citation replaced, and the enum violations corrected. |
| **"Nine queues" denoted three different sets** across seven documents, one of which counts an index surface as a queue and another double-counts audited access. It is a permanent risk entry, a launch checklist, and an owner-facing diagram. | **HIGH** | Canonicalised: **eight case queues plus one index surface, on nine surfaces; `UX-29` carries two case kinds.** |
| **An accessibility completeness claim its own table did not support** — "the complete V1 status set" omitting eleven `ServiceRequest` states rendered on customer surfaces, while the same document says a status rendered without text does not ship. | **HIGH** | Claim downgraded and the three missing lifecycle families carried by reference, with the per-state text named as a P05 authoring task rather than assumed done. |
| **A launch-approval gate stated imperatively**, which P04 has no authority to set. | **MEDIUM** | Reframed as a P04 recommendation over `mvp-scope.md`'s existing requirement. |
| **`PROPOSED` sources described as binding** in a thirteen-row "constraints this document is bound by" table. | **MEDIUM** | Retitled to "constraints this document adopts", with every cited record noted as itself `PROPOSED`. |
| **An operator obligation asserted outside the queue count and called "already budgeted"** with no cost-model line behind it. | **MEDIUM** | Folded into `OQ-08` and the budget claim deleted. |
| **The classification scheme was counted three ways** — five classes, four values, "three governed tiers" — in the document that calls it the admission test. | **MEDIUM** | Settled at five, and the `FUTURE` class renamed `NOT-V1` because a class value must not collide with a governed evidence label. |
| **An invented register** — "the never-publicly-exposed list" — cited in two documents that cite only each other. | **MEDIUM** | Repointed at `domain-model.md` §1.9, `security-privacy-architecture.md` §12, and `ADR-012`'s allowlist. |
| Assorted: a "6–9 answers" figure surviving in eight documents; a phone-width count that double-counted a notification body; an "at least 1" floor under-reporting its own basis; a `Quote`-named CTA as the product's most-rendered string; an explicit "prefer not to say" rendered as a blank; `UX-10` drawn only in the prohibited "Sent" wording and missing its second state; Banda drawn as both a `Category` and a filter value; an off-by-one in the one worked example of the honest-progress rule; a `DOC` class defined as "no island required" and then given one; a misattributed polling figure; a login-wall rule that permitted the wall it forbade; Spanish in an English-only internal document. | LOW–MEDIUM | All corrected in place. |

**Findings raised and rejected**, recorded so they are not re-litigated: the `.excalidraw` file was reported missing and exists (the reviewer read the tree before the writer finished); `vendor` used for technology suppliers is inherited from `ADR-019`'s "zero vendor calls" and rewriting only P04's prose would make P04 inconsistent with the record it cites, so the glossary deviation is recorded rather than half-fixed; and the `ADR-020` filename retains its original `provisional-until-p04` slug, because committed records cite the path and rewriting history to tidy a filename is a worse trade than a stale slug.

**Axes both reviewers found clean:** the stable document prefix policy; scope creep inside `docs/04-ux/` (zero hex values, font names, spacing scales, component libraries, SQL or schemas across nineteen files); provenance-enum conformance; the operator-hours arithmetic, independently recomputed from `cost-model.md` and matching in all nine occurrences; the AI treatment, where the V1 refusal rests on the honest ground that no baseline exists and every guardrail on a future version is stated; and the map argument's substance, which argues from journey structure and explicitly refuses the privacy argument that would have been easier.

**What the reviews did not find, and what that is worth:** neither reviewer found a place where P04 marked an owner gate satisfied, presented a recommendation as an approval, promised a response time, invented a rating or a price, implied AI was authoritative, or let payment or booking UX into V1. **Those were the axes the phase was most at risk on, and they held.** The defects were in citation discipline, arithmetic basis, and internal consistency — which is a better failure profile than the reverse, but is not a small one: **two of the six CRITICAL findings were P04 citing sources that did not say what P04 said they said.**

## Deliverables

**New — `docs/04-ux/`:** `README.md` · `design-canon.md` · `customer-journey.md` · `request-intake.md` · `discovery-and-results.md` · `identity-and-verification.md` · `provider-profile.md` · `provider-onboarding.md` · `provider-workspace.md` · `response-conversation-outcome.md` · `operator-surfaces.md` · `information-architecture.md` · `surface-inventory.md` · `rendering-evidence.md` · `map-decision.md` · `accessibility-and-responsive.md` · `wireframes.md` · `ux-complexity-review.md` · `p05-p06-handoff.md`.

**New — diagrams:** `diagrams/journeys/customer-journey.md` · `diagrams/journeys/provider-journey.md` · `diagrams/journeys/decision-overlay.md` · `diagrams/context/superola-ux-map.md` · `diagrams/journeys/customer-journey.excalidraw`.

**New — presentation:** `presentation/ux-preview-v0.1.md` (bilingual) · `presentation/superola-ux-walkthrough-v0.1.html`.

**New — research:** `docs/07-research/gigsalad-request-flow-capture.md`, filed because `request-intake.md` set a guard requiring the capture to exist before a downstream document cited it, and the owner-facing preview does.

**New — plan:** `plans/P04-ux-architecture.md`.

**Modified:** `docs/02-architecture/adr/ADR-020` (rewritten; status moved off `DEFERRED`) · `docs/02-architecture/adr/ADR-019` (Level 3 answered) · `adr/README.md` · `docs/00-context/assumptions.md` (`A-026`–`A-031`) · `docs/00-context/source-register.md` (`SRC-015`) · `docs/00-context/glossary.md` (the `Vendor` retirement note) · `docs/01-product/open-questions.md` (`Q-036`–`Q-040` and three recommendations) · `docs/05-roadmap/risks.md` (`R-050`–`R-056`) · `plans/README.md` · `README.md` · `CHANGELOG.md` · `presentation/sources.md` · `presentation/outline.md` · `diagrams/journeys/README.md` · `diagrams/context/README.md`.

**Removed:** `docs/04-ux/customer-flow.md` · `vendor-flow.md` · `admin-flow.md` · `promotion-flow.md`. All four were five-line `NOT STARTED` placeholders whose content P04 delivered. `vendor-flow.md` could not survive under any name — `Vendor` is retired for the supply side, and the glossary had already recorded that the file would need renaming if P04 kept it.

## Handoff and recommended next step

**Recommended: obtain David's decisions on the P04 recommendation packet, and reconcile `SRC-004` when it arrives. Do not execute P05, P06, or P07 from this plan.**

- **The packet David now owns:** `ADR-020` Option A, `ADR-019` Level 3's no-map answer, the guest-then-verify timing (`Q-018`), the facet-count recommendation (`Q-035`), the `ReportedOutcome` reason extension (`Q-037`), and the price-on-public-surface conflict (`Q-040`). **Six items, individually.**
- **`G-06` is still the unresolved gate**, and it is now more consequential than it was before P04, not less: the profile, results, request and response surfaces are all designed against `WA-01`. If the owner answers that "available" means a reservable commitment, `DB-10` couples to `DB-02` and a materially different product is being built.
- **`SRC-004` remains NOT RECEIVED.** P04 built a complete UX architecture with no sight of the owner's existing UX work. That was authorized and it is still a real exposure — `OR-016` reconciliation precedes acceptance of any P04 design claim.
- **P05 inputs** are in `docs/04-ux/p05-p06-handoff.md` as `H5-01`–`H5-10`. The claim queue is **zero unless a cohort is approved**, and nothing is disclosed before a claim grant.
- **P06 inputs** are counts, not estimates: 36 V1 surfaces, 3 `FUTURE`, 9 operator surfaces, 3 islands each needing two implementations, 5 archetypes of which 4 form the P04 representative design set (not authoritatively validated — no usability evidence exists) and 1 is recommended out of the launch cohort. **P04 produced no developer-day or cost figure and P06 must not infer one from these counts.**
- **P07's requirements are unchanged and were not started.** The *Superola Decision & Cost Explorer* remains a P07 deliverable; the HTML walkthrough produced here is a marked prototype for understanding the journey and is not it.

**Two things that remain free now and expensive later**, in P04's own area: the composer's step-level abandonment instrumentation (`R-051`, and `ADR-020` trigger 5 depends on it), and making service-area or travel-distance an available decline reason so that the no-map decision is falsifiable from Phase-1 data rather than from anecdote (`R-055`).

Do not treat this plan's completion as authorization for any later phase.

---

*Record dates — phase executed 2026-08-12 on branch `p04-ux-architecture`, against P03.1 complete at commit `79b1f56`. The competitor request-flow capture was supplied by David on the same date as conversation input and was **not re-observed**; it is filed at `docs/07-research/gigsalad-request-flow-capture.md`. **No usability or traffic evidence exists** — `SRC-006` NOT RECEIVED — and nothing was implemented, so every interaction-cost and friction figure in this phase is an argued estimate rather than a measurement.*
