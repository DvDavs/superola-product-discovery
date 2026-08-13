# GigSalad request-flow capture — David-supplied comparative input

> Status: `OBSERVED` competitor behavior **as supplied**. Provenance: `DAVID_DIRECTIVE` (`SRC-015`).
> **This is not an owner requirement, not canonical Superola behavior, and not evidence that Superola must copy GigSalad.** No Superola requirement derives from it.
> **It was NOT independently re-verified against first-party sources during P04**, and it is recorded at **step-shape granularity only**.

This file exists because `docs/04-ux/request-intake.md` set a guard on itself: the capture must be filed before a downstream document cites it as a source. The owner-facing `presentation/ux-preview-v0.1.md` cites the comparison, so the capture is filed here rather than left in a phase brief.

## What was supplied, and what it is not

David observed a GigSalad request flow and supplied its item list as comparative UX input with the P04 authorization. What reached the repository is **the list of things the flow asks for, in roughly the order it asks for them.**

**What the capture does NOT assert**, and what no P04 document may therefore assert from it:

- The exact wording of any question.
- The exact ordering, or how items are grouped into screens.
- Which items are required and which are optional. *(Where P04 text calls an item "required", that is P04's reading of the supplied description, not a verified property of the competitor's form.)*
- How many screens the flow spans, or how long it takes.
- Any friction, abandonment, or usability measurement. **No usability evidence exists in this repository** (`SRC-006` NOT RECEIVED), so no P04 document may state which of these questions is hardest for a real user.

## The captured items

Twenty-four items, as supplied. `docs/04-ux/request-intake.md` §2.2 maps each to Superola's disposition as `GC-01`–`GC-24`.

| # | Item as supplied |
|---|---|
| 1 | Previous request versus new request |
| 2 | Event type |
| 3 | Service selection |
| 4 | Service-specific questions |
| 5 | Preferred group size |
| 6 | Audience age |
| 7 | Equipment |
| 8 | Guest count |
| 9 | Venue / address |
| 10 | Event date |
| 11 | Start time |
| 12 | Duration |
| 13 | Optional fan-out to additional providers |
| 14 | Free-form event details |
| 15 | Planning intent |
| 16 | Pricing guidance |
| 17 | Minimum budget |
| 18 | Maximum budget |
| 19 | Indoor / outdoor |
| 20 | Travel-expense support |
| 21 | Stage size |
| 22 | Song requests |
| 23 | Submission, including name, contact details, and account creation |
| 24 | Additional provider suggestions after submission |

**"Approximately twenty steps" is the phrase used in P04 and in the owner-facing preview.** Twenty-four items are captured; several are grouped or conditional, so the number of screens a customer sees is smaller than twenty-four and is not known precisely. Where a P04 document needs a single number, it should say **twenty-four captured items** and, if it needs a step count, say that roughly twenty are presented to the customer as steps — with this file as the basis for the qualification.

## How this may and may not be used

**May be used for:** illustrating that a competitor's structured intake reaches this length; naming a specific field so Superola's disposition of it is checkable; showing that Superola declines certain items deliberately rather than by omission.

**May not be used for:** justifying a Superola requirement; asserting a friction ranking; asserting a conversion or abandonment effect; asserting what the competitor's customers or providers experience; or as a first-party observation by this repository.

## If it becomes load-bearing

`AGENTS.md` requires that competitor behavior be verified against current first-party sources with an access date before a recommendation turns on it. **If any Superola recommendation ever depends on current GigSalad behavior, this capture is insufficient** and the flow must be re-observed and recorded with an access date, in the manner of `docs/01-product/competitive-benchmark.md` (`GS-01`–`GS-09`), which is dated and first-party.

The P04 comparison does not depend on it. P04's argument for progressive intake stands on the structural claim that a universal form asks venue questions of a cake baker — which is true of any universal form and needs no competitor at all.

---

*Record dates — capture supplied by David 2026-08-12 with the P04 authorization, as conversation input rather than as a file, so no byte count or hash is recorded. Not re-observed. Registered in `docs/00-context/source-register.md` as part of `SRC-015`.*
