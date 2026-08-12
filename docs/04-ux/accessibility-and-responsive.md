# Accessibility and Responsive Architecture — P04 UX Architecture

> Status: `PROPOSED — OWNER VALIDATION REQUIRED`. Provenance: `TECHNICAL_DISCOVERY`. This is a P04 UX architecture proposal, not an approved design and not a visual specification.
> Working assumptions: `WA-01`–`WA-05` (see `docs/04-ux/README.md`). `G-06` is UNSATISFIED.

This document defines a **baseline interaction architecture**: which interactions must be operable without a mouse, how state is communicated when color is unavailable, where focus goes when the interface changes shape, and how each primary journey behaves at phone width. It is not a visual specification, not a component library, not a style guide, and not an audit.

**It makes no compliance certification claim of any kind.** No conformance level is asserted, no standard is claimed as met, no audit has been performed, and no artifact in P04 may be cited as evidence that Superola conforms to any accessibility law, regulation, or guideline in any market. Nothing was implemented and nothing was tested with assistive technology, so every statement here is a **design requirement**, not a measured result. Certification language — "accessible", "compliant", "conformant", a level letter, a badge — must not appear on any Superola surface or in any owner-facing artifact on the basis of this document. Whether a formal audit is commissioned, and against which standard, is an owner decision P04 does not make.

---

## 1. What this baseline binds

| Scope | Position |
|---|---|
| Surfaces bound | Every V1 surface in `docs/04-ux/surface-inventory.md`, including the three client islands `I-1`, `I-2`, `I-3`. |
| Rendering basis | Option A — server-rendered views plus progressive enhancement (`ADR-020` recommendation). Every island must degrade to a working server-rendered path, so **the no-JavaScript path is also the baseline accessibility path**, not a fallback of lower quality. |
| Evidence status | `PROPOSED`. Provenance: `TECHNICAL_DISCOVERY`. No usability or assistive-technology evidence exists — `SRC-006` is NOT RECEIVED. |
| Out of scope | Contrast ratios, color values, type scale, spacing, iconography, motion curves, and any component API. Architecture before aesthetics. |

---

## 2. Keyboard operability by interaction pattern

Every interactive surface is operable with the keyboard alone. Where an island provides an enhanced pattern, the requirement applies to **both** the enhanced pattern and its degraded server-rendered path.

| Pattern | Surfaces | Keyboard requirement | Degraded path when JavaScript is unavailable |
|---|---|---|---|
| Document and navigation | `UX-02`, `UX-03`, `UX-05`, `UX-06`, `UX-10`, `UX-11`, `UX-13`, `UX-24` | Standard tab order following reading order; a skip link to the main region; no focus trap anywhere; visible focus on every focusable element. | Native. |
| Standard form controls | `UX-08`, `UX-09`, `UX-14`, `UX-15`, `UX-16`, `UX-19`, `UX-21`, `UX-25` | Native controls only. No custom widget where a native control exists. Submit reachable by keyboard without leaving the form. | Native. |
| `I-1` governed type-ahead picker (category and `Place`) | `UX-01`, `UX-04`, `UX-07`, `UX-17`, `UX-22` | **ARIA combobox with a managed active descendant.** Typing filters governed rows; `Down`/`Up` move the active descendant without moving DOM focus out of the text input; `Enter` commits the active row; `Escape` closes the list and restores the typed value; `Home`/`End` move within the text, not the list. The active row is programmatically exposed as the input's active descendant so a screen reader announces it as the user arrows. The listbox is never focus-trapped. **Selection is a commit to a governed identifier, never to free text** — an uncommitted string is rejected and shown to the customer, never silently approximated. | **A plain `select` over the governed list, plus a server round trip.** This is a hard requirement, not a courtesy: the governed `Category` and `Place` lists are enumerable by construction (`ADR-019` Level 1), so a native select is always a correct representation. Where a list is too long for one select at a given granularity, the degraded path is a two-step server-rendered narrowing (for example region then `Place`), never a free-text box. |
| `I-2` composer step controller | `UX-07` | Every step is a form with native controls and a native submit. Forward and back are keyboard-operable controls, not gestures. Local draft persistence is invisible to the keyboard model — it must never change focus, never move the caret, and never fire on a timer that interrupts typing. | **One server-rendered step per POST.** The composer is fully completable with no JavaScript at all. |
| `I-3` media upload manager | `UX-17`, `UX-23` | File selection is reachable through a native file input, keyboard-operable; drag-and-drop is an **addition**, never the only path. Per-file rows are keyboard-reachable, each with an accessible name identifying the file and a keyboard-operable remove action. The `ADR-018` derivative **processing state** is exposed as a live status per file (see §6), not as a spinner alone. | **A native multi-file form POST with a server-rendered result page** listing each file and its processing state, refreshed on navigation. No progress bar, no failure. |
| Conditional reveal regions | `UX-07`, `UX-17`, `UX-19`, `UX-22`, **`UX-14`**, **`UX-27`** | See §4. The revealed region is in DOM order immediately after the control that caused it; it is announced; focus moves deliberately. **`UX-14` (the optional secondary outcome reason revealed by the governed `ReportedOutcome` answer) and `UX-27` (the reason fields revealed per moderation action type) reveal on user input exactly as the four surfaces above do, and are not exempt for being small or in-form.** §4.1's three failure modes do not depend on the size of the revealed region: a silent reveal of one field is still silent, and focus theft into one field is still focus theft. The one §4 rule that does not apply is the removal warning, because neither reveal destroys an answer the user has already given. | Reveal becomes the next server-rendered step. For `UX-14` and `UX-27` this is a second server-rendered form on the same page after the first answer is posted. |
| Destructive confirmation (for example the category-change warning, §5.2 of the composer model) | `UX-07`, `UX-13`, `UX-21`, `UX-22` | Focus moves into the confirmation on open, returns to the invoking control on cancel, and moves to the resulting region on confirm. The warning names **what will be lost before the fact**; the undo affordance is keyboard-reachable and is not a transient toast that expires before a screen-reader user reaches it. | A server-rendered interstitial confirmation page. |
| Result list and ranking explanation | `UX-03`, `UX-04` | Each result slot is a single coherent stop with one primary link; the per-slot ranking explanation and the `placementBasis` disclosure marker are text in the accessible name or in the slot's readable content, never a hover-only tooltip. | Native. |
| Thread and conversation | `UX-12`, `UX-20` | Chronological reading order matches DOM order; the composer is a form with a native submit; author role and capacity (§8 of the dual-role model) are text within each entry. **No focus movement on content arrival** — there is no realtime transport (`system-architecture.md` §3), so content only ever arrives on navigation, which is the safest possible case. | Native. |
| Operator queue tables and filters | `UX-26`–`UX-34` | Table semantics with header association; filters are a native form with an explicit apply; row actions are keyboard-reachable and each names the case it acts on. Bulk selection is not built (see `docs/04-ux/ux-complexity-review.md`). | Native. |

**Prohibitions.** No keyboard trap on any surface. No interaction whose only trigger is hover, drag, long-press, swipe, or a pointer gesture. No control whose only affordance is an icon without an accessible name. No native-mobile-specific interaction pattern anywhere (`WA-04`).

---

## 3. Labels, errors, and announcement

| Requirement | Rule |
|---|---|
| Label association | Every input has a programmatically associated label. A placeholder is never a label. A label is never removed on focus or on entry. |
| Required and optional | Every field states which it is in text. This matters structurally in Superola because the composer mixes `DISCOVERY`, `PRE-SUBMIT`, `QUALITY`, and `CATEGORY` fields on adjacent steps, and a `QUALITY` field that looks required is friction the model explicitly rejects — budget in particular is optional and must read as optional (§5.4 of the intake model). |
| Help and constraint text | Format constraints, units, and examples are associated with the field and are available before the user submits, not only in the error. Provider-entered service-area units are preserved as the provider entered them (`ADR-019`). |
| Error association | Every error is programmatically associated with its field, is placed adjacent to it, and names the field in its own text so it is comprehensible when read alone. |
| Error announcement | Errors are **announced**, not only rendered. A form-level summary is exposed as an alert on submit failure, lists each failing field as a link to that field, and is the focus target after a failed submit. |
| **Never color alone** | **No error, warning, or success is communicated by color, border weight, icon shape, or position alone.** Every one carries text. |
| Validation source | Validation messages are authored server-side because validation lives in the domain (`ADR-011`). Client-side enhancement may pre-empt a round trip; it may never be the only validation and may never produce a message the server would not produce. |
| Rejected input | Unresolvable governed input (a category or `Place` that does not exist) produces a plain rejection naming what was not recognized and offering the governed alternatives. It is never approximated, never auto-corrected silently, and never converted into a broadcast. |
| Free text | Free-text notes carry a visible statement of how they are treated. Superola must **not** tell users that "in-platform" means contact-protected (`ADR-010`); the field's help text must not imply protection it does not provide. |

---

## 4. Focus management in progressive, multi-step flows

**This is the hardest accessibility problem in P04**, and it is a direct consequence of the intake model: the composer is progressive and category-governed (§5.1), so selecting a `Category` changes what questions exist. A sighted mouse user perceives that change instantly and peripherally. A screen-reader user perceives nothing at all unless the architecture makes the change perceivable, and a keyboard user perceives it only as an unexplained change in tab order. The same problem recurs in provider onboarding (`UX-17`), the offering editor (`UX-22`), the archetype-conditional provider response form (`UX-19`), **outcome capture (`UX-14`), where the optional secondary reason is revealed by the governed `ReportedOutcome` answer, and the moderation case detail (`UX-27`), where the reason fields depend on the action type chosen.** The last two are in-form radio reveals and are **not exempt**: this section's rules bind them in full, because a one-field reveal is silent in exactly the way a ten-field reveal is.

### 4.1 The three failure modes being designed against

| Failure | What the user experiences | Why the naive fix is wrong |
|---|---|---|
| Silent reveal | New questions appear; the screen reader says nothing; the user submits an incomplete request and receives errors for fields they never knew existed. | Marking the whole form as a live region announces every keystroke and every unrelated update. The region must be scoped and polite, not the form. |
| Focus theft | Focus jumps to the first revealed input the moment the category is chosen. | The user is now inside a group whose purpose, size, and relationship to the choice they just made were never stated. They have been teleported past the context. This is the most common "accessible" implementation and it is worse than doing nothing. |
| Reordering | The reveal re-sorts the form so answered questions move. | Position is memory. A user who answered question two and is told it is now question five has lost the ability to navigate by recall, and a screen-reader user re-reading the form cannot tell whether it is the same form. |

### 4.2 The rules

| Rule | Requirement |
|---|---|
| Placement | The revealed region is inserted in DOM order **immediately after the control that caused the reveal**. Visual order and DOM order match. The region is a labelled group with a heading that names the archetype context — for example *Details for a mobile performer* — so the announcement and the visible heading are the same words. |
| Announcement | The region is announced **politely**, never assertively: an assertive announcement interrupts the user mid-thought and is reserved in this product for submission errors and delivery failures. The announcement states what appeared and how much of it — the count of questions in the group — because "some questions appeared" is not actionable and a named count is. |
| **Focus target** | **Focus moves to the new region's heading, not to its first input.** The heading is made programmatically focusable for this purpose only, and it is not in the tab order. |
| Why the heading and not the first input | Three reasons, all structural. First, the heading carries the *reason* the region exists — the category the user just chose — and landing on an input discards that context permanently, because a screen reader announces the field label and not the group's purpose. Second, landing on an input commits the user to answering before they have heard what the group contains or how long it is; landing on the heading lets them read the group, `Tab` into it, or `Shift+Tab` back to correct the category choice that produced it. Third, the category choice is the single most consequential and most reversible decision in the composer — changing it drops category-specific answers (§5.2) — so the interaction must keep the escape route one keystroke away. Landing on the first input puts the escape route behind an unknown number of fields. |
| **No reordering** | **A reveal must never reorder already-answered questions.** Universal `EventContext` answers keep their position and their answers when a category is chosen or changed. Category-specific groups are appended, never interleaved with universal questions. Removing a category-specific group removes only that group. |
| Removal | When a category change removes a group, the before-the-fact warning names what will be lost (§5.2), and after confirmation focus returns to the category control — the control the user acted on — not to an arbitrary position in the shortened form. The undo affordance is announced with the removal and persists until the user navigates. |
| Progress and step transitions | Moving between composer steps is a navigation. Focus moves to the new step's heading; the named stage is announced with it (§5). The primary action is reachable without traversing the whole step. |
| Server-rendered equivalence | On the no-JavaScript path each reveal is a new server-rendered step. Focus lands where a page load lands, the heading is the first meaningful content, and the same wording is used. **The two paths must announce the same words**, so the announcement text is authored once, server-side, and the island reuses it. |
| Prohibited | No focus movement caused by a timer. No focus movement caused by content the user did not act on. No focus movement on autosave. No modal that returns focus to the top of the document. |

### 4.3 What would confirm this works

Nothing here is measured. The evaluation that would produce evidence is a moderated task-completion pass through `UX-07` with screen-reader and keyboard-only participants, recording whether the reveal was noticed, whether the group's purpose was understood, and whether the category choice was recoverable. That evaluation has not been run and `SRC-006` is NOT RECEIVED, so no completion claim may be made.

---

## 5. Honest progress indication

| Rule | Requirement |
|---|---|
| Named stages, not a denominator | The composer uses a **named-stage indicator** — for example *What you need · Where · When · Details · Review* — with the current stage marked in text and announced on entry. |
| **No false denominator** | **"Step 3 of 6" must not be rendered while the total is data-dependent.** The composer's question count derives from the selected `CategoryArchetype`'s governed `CategoryAttributeDefinition`s, so a total shown before the category is chosen is a fabricated number; and a total that changes after the category is chosen is worse than no total, because the user has already anchored on it. This is an accessibility requirement, not only an honesty one: a screen-reader user has no peripheral view of the form and relies entirely on the stated position, so a wrong denominator is the only information they have. |
| When a count is honest | Within a single stage whose contents are already determined, a count of the questions in that stage is honest and may be stated. Across stages it is not, until the category is committed. |
| Provider onboarding | `UX-17` and `UX-24` use a **requirements checklist**, not a percentage. Each item states what it is, why it is required, and what it unlocks. A percentage is a denominator problem in the same shape: `publicationGateMet` is archetype-aware, so the item count differs by archetype and the requirement version can change. |
| Indeterminate work | Where duration is genuinely unknown — media derivative generation (`ADR-018`), a delivery attempt — the indicator says the work is in progress and does not invent a proportion. Completion and failure are both announced. |

---

## 6. Non-color status communication

**Every status in the product carries text.** Color, icon, weight, and position are reinforcement only.

**What this table is, stated honestly.** It is **the statuses whose non-color treatment is specified here** — 18 rows. **It is not the complete V1 status set**, and the earlier claim that it was — *"a status not in this table does not exist as a status"* — was over-broad: it omitted three whole lifecycle families that `domain-model.md` defines and that are rendered on customer surfaces. Those families are carried **by reference** in §6.1 rather than restated, so the specification is honest about what it has and has not treated.

| Status | Where it appears | Text requirement | Prohibition |
|---|---|---|---|
| `RequestIntake` = `accepting` | `UX-04`, `UX-05`, `UX-22` | Stated in words as an intake state — the provider is accepting requests. | **Never worded as availability**, never as a date claim, never as a calendar state. `V1 has no availability model` and the non-claim appears verbatim in substance on every profile and search surface (`ADR-005`). |
| `RequestIntake` = `paused` | `UX-04`, `UX-05`, `UX-22` | Stated in words, with the optional provider-authored reason and optional horizon when supplied. | **"Not accepting requests" is not date unavailability and must never be worded as either.** No red dot standing in for the words. |
| `RequestIntake` = `unconfirmed` | `UX-04`, `UX-05`, `UX-22` | Stated as unconfirmed intake, distinct from both other values in text. | Never collapsed into `paused`. Never rendered as an absence. |
| Freshness / `Stale` | `UX-04`, `UX-05` | A freshness indicator in words alongside the result or profile. `Stale` providers **stay discoverable and flagged**, never hidden. | Never a faded row, a lowered opacity, or a gray badge alone. |
| Publication state, provider-facing | `UX-24`, `UX-21`, `UX-17` | `Draft`, `PendingReview`, `Published`, `Stale`, `Deactivated`, `Suspended`, `Rejected`, `Closed` each named in text with its reason and the next action. | No state communicated by badge color alone. |
| Publication state, customer-facing | `UX-04`, `UX-05` | Only `Published` and `Stale` are distinguishable, and `Stale` carries the freshness indicator. | **`Suspended` must not be distinguishable from `Deactivated` by any signal — text, color, wording, HTTP status, or absence pattern.** |
| `ProviderResponse` kind = `clarification` | `UX-12`, `UX-20` | Named in text as a question; the thread entry states that no price is included. | Never rendered as a neutral message indistinguishable from an `offer`. |
| `ProviderResponse` kind = `decline` | `UX-12`, `UX-20` | Named in text, with the optional provider-authored reason when supplied. | Never colored as an error or failure. A decline is a legitimate outcome, is non-punitive, and must not read as a fault. |
| `ProviderResponse` kind = `offer` | `UX-12`, `UX-20` | Named in text, with `PriceStatement` fields — currency, basis, inclusions, exclusions, conditions, as-of — as labelled text when present. | **No accept button that creates an obligation.** A quote is not a booking and must not imply transaction protection. |
| `NoResponse` (system-observed) | `UX-11`, `UX-12` | Stated as an observation about elapsed time, in text, on the customer's own surface. | **Never presented as a judgement of the provider** (`Q-032`), never as a badge, never as a score, never publicly. The window length is `POLICY PENDING`. |
| `Superseded` response | `UX-12`, `UX-20` | Named in text; remains visible to both parties. | Never hidden, never struck through as the only signal. |
| `LocationEligibility` = `undetermined` | `UX-04`, `UX-31` | **Surfaced with its uncertainty in words** on the customer-facing result and in the operator queue. | **Never coerced** to `eligible` or `ineligible`, never silently dropped from results, never presented as a confirmed match, and never represented only by a differently colored row. |
| Delivery failure (`DeliveryAttempt`) | `UX-11`, `UX-15`, `UX-25`, `UX-32` | Announced assertively to the affected party on their own surface, in text, with what to do next. | **Never presented to the counterparty as non-response.** Notification failure is visible to its own party only. |
| Unread | `UX-11`, `UX-12`, `UX-18`, `UX-20` | A count or state in text within the accessible name of the item it qualifies. Computed server-side and rendered on navigation. | Never a bare colored dot. **No global polling** — there is no realtime transport, so unread must never be presented as if it were live. |
| Media processing state (`ADR-018`) | `UX-23`, `UX-17` | Per-file text state: pending, processing, ready, failed, and the dead-external-link state for linked audio and video. | Never a spinner alone. Never a silently failed file. |
| `placementBasis` disclosure | `UX-03`, `UX-04` | A per-slot disclosure marker in text on every result slot, plus a ranking explanation. V1 value is `organic`. | No `featured` or `sponsored` styling, since neither exists in V1. |
| `verificationBasis` | `UX-05` | The precise auditable event stated in words. | **No bare `verified` badge.** No trust icon standing in for an undefined claim. |
| Capacity in a dual-role context | `UX-12`, `UX-19`, `UX-20` | Stated in text on every surface where capacity is ambiguous — *You are replying as {Business}* or *You are writing as yourself*. | Never implied by placement, avatar, or column alone. This is `ADR-004`'s assigned negative consequence and it is discharged in text. |

### 6.1 Status families carried by reference, not specified above

These are rendered on customer or provider surfaces and are **governed by `docs/02-architecture/domain-model.md`, not by this document**. The universal rule in §11.4 applies to every one of them; their per-state text treatment is **not specified here and is a P05 authoring task** against the lifecycle definitions. **P04 does not restate them, because a second copy of a lifecycle drifts from the first.**

| Family | Source | States not treated above | Where rendered |
|---|---|---|---|
| `ServiceRequest` lifecycle | `domain-model.md` §5.2 | **11** — `PendingVerification`, `Abandoned`, `VerificationFailed`, `RecipientIneligible`, `Delivered`, `InDiscussion`, `Declined`, `Withdrawn`, `Quarantined`, `Lapsed`, `Closed`. `NoResponse` is the one state of this family with a row above. | `UX-10`, `UX-11`, `UX-12` customer-side; `UX-18`, `UX-19` provider-side, subject to the visibility rule that **the provider sees nothing before `Delivered`**. |
| `ProviderResponse` lifecycle | `domain-model.md` §5.3 | **2** — `Withdrawn`, `Expired`. `Superseded` has a row above; the three *kinds* are treated above and are not states. | `UX-12`, `UX-20`. |
| `Conversation` lifecycle | `domain-model.md` §5.4 | **2** — `Restricted`, `Blocked`, the two states a participant perceives as a change in what they may do. The remaining states of that lifecycle are retention and moderation concerns with no distinct participant-facing treatment specified here. | `UX-12`, `UX-20`. **A blocked party sees a policy-level reason only** — never who reported, never the report content. |

**The count, stated plainly so no downstream document repeats the old one:** **18 statuses are specified in §6**, and **15 further states across three families are carried by reference and are not specified here.** *"18 statuses"* was never the complete V1 status set and must not be quoted as one.

---

## 7. Motion

| Rule | Requirement |
|---|---|
| Reduced motion | The reduced-motion preference is respected wherever motion exists. Motion is reduced to an instantaneous state change, not merely shortened. |
| **No motion-dependent state change** | **No state may be communicated only by motion.** A transition, a pulse, a slide, or an auto-scroll is never the signal that something changed; the accompanying text is the signal. Under reduced motion the interface must lose nothing but the animation. |
| No auto-advance | Nothing advances a step, dismisses a message, or navigates on a timer. There is no carousel that moves on its own. Media never autoplays. |
| Transient messages | A confirmation or undo affordance must not be the only record of an action if it expires. Undo after a category change persists until navigation (§4.2), not for a timed interval. |

---

## 8. Bilingual expansion and locale honesty

Launch locale does not follow launch country: the owner-reported category mix is direct evidence of a Spanish-speaking segment inside the United States (`Q-020`, `A-015`). Bilingual capability is therefore a **layout and content-model constraint from the first screen**, not a later localization pass.

| Requirement | Rule |
|---|---|
| Expansion budget | **Spanish strings run materially longer than English.** Every label, button, status text, validation message, empty state, and navigation item must remain legible and operable when its string grows substantially relative to English. |
| **No fixed-width dependence** | **No control's width, and no layout's viability, may depend on the length of its English string.** Buttons, tabs, stage indicators, table headers, badges, and the named-stage composer indicator wrap or reflow. A control that truncates its own label is a defect, not a style choice; truncation with an accessible full name is acceptable only for user-authored content, never for interface text. |
| Where it bites first | The named-stage progress indicator (§5), the `RequestIntake` and publication status texts (§6) — which are already long by design because they must say what they mean and not use a badge — the requirements checklist (`UX-24`), and the operator queue table headers. These are the surfaces to test expansion against. |
| `contentLocale` | **`contentLocale` is explicit on provider narrative and on messages.** A surface renders the locale it was authored in and states it where two locales can appear together. Interface locale and content locale are separate and may differ. |
| Machine translation | **Machine-translated content is explicitly marked as machine-translated wherever it appears**, so that **no surface presents a translation as the provider's own words.** The original is reachable from the translation. A translated `ProviderResponse` or `Message` is never delivered as if the provider wrote it, because the provider is accountable for what they wrote and not for what a translator produced. Notification bodies carry no content that could be mistranslated into a claim — the allowlist already forbids conversation content, request free text, and offer terms (`ADR-010`). |
| Language switching | The interface locale is a setting on `UX-15`, not a URL guess and not an IP guess. Switching locale does not translate user-authored content and does not change `contentLocale`. |
| Search and matching | Filtering and eligibility operate on language-neutral identifiers; the governed synonym and alias table folds Unicode, diacritics, and case (`norteño` → `norteno`). Cross-language discovery is a content-model question, not a search-engine question (`Q-020`, `A-015`). |

### 8.1 `Q-020` — canonical locale for a profile and for a request conversation

`Q-020` is **P04-named and OPEN**. P04 records a recommendation and does **not** claim it resolved. Decision owner per `docs/01-product/open-questions.md`: owner + P04 + content-operations capacity + compliance.

- Evidence: `PROPOSED`. Provenance: `TECHNICAL_DISCOVERY`. **Recommendation: a `ProviderProfile` has one provider-declared canonical `contentLocale`, and a request `Conversation` has one canonical locale fixed at request creation from the customer's interface locale, with every `Message` carrying its own `contentLocale`.**
- Reasoning: a profile with no canonical locale cannot have a correct language link, cannot be indexed honestly, and gives moderation no baseline. A conversation whose locale drifts per message makes machine-translation marking ambiguous — there is no "original" to mark against. Fixing the conversation locale at creation, while still stamping each `Message`, keeps the marking rule decidable without forcing either party to write in a language they do not use.
- What it does not decide: **who authors additional locales** — provider, operator, or machine — which is the substantive limb of `Q-020` and carries the content-model and moderation-load consequences. P04 does not answer it. `OPEN`.
- Consequence if the recommendation is rejected: the machine-translation marking rule in §8 needs a different anchor, and the language-link correctness requirement in `docs/02-architecture/internationalization-architecture.md` needs restating. Nothing else in this document changes.

---

## 9. Media accessibility

| Requirement | Rule |
|---|---|
| Alternative text ownership | **Alternative text is a property of the `MediaReference` placement**, not of the stored file and not of the rendering surface. The same asset placed in two contexts may need two descriptions, and a description authored for a profile hero is wrong in a gallery. |
| Publication quality | **Alternative text is a publication-quality item, not an afterthought.** It belongs in the provider's requirements checklist (`UX-24`) alongside media rights acknowledgement, with the same treatment: stated at upload time, with the reason it is required. |
| Whether it gates publication | `PROPOSED`. `publicationGateMet`'s governed item set is defined in the onboarding model and P04 does not silently add a gate to it. **P04's recommendation is that missing alternative text is surfaced as an unmet publication-quality item on the checklist, not as a hard publication gate**, because a hard gate on provider-authored prose is the kind of requirement that produces "image" typed into every field, which is worse than an honest absence. `OPEN` — a decision that raising this to a gate must not retroactively unpublish existing profiles. |
| Decorative media | A decorative placement is marked decorative explicitly. Empty alternative text with no decision recorded is not the same thing and is not acceptable. |
| Uploaded images | Images are hosted, with derivatives from a small fixed enumerated set and a visible processing state (`ADR-018`, §6 above). The processing state is announced per file, not only drawn. |
| **Audio and video** | **Audio and video are linked or embedded, never uploaded** (`ADR-018`). |
| **The limitation that must be stated, not hidden** | **The accessibility of externally embedded audio and video is outside Superola's control.** Whether a linked performance video has captions, whether its player is keyboard-operable, whether its controls have accessible names, and whether it autoplays are all properties of the third-party host. Superola cannot add captions to a video it does not store, cannot fix a player it does not ship, and must not imply otherwise. The requirements that follow are therefore the whole of what Superola can do, and they must be presented as such: the embed placement carries a text label naming what the media is and who hosts it; the link is operable and its purpose is stated in text so a user who cannot use the embedded player can still reach the source; the embed never autoplays where Superola controls that parameter; a dead external link is a real, visible state with an operator sweep and a queue (`UX-33`, `ADR-018`); and the provider-facing media surface states plainly that captions and player accessibility are the provider's responsibility at the host. **No Superola surface may claim that linked media is accessible.** |
| Media metadata | Metadata is stripped on re-encode (`ADR-018`). This is a privacy requirement with an accessibility side effect: no descriptive text may be recovered from a file, which is exactly why alternative text must be authored at placement. |

---

## 10. Responsive — per-journey phone-width evaluation

Web-first, responsive required; native mobile is future; **no native-mobile-specific interaction patterns** (`WA-04`, `DB-06`). Every journey below is evaluated at phone width. "Above the fold" here means the first screen at phone width without scrolling, and is a content-ordering statement, not a pixel specification.

| Journey | Surfaces | Phone-width requirement | Failure mode if ignored | Risk |
|---|---|---|---|---|
| Multi-step request composer | `UX-07` | **One question group per screen** with a persistent primary action that does not require scrolling past the questions to reach. The revealed archetype group is a new screen, not an expansion below the fold. The named-stage indicator is one line and does not consume a fixed header band. `I-1` opens as an inline list in flow, **not** as a full-screen sheet or any other native-app pattern. | The reveal happens below the fold; the user never sees it; abandonment is recorded at the wrong step and the fix is aimed at the wrong problem. | HIGH |
| Search results | `UX-03`, `UX-04` | **Single column.** The per-slot ranking explanation collapses to one line with the full explanation reachable in place. Filters are a separate screen or an in-flow disclosure with an explicit apply, never a sticky sidebar. No live facet counts (§5.6 recommendation), which also removes a per-filter count from an already tight row. | Filters consume the viewport; the customer never reaches a result; or the ranking explanation is dropped, which is a disclosure requirement and not decoration. | MEDIUM |
| Provider profile | `UX-05` | Leads with **identity, service, coarse `Place` label, and the request call-to-action above the fold**. The `V1 has no availability model` non-claim and the `RequestIntake` state are in that first screen, not below the media. Media is below identity, count-capped and size-capped, within the ~200 KB served-bytes design assumption per anonymous public profile view (400 KB is the reconsideration threshold). | The customer reaches the request action only after scrolling a gallery, and the intake state — the thing that determines whether the request is worth composing — is discovered last. | MEDIUM |
| Request review and send | `UX-08` | The full answer set is reviewable and each answer editable in place without losing position. The send action is unambiguous and states what happens next. | Editing an answer restarts the composer and the customer abandons at the last step, having done all the work. | MEDIUM |
| Identity and channel verification | `UX-09` | A single-purpose screen. The code entry is a native input; the resend action is visible without scrolling; the state that the request is **already durably stored as `PendingVerification`** is stated on this screen, because it is the reassurance that prevents abandonment here. | Verification abandonment — already the known cost of `WA-05` — is amplified by a screen that looks like the work might be lost. | HIGH |
| Quote and response, customer side | `UX-12` | The response kind is the first thing read. `PriceStatement` fields stack as labelled rows, never as a table that scrolls horizontally. The forward actions — reply, ask for clarification, report an outcome, withdraw — are reachable without traversing the whole thread. | A horizontally scrolling price table on a phone hides exclusions and conditions, which is the part of an offer most likely to matter and most likely to be disputed. | MEDIUM |
| Conversation | `UX-12`, `UX-20` | Thread plus composer. **No realtime affordances of any kind** — no presence, no typing indicator, no read receipt, no live-updating badge — because none exists (`system-architecture.md` §3). The composer does not occupy a fixed portion of the viewport. Anchored request and `ProviderResponse` entries stay visually distinct when everything is one column. | A phone layout that mimics a messaging app promises realtime the architecture cannot deliver, and the first unanswered message reads as a broken product rather than an asynchronous one. | MEDIUM |
| **Provider request-response surface** | `UX-19` | **Fully completable on a phone, end to end**: read the `EventContext` and every category answer, then send a `clarification`, a `decline` with an optional reason, or an `offer` with an optional `PriceStatement` — without a desktop. Archetype-conditional fields follow §4's reveal rules on a narrow screen. The three response kinds are equally prominent; `decline` is not buried, because burying it converts a decline into a `NoResponse` and corrupts the only response-behavior measurement V1 has. | The provider reads the request on a phone, cannot respond on it, defers to a desktop they may not open, and the request becomes `NoResponse`. | **HIGHEST** |
| Provider inbox, onboarding, editors, media | `UX-17`, `UX-18`, `UX-21`, `UX-22`, `UX-23`, `UX-24` | Usable at phone width. `I-3` file selection uses a native file input; drag-and-drop is desktop enhancement only. The requirements checklist is a single column and each item states why and what it unlocks. `Stale` → `reconfirm` is **one action from a notification link on a phone** — this is the highest-frequency provider maintenance action in the product and it must never require a desktop. | Providers stop maintaining freshness; `Stale` supply accumulates; the freshness indicator becomes the dominant thing customers see. | HIGH |
| **Operator queues** | `UX-26`–`UX-34` | **Operator queues are the one surface class allowed to be desktop-first, and P04 states that rather than assuming it.** They remain keyboard-operable and must not break at narrow width, but multi-column case tables, side-by-side case detail, and filter panels may be optimized for a wide viewport. | None material. The failure mode of the opposite choice is worse: phone-optimizing nine queues spends the scarcest budget in the project on the smallest, most controllable, and most training-available audience. | LOW |

### 10.1 Why the provider request-response surface is the single highest-risk mobile surface in the product

Evidence: `PROPOSED`. Provenance: `TECHNICAL_DISCOVERY`, resting on `DB-06` and `WA-04`.

**Providers are mobile-first in practice — performers working from phones** — and `UX-19` is where that meets the surface with the least tolerance for failure. Four properties compound:

1. **It is the only surface whose failure is invisible.** A customer who cannot complete a composer abandons and is measured as composer abandonment. A provider who cannot complete a response on a phone produces `NoResponse`, which is a **system-observed state, not a provider action** — so it is recorded as silence, and silence is indistinguishable from a provider who chose not to answer. The product would be measuring a UI failure as a behavioral one.
2. **Its failure is the marketplace's core failure.** The V1 loop ends at a `ProviderResponse`. A response surface that does not work on the device providers actually hold breaks the only two-sided loop V1 has, and does so without any error to point at.
3. **It is structurally the most complex provider surface**, not the simplest: archetype-conditional fields, three distinct response kinds, an optional `PriceStatement` with six labelled parts, and the full `EventContext` and category answers to read before responding — all in one column.
4. **The mitigation is not "make it shorter".** The `EventContext` must be readable in full before responding, because a response given without reading it is the failure mode the whole progressive-intake model exists to prevent. The requirement is layout and ordering discipline at narrow width, not content reduction.

**Evaluation that would confirm or refute this:** a moderated pass through `UX-19` on a phone, with providers from the launch archetype cohort, measuring completion of each of the three response kinds and time to first response by device class, plus instrumenting response-by-device against `NoResponse` rate once the surface ships. Neither has been run. `SRC-006` is NOT RECEIVED, so **no completion rate, response rate, or device-share figure may be stated anywhere.**

---

## 11. Constraints this baseline places on everything downstream

1. **No compliance claim may be derived from this document.** No conformance level, no standard, no badge, no audit assertion, in any surface, artifact, or owner-facing material. Whether an audit is commissioned and against which standard is an owner decision that P04 does not make and does not pre-empt.
2. **Every island's degraded server-rendered path is a shipping requirement, not a contingency.** `I-1` without a plain-select fallback, `I-2` without one server-rendered step per POST, or `I-3` without a native multi-file POST does not ship. The island budget is three against a reconsideration trigger of five (`ADR-020` recommendation); a fourth island must restate its degraded path before it is accepted.
3. **A reveal that steals focus to an input, or that reorders answered questions, is a defect** — not a polish item, not a backlog entry.
4. **A status rendered without text does not ship.** This binds **every** status in the product, including the three lifecycle families §6.1 carries by reference — it is not limited to the 18 rows §6 specifies. **The §6 table is not the complete V1 status set** and must not be cited as one; it is the set whose non-color treatment P04 has specified. Adding a status to §6 adds a row, and a row without a text requirement is not a row.
5. **`Suspended` must remain indistinguishable from `Deactivated` on every customer-facing surface**, including in text, wording, layout, and absence patterns. Any accessibility affordance that would distinguish them is prohibited.
6. **No fixed-width interface control may depend on English string length.** This binds before any visual design begins; discovering it after a component set exists is the expensive order.
7. **`Q-020` is OPEN.** The canonical-locale recommendation in §8.1 is `PROPOSED` and the authorship limb is unanswered. `G-06` is UNSATISFIED and P04 proceeds on `WA-01` under explicit David authorization.
8. **Nothing here is measured.** Every requirement is an argued design position. The evaluations named in §4.3 and §10.1 are what would produce evidence, and `SRC-006` is NOT RECEIVED.
9. **P04 does not decide** whether an accessibility audit is in the V1 budget, which standard applies in which market, or whether alternative text becomes a hard `publicationGateMet` item. Those are owner and P06 decisions and this document does not pre-empt them.
