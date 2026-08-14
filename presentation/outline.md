# Presentation Pack — Production Outline

> Status: P07 IN PROGRESS — CONTENT SOURCE COMPLETE; rendered deck and interactive explorer not started.

P07 now provides the canonical editorial package in:

- `superola-owner-review-v1.md` — 16-slide core source;
- `superola-owner-review-speaker-notes-v1.md` — Spanish delivery notes;
- `superola-owner-review-appendix-v1.md` — depth material;
- `owner-decision-sheet-v1.md` — bilingual meeting capture sheet.

This outline now governs the remaining visual-production work. The content-source milestone did not generate a PPTX or the Decision & Cost Explorer.

This internal planning document remains English only. Only the owner-facing preview/slides and presentation diagrams are bilingual Spanish + English; internal source, research, production, and speaker/planning support files remain English only unless explicitly intended for owner viewing.

## P01-authorized Discovery Preview v0.1

An explicitly started P01 may produce a lightweight `Discovery Preview v0.1` before P07. It is not created in P00.1 and does not replace the mature Presentation Pack.

The preview must be:

- labeled `PROPOSED — WORKING DRAFT`;
- concise and bilingual in Spanish and English;
- evidence- and provenance-aware;
- free of accepted/unapproved stack decisions;
- suitable for owner discussion rather than presented as a final recommendation.

Candidate content:

1. Current understanding of Superola.
2. Customer/provider marketplace map.
3. Confirmed owner ideas.
4. Proposed capability map.
5. Major unknowns.
6. Proposed launch-slice hypothesis.
7. Questions requiring owner input.
8. What follows discovery.
9. Optional `PROPOSED` concept: Human + AI Discovery—one governed marketplace model serving people, public search/AI discovery, and future authorized agents without committing protocols.

## P07 mature Presentation Pack

Future P07 materials should provide reviewed Spanish and English versions/support for:

1. Superola opportunity and evidence quality.
2. Validated customer/provider problems and MVP outcomes.
3. Marketplace and domain recommendation.
4. Technology recommendation with alternatives and reconsideration triggers.
5. Build-vs-buy and cost summary.
6. Legacy-data strategy.
7. UX/customer/vendor journey summary.
8. Incremental roadmap, dependencies, risks, decisions, and owner asks.

Every factual or comparative claim needs a source in `presentation/sources.md` and alignment with canonical repository evidence.

## Persisted requirement — Superola Decision & Cost Explorer (P07)

`REQUIREMENT — RECORDED IN P03.1 FOR P07. Do not build it before P07 is explicitly started.`

**P04 did not build it.** P04 produced `presentation/superola-ux-walkthrough-v0.1.html`, which is a **different artifact**: a marked structural prototype for walking the primary customer journey across low-fidelity screens, carrying a persistent bilingual `PROTOTYPE — NOT FINAL DESIGN` banner and explaining no cost, no decision packet, and no roadmap. **The Explorer's scope below is untouched and still belongs to P07.** The prohibition above is read as forbidding the Explorer, not as forbidding any HTML in this directory; a marked prototype whose palette and font stack are placeholders required for HTML to render is permitted, and no product surface may take a color, typeface, or measurement from it.

P07 must generate a **presentation-quality, self-contained HTML artifact** intended for David and the owner. It is the mature replacement for the current Markdown previews, not an addition to them.

### What it must visually explain

**Product** — what Superola does; the customer/provider loop; V1 versus future.

**Architecture** — the major modules; the one-deployable shape; future extension points.

**Technology** — the selected stack; the meaningful alternatives; **why each selection won**; and **which decisions remain reversible**, with reversal difficulty visible rather than implied.

**Costs** — Pilot / Early Marketplace / Growth, **with USD labelled explicitly on every figure**; an expandable breakdown by component; fixed versus variable separated; **infrastructure cash cost visually separate from illustrative human operational cost, never summed into a headline**; excluded capabilities stated; and the alternative-cost comparisons from `docs/03-technology/cost-alternatives.md`.

**Decision branches** — what changes if: payments enter V1; multi-provider broadcast; broad legacy migration; native mobile; AI-assisted intake.

**Roadmap** — P06 output, when it exists.

### Requirements on the artifact itself

- Understandable by a **non-technical owner**, and simultaneously useful to David as a technical explainer.
- **Responsive.**
- **No backend dependency**; self-contained where practical.
- **Must not require proprietary design software to open.**
- **Current prices must display their verification date beside the price detail**, not as volatile metadata in a document or page header.
- **No automatic regeneration timestamp in the visible header** — see `AGENTS.md`, *Stable document prefix policy*.
- Every figure must reconcile to `docs/03-technology/cost-model.md` §1.1, which is the single arithmetic source of truth. **The artifact must not become a second source of arithmetic.**

The final artifact may be complemented by Excalidraw and/or Claude Design visuals.

## Persisted requirement — owner-facing diagram sources (P04 / P07)

`REQUIREMENT — RECORDED IN P03.1. Sources belong under diagrams/; owner-facing diagrams are bilingual Spanish + English, internal engineering diagrams remain English only.`

At minimum P04/P07 should produce Excalidraw (or equivalent editable) sources for three diagrams:

**Diagram A — Superola in one page.** Customer + Provider → Superola → Discovery / Profiles / RFQ / Quotes / Conversation → core platform and data → external services → future extensions.

**Diagram B — Customer journey.** Need/event → structured request → relevant results → provider profile → RFQ → quote/clarification → conversation → outcome.

**Diagram C — What changes if…** Payments · Matching/fan-out · Legacy migration · Mobile · AI.

### Delivered by P04

All three exist, plus a fourth. **Because this repository contains no diagram toolchain**, each is a Markdown source carrying a Mermaid rendering **plus a precise Excalidraw build specification**, which the P04 directive authorized as the substitute for generated sources; one hand-authored `.excalidraw` scene was produced for Diagram B and validated as JSON.

| Diagram | File | Note |
|---|---|---|
| A — Superola in one page | `diagrams/context/superola-ux-map.md` | Rendered as the surface map: public, customer, provider, channel and operator lanes, with `FUTURE` surfaces visually separated. |
| B — Customer journey | `diagrams/journeys/customer-journey.md` + `diagrams/journeys/customer-journey.excalidraw` | The `.excalidraw` scene is a **subset** of the build specification and says so on the canvas. |
| C — What changes if… | `diagrams/journeys/decision-overlay.md` | **P04 substituted guaranteed calendar availability for legacy migration** in the branch set, on the P04 directive's own list, and recorded the substitution rather than dropping legacy migration silently. |
| — Provider journey | `diagrams/journeys/provider-journey.md` | Not in the P03.1 requirement; added because the provider side is half the marketplace and the owner asks about it. |

**The Mermaid was reviewed by hand and not machine-validated**, because installing a renderer would add tooling to a repository that deliberately has none.
