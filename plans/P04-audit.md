# P04 Audit — UX Architecture

## Metadata

- Status: `COMPLETED — SUPERSEDED BY RE-AUDIT`. The verdict below (`REOPEN MATERIAL PART`) was this audit's original finding. `plans/P04.1-reconciliation.md` closed the bounded findings, and the **§ P04.1 re-audit result** section at the end of this document is the current record. Read that section for the standing verdict; the rest of this document is retained as the historical finding it reconciled against.
- Scope: audit of the P04 UX bundle and its owner-facing structural walkthrough
- Decision status: `PROPOSED`; this audit approves nothing and does not authorize P05
- Evidence basis: repository sources, cross-document reconciliation, and a local browser smoke test of `presentation/superola-ux-walkthrough-v0.1.html`

## Objective

Determine whether P04 is internally coherent, traceable to the canonical UX inventory, and safe to hand to an implementation phase without silently promoting assumptions or creating misleading marketplace promises.

## Executive verdict

`REOPEN MATERIAL PART`.

The P04 architecture is a credible working proposal. The progressive request intake, `RequestDraft`, provider/customer separation, four request outcomes, no-rendered-map recommendation, and ADR-020 Option A rationale are substantially coherent. However, P04 acceptance is blocked by several material contradictions and stale indexes. The required work is bounded P04.1 reconciliation, not a new P04 design and not P05.

## Canonical baseline

The current canonical inventory is 39 named surfaces: 36 V1 and 3 `FUTURE`; 35 V1 rendered web surfaces (24 `DOC`, 11 `LOCAL`) plus `UX-35` as a notification channel. `UX-39` is the V1 report-intake/block-confirmation surface. See `docs/04-ux/surface-inventory.md` §8 and `docs/04-ux/rendering-evidence.md`.

ADR-020 remains `PROPOSED` and records a partially discharged `HOLD`. It recommends server-rendered views plus progressive enhancement and exactly three named islands. The per-surface implementation-cost evidence is not available before code exists; no production implementation was created by P04.

## Material findings

| Severity | Finding | Evidence and consequence |
|---|---|---|
| HIGH | The source-of-truth counts and status language drift across indexes and owner-facing material. | Canonical inventory says 39/36/35/24/11 and includes `UX-39`; the root README and plans index still say 38, the P05/P06 handoff uses older totals, and the map says nine queues rather than eight domain queues plus one index surface. The map also omits `UX-39`. This can mislead implementation scope and owner review. |
| HIGH | Anonymous local-draft privacy boundary is underspecified in the UX sources. | `R-050` requires browser-local state to contain structured answers and not free-text notes, with discard, promotion-clear, and expiry controls. The generic `RequestDraft`/composer wording does not carry all three controls or explicitly exclude notes. Implementing the generic wording literally could place sensitive text outside platform access control. |
| HIGH | Date semantics and delivery-food eligibility need one explicit reconciliation. | `IQ-04` says date is context and never a filter/system claim, while delivery-food `LocationEligibility` uses requested date against declared lead time and makes `flexible` produce `undetermined`. This may be a valid deterministic lead-time rule, but its customer-facing meaning must be reconciled with unresolved `G-06` before acceptance. |
| HIGH | Provider-fit language overpromises. | The owner preview says customers see providers who “can actually serve there” and requests “only reach providers they can serve.” Provider-profile rules explicitly prohibit implying that a provider can serve the event; coverage is a provider declaration and date feasibility is resolved in the request exchange. |
| HIGH | The mobile-performer profile offers a map-app action despite the canonical privacy/content rule. | `PP-63` permits link-out only from a published fixed venue address. The UX05 wireframe and HTML offer “Open Houston in your map app” for a mobile performer whose base is only an area. |
| HIGH | Pending-verification copy overstates delivery and makes the contact action ambiguous. | The prototype says the request “will reach” the provider after confirming the email/phone and labels the action “Confirm my address.” Canonical behavior is conditional on contact proof and recipient eligibility, with best-effort delivery and visible failure. “Address” can also be confused with event location. |
| HIGH | The representative walkthrough skips the canonical `UX-11` My Requests hub. | The canonical loop is `UX-10 → UX-11 → UX-12`; the HTML goes from confirmation directly to request detail. The prototype may be intentionally representative, but it must say so and show the authoritative hub or explain the omission. |
| MEDIUM | Notification copy selects email before a channel is approved. | The outcome document says “We will email you,” while MVP scope only requires one approved minimum channel and no vendor/channel decision exists. Keep the channel neutral until approved. |
| MEDIUM | The preview compresses four outcomes into three. | Canonical outcomes are clarification, decline, offer, and system-observed `NoResponse`. Owner-facing material should include non-punitive `NoResponse`, otherwise it understates a visible state. |
| MEDIUM | Validation and review claims are not independently auditable from the tree. | The P04 plan reports two blind adversarial reviews and prior corrections, but no review reports/manifests are persisted. The claim should be qualified or the bounded evidence artifacts added. The four archetypes should also be called a representative design set, not “validated” or launch-approved. |

## Requested audit conclusions

1. P04 in two minutes: coherent marketplace UX proposal, not an approved product design.
2. Customer view: discovery, profile, progressive request composition, identity verification, delivery state, request detail, conversation, and outcome capture.
3. Provider view: public profile, provider onboarding/workspace, request inbox/detail, response/conversation, and account-level settings; no availability guarantee.
4. RFQ model: the product sends a `ServiceRequest` after a `RequestDraft`; progressive questions are archetype-governed; verification gates delivery; recipient delivery remains conditional; outcomes are clarification, decline, offer, or `NoResponse`.
5. Surface inventory: canonical 39/36/35/24/11 baseline; the walkthrough is only a 17-screen structural sample.
6. Rendering: ADR-020 Option A is a reasonable `PROPOSED` recommendation. SSR/public readability and PE degradation are well framed; implementation cost and measured PE behavior remain unavailable.
7. Map: no rendered map in V1 is a defensible `PROPOSED` product decision, but the map source and count language need reconciliation.
8. AI: correctly kept `FUTURE`; it needs privacy/legal basis, deterministic fallback, spend/kill controls, and a measurable experiment gate before activation.
9. Wireframes/HTML: useful for flow comprehension, but not surface-complete and currently contain the mobile-map, verification, UX-11, and channel-copy issues above.
10. Strong parts: explicit evidence labels, no availability promise, no payment/booking/fanout in V1, four outcome model, no-rendered-map privacy boundary, and honest ADR-020 measurement limitation.
11. Required changes: bounded P04.1 reconciliation listed below.
12. Verdict: `REOPEN MATERIAL PART`.
13. Next: complete P04.1, re-audit, then stop. Do not begin P05 from this audit.

## Bounded P04.1 acceptance work

- Normalize all surface, rendering, operator-surface, and question-class counts; add `UX-39` to owner-facing maps; replace “nine queues” with “eight queues plus one index surface” where that is the intended meaning.
- Normalize status language to `PROPOSED` / approval required; do not describe ADR-020 or the map decision as closed or accepted.
- Record one explicit date/lead-time/`G-06` semantic rule, including the customer copy for `undetermined`.
- Make the local-draft contract explicit: structured answers only, free-text notes excluded, visible discard, clear-on-promotion, and local expiry.
- Remove the mobile-performer map link; retain link-out only where a published fixed venue address makes it valid.
- Rewrite provider-fit and pending-verification copy to describe declared coverage and conditional delivery without availability or delivery guarantees; rename contact verification actions so they cannot be confused with event address.
- Show or explicitly account for `UX-11`, `UX-35`, `UX-39`, and the operator surfaces in the structural walkthrough; keep its representative scope visible.
- Make notification wording channel-neutral and add `NoResponse` to the owner-facing outcome summary.
- Persist or qualify the historical adversarial-review claims, and change “validated archetypes” to “representative design set” unless authoritative validation evidence arrives.

## Validation performed

- Repository was clean before this audit; `git diff --check` passed.
- Local browser smoke test passed for title load, primary navigation, zero-result state, category-specific reveal, phone-stage toggle, intentional dead-control toast, and no console warnings/errors.
- The HTML contains 17 structural screens and 53 intentionally inert controls. It is not a complete rendering of the 39-surface inventory; `UX-11`, `UX-13`, `UX-16`, `UX-26`–`UX-35`, and `UX-39` are not represented as complete walkthrough screens.
- No production build or application implementation was performed.

## Handoff

P04 is not accepted as implementation-ready. P04.1 should reconcile the bounded material findings above and re-run the same source, coverage, privacy, and local-browser checks. P05 remains explicitly out of scope.

---

## P04.1 re-audit result

- Status of this section: `COMPLETED`. This is a **bounded re-audit** of `plans/P04.1-reconciliation.md` against the findings table and "Bounded P04.1 acceptance work" list above. It is not a new audit of P04 from scratch, it is not P05, and it does not authorize P05.
- Scope: verify each `HIGH`/`MEDIUM` finding's fix against the actual repository files (not only against P04.1's own claims), re-run a live browser smoke test of `presentation/superola-ux-walkthrough-v0.1.html`, verify the four items P04.1 left explicitly open are still correctly labelled open, grep the repository for the stale counts and `UX-39` coverage, and re-confirm `ADR-020`'s status and evidence-label discipline.
- Evidence basis: repository sources at their current working-tree state on branch `p04-ux-architecture` (P04.1's uncommitted diff), targeted greps across the full tree, and a live local browser session (Chrome DevTools MCP) against `presentation/superola-ux-walkthrough-v0.1.html` opened via `file://`.

### Executive verdict

**`ACCEPTED — OWNER VALIDATION REQUIRED`.**

P04, with P04.1's reconciliation applied, is internally coherent and free of the material contradictions this audit originally found. Every `HIGH` and `MEDIUM` finding traces to a verifiable fix in the actual files, not only to a claim in P04.1's own document. The four items P04.1 named as staying open remain correctly labelled open — none was silently resolved, and none was silently dropped. `ADR-020` still reads `PROPOSED — DAVID APPROVAL REQUIRED`. No evidence label was found promoted anywhere in the diff this re-audit inspected. The live smoke test — the one explicit gap P04.1 left — now passes with no console errors.

This verdict is technical acceptance of internal coherence, not product approval. It does not satisfy `G-06`, does not answer `Q-038`, does not close `ADR-020`, and does not authorize P05. **The decision packet P04 already owes David is unchanged by this re-audit** (see below).

### Findings verification table

| # | Original finding (severity) | Verification method | Status |
|---|---|---|---|
| 1 | Stale counts/status language drift across indexes (`38`, "nine queues", `UX-39` missing from owner-facing map) — **HIGH** | Repository-wide grep for `\b38\b`, "nine queues"/"nueve colas", `35/24/10`, `34 of 35`; read `README.md`, `plans/README.md`, `diagrams/context/superola-ux-map.md`, `docs/05-roadmap/risks.md` `R-053` | **CONFIRMED CLOSED.** No live stale occurrence found outside the audit's own historical record and `plans/P04-ux-architecture.md`'s adversarial-review table (both correctly describe a *prior* state, not a live claim). `superola-ux-map.md` now reads "eight domain queues plus one cross-queue index, on nine surfaces" throughout and draws `UX-39` (node, table row, group `g-crossrole`, bilingual explanatory row). |
| 2 | Anonymous local-draft privacy boundary underspecified (`R-050`) — **HIGH** | Read `docs/04-ux/request-intake.md` §4.6 | **CONFIRMED CLOSED.** `RD-17`–`RD-21` added: structured-answers-only (free-text `GC-12` explicitly excluded from local storage), visible discard, clear-on-promotion, local expiry (window correctly left `POLICY PENDING`), and `RD-21` correctly leaves the disclosure question `OPEN` at `Q-038` rather than answering it. |
| 3 | Date/lead-time and delivery-food eligibility vs. `IQ-04`/`G-06` — **HIGH** | Read `docs/04-ux/request-intake.md` §7.3 and `IQ-04`/`IQ-11` | **CONFIRMED CLOSED.** One explicit reconciling paragraph added ("Why this does not reopen `G-06`"), correctly framed as a narrower deterministic logistics predicate rather than an availability claim, and correctly stops short of resolving `G-06` itself. |
| 4 | Provider-fit language overpromise — **HIGH** | Grep `presentation/ux-preview-v0.1.md` and `docs/01-product/user-journeys.md` for "can actually serve" / "declare covering" | **CONFIRMED CLOSED for the cited scope** (owner preview, both languages, now "declare covering that area" / "declaran cubrir esa zona" with the added disclaimer that Superola does not promise service). `docs/01-product/user-journeys.md` still reads "receive requests the provider can actually serve" — **out of P04.1's cited scope by the original finding's own wording**, and correctly re-flagged as open rather than silently dropped (see Contradictions table below). |
| 5 | Mobile-performer map link-out violating `PP-63` — **HIGH** | Grep `docs/04-ux/wireframes.md` and the HTML for "map app"/"Houston"; **live browser check** of `UX-05` (screen 4) | **CONFIRMED CLOSED.** Wireframe annotation and both HTML profile screens now state explicitly that no map-app link appears for a declared-area provider, and name `PP-63`'s fixed-address condition. Verified live in-browser: the rendered `UX-05` screen shows the explanatory bilingual note, no map-app control. |
| 6 | Pending-verification copy overstates delivery / ambiguous "Confirm my address" — **HIGH** | Grep wireframes and HTML for "Confirm my address/contact", "will reach"; **live browser check** | **CONFIRMED CLOSED.** Copy now reads "We will try to deliver it... and only if the recipient is still eligible at that time"; action renamed "Confirm my contact" / "Use a different contact" in both wireframes and the HTML (verified live, screen 9's dead control text). |
| 7 | Representative walkthrough skips canonical `UX-11` hub without saying so — **HIGH** | **Live browser check** of the HTML banner | **CONFIRMED CLOSED.** The HTML banner (verified live, bilingual) explicitly states the walkthrough is a 17-screen representative sample, names `UX-11`, `UX-13`, `UX-16`, `UX-26`–`UX-35`, `UX-39` as not drawn, and states that the real product routes the confirmation-to-detail jump through `UX-11`. |
| 8 | Notification copy commits to email before channel approval — **MEDIUM** | Grep `docs/04-ux/response-conversation-outcome.md` | **CONFIRMED CLOSED.** Now channel-neutral ("We will try to notify you...") with `Q-032` cited as unresolved. |
| 9 | Owner preview compresses four outcomes into three (`NoResponse` missing) — **MEDIUM** | Grep `presentation/ux-preview-v0.1.md` | **CONFIRMED CLOSED.** `NoResponse` now named explicitly as a legitimate, non-punitive outcome in both languages. |
| 10 | Unauditable validation claims / "validated archetypes" — **MEDIUM** | Grep `plans/P04-ux-architecture.md` and `docs/04-ux/p05-p06-handoff.md` for "validated"/"representative design set" | **CONFIRMED CLOSED.** Both now read "representative design set... not authoritatively validated — no usability evidence exists." The adversarial-review findings remain persisted in full in the plan rather than summarized away, which is what the original finding asked for. |

### Contradictions P04.1 left open — re-verified

| # | Item | Re-audit check | Status |
|---|---|---|---|
| 1 | `docs/01-product/user-journeys.md`'s "receive requests the provider can actually serve" | Grep confirms the string is still present, unedited | **STILL OPEN — correctly labelled.** Not silently resolved, not silently dropped from the record. |
| 2 | `Q-038` (local-draft disclosure) and the local-draft expiry window | Read `request-intake.md` `RD-20`/`RD-21` | **STILL OPEN — correctly labelled** `POLICY PENDING` / `OPEN`, owner David (+ counsel on `Q-038`). |
| 3 | `G-06` | Read `docs/04-ux/request-intake.md` `RI-O1` and §7.3 | **STILL UNSATISFIED — correctly labelled.** No artifact touched by P04.1 treats it as resolved. |
| 4 | No live smoke test had been run on the HTML walkthrough | This re-audit ran one | **CLOSED BY THIS RE-AUDIT.** See below. |

### Smoke test result

A live browser session (Chrome DevTools MCP, `file://` load, no build step) was run against `presentation/superola-ux-walkthrough-v0.1.html` in its current working-tree state.

- **Load and structure:** page loads; the bilingual scope-note banner (added by P04.1) renders correctly and names `UX-11`, `UX-13`, `UX-16`, `UX-26`–`UX-35`, `UX-39` as out of the walkthrough.
- **Navigation:** `Next`/`Prev` and the screen-jump dropdown both work across all 17 screens; the screen counter updates correctly.
- **Zero-result variant:** the "See zero-result variant" control on `UX-04` correctly swaps to the no-match state (Banda × Rockport), with the relaxed-search suggestions and the unsupported-category variant both present.
- **Width toggle:** the Desktop/Phone toggle correctly constrains the stage width; verified visually at both settings.
- **Mobile-map removal (P04.1 fix):** navigated live to `UX-05` (screen 4) — the rendered page shows "Superola no muestra un mapa, y aquí tampoco hay enlace a una app de mapas" with the `PP-63` condition stated, and no map-app control is rendered. Matches the file-level grep result.
- **Verification copy (P04.1 fix):** the "Confirm my contact" / "Use a different contact" controls render as edited; the "We will try to deliver it... and only if the recipient is still eligible" wording is present in the source at the point the verification screen reads from.
- **Category-conditional reveal:** switching the demo archetype radio on `UX-07` stage B (screen 6) re-renders the revealed question block with an `aria-live="polite"` status region, consistent with the accessibility rule the document cites.
- **Dead-control mechanism:** clicking an inert control (`data-dead`) triggers the prototype's own `flash()` handler, which is the "intentional dead-control toast" the original audit's validation section described. Mechanism confirmed present and firing; unrelated to any P04.1 edit.
- **Console:** **zero JavaScript errors or warnings** across the full session (load, all 17 screens, zero-result variant, width toggle, category reveal, dead-control click). The only console entries in the entire session are two DevTools accessibility **issues** (not errors): "No label associated with a form field" (7) and "A form field element should have an id or name attribute" (22).

**New finding (LOW, informational, not blocking):** the 7 unlabeled-field / 22 missing-id-or-name accessibility lint issues above were not part of any original audit finding and are not claimed fixed by P04.1. They most likely predate P04.1 — P04.1's own diff to this file is text-only plus one removed dead control, per its Validation section — but this was not independently confirmed via `git blame`. They do not block P04 acceptance: the prototype already disclaims being a final, accessibility-certified surface, and the 39-surface product itself makes no accessibility-compliance certification claim (`docs/04-ux/accessibility-and-responsive.md`). Recorded here rather than fixed silently, per this re-audit's instructions; a cleanup pass on the decorative `data-dead` controls and the free-text inputs is recommended before the prototype is used in an accessibility-sensitive review, but is not required for the `ACCEPTED — OWNER VALIDATION REQUIRED` verdict above.

### Evidence-label and scope discipline re-check

- `ADR-020` header re-read: still `PROPOSED — P04 EVIDENCE DELIVERED; RECOMMENDS OPTION A; DAVID APPROVAL REQUIRED`. Untouched by P04.1.
- `docs/04-ux/surface-inventory.md` and `docs/04-ux/rendering-evidence.md` headers re-read: both still `PROPOSED — OWNER VALIDATION REQUIRED`; neither file appears in P04.1's changed-file list (`git diff --stat`), consistent with P04.1's claim that they were "already internally correct at audit time" and out of its edit scope.
- No `ASSUMPTION` or `PROPOSED` label was found promoted to `CONFIRMED`/`ACCEPTED` in any file P04.1 touched.
- `docs/04-ux/p05-p06-handoff.md` §2.1's surface-count table was independently re-summed against `docs/04-ux/surface-inventory.md` §8: 24 `DOC` + 11 `LOCAL` + 1 n/a (`UX-35`) = 36 V1; 13 customer + 9 provider + 9 operator + 4 any + 1 cross-role = 36; 9 public + 1 transitional + 25 authenticated + 1 channel = 36. All three independent partitions sum to 36 and agree with the canonical inventory — the original off-by-one/undercount finding is fully closed, not just asserted closed.
- `git diff --stat` against `HEAD`: 12 modified files, all inside `docs/04-ux/`, `docs/05-roadmap/risks.md`, `diagrams/context/`, `presentation/`, `README.md`, `plans/` — matches P04.1's acceptance-criteria file-scope claim exactly. `plans/P04-audit.md` and `plans/P04.1-reconciliation.md` are untracked new files, not modifications to prior history.
- `git diff --check`: **PASS** (exit 0), only informational CRLF/LF warnings on Windows, no conflict markers or whitespace errors.

### What is still owed to David — unchanged by this re-audit

This re-audit does not add to or subtract from the decision packet `plans/P04-ux-architecture.md` and `plans/P04.1-reconciliation.md` already name:

- `ADR-020` Option A (web rendering) — approval required.
- `ADR-019` Level 3's no-rendered-map answer — approval required.
- Guest-then-verify customer verification timing (`Q-018`).
- The facet-count recommendation (`Q-035`).
- The `ReportedOutcome` secondary-reason extension (`Q-037`).
- The price-on-public-surface conflict (`Q-040`).
- `Q-038` — anonymous local-draft storage: acceptable, and does it require disclosure (David + counsel).
- `G-06` — what "available"/"accepting requests" promises a customer — still the most consequential open gate; still unsatisfied.
- `docs/01-product/user-journeys.md`'s residual provider-fit wording, named above, which no phase has yet corrected.

### Confirmation

**P05 was not started, was not designed, and is not authorized by this re-audit.** No production code was written, no build was run, no architecture was changed, and no timestamp was updated as a mechanical side effect of this pass. This re-audit's only repository changes are the Status/verdict update at the top of this document and this section.

---

*Re-audit executed 2026-08-12 on branch `p04-ux-architecture`, against `plans/P04.1-reconciliation.md`'s completed reconciliation and the working tree it left uncommitted. No commit was made by this re-audit.*
