# Legacy Migration Dependency — P05 Required

## Current status

P05 is `NOT STARTED — INTERNAL ACCESS REQUIRED`. This document does not execute P05, classify internal records, recommend a migration strategy, or authorize use of legacy data.

The public audit establishes `OBSERVED — LIVE_PLATFORM` facts about public surfaces as of 2026-08-13. It does not establish internal record counts, accounts, ownership, consent, activity, communication, claims, historical monetization, booking/payment state, or migration viability.

## Required dependency chain

```text
owner approval
→ authorized access and data-handling controls
→ P05 legacy audit
→ audited classification and lawful-use findings
→ owner + counsel migration decision
→ approved implementation/import plan
→ controlled import, claim, suppression, or decommission work
```

No implementation step may jump over P05. A fresh-supply pilot may proceed independently if legacy data is excluded.

## What public reconnaissance can and cannot say

| Public evidence can support | Public evidence cannot support |
|---|---|
| The site publishes **43,361 public listing URLs**, reproducible from public endpoints. | The number of providers, users, accounts, registrations, distinct businesses, active businesses, or migration-ready records. |
| A stratified sample of 53 listing pages showed a material ownership-concentration signal: 47 referenced user id `1`; the real role/owner is unknown. | Corpus-wide ownership distribution, who created each listing, consent, claim/control, or the percentage that should migrate. |
| Public category/location defects and inconsistent behavior are observable examples/signals. | Full taxonomy/geography quality, correct mappings, activity, reachability, or publishable yield. |
| “Solicitud de Reserva” publicly renders a contact-intake form and does not publicly demonstrate date reservation, quote lifecycle, payment, or statuses. | Whether a request/booking/payment capability or history exists internally. |
| “Destacado” was observed as a suggestion-group heading; no current public paid listing product was observed. | Whether “Destacado” was historically paid, whether subscriptions/promotions existed, or whether private sales/contracts exist. |
| Public provider phone exposure, forced Customer/Business Owner registration, Digital Consulting, and merchandise shop surfaces exist. | Lawful basis, terms acceptance/versioning, shared account semantics, revenue, staffing, order history, strategic importance, or preservation requirement. |

The authorization brief mentions money-back-guarantee marketing, but the canonical public audit does not record that observation. Treat it as an owner question until a source is registered.

## Decision matrix: migration versus fresh supply

No option is selected before P05.

| Option | Advantages | Risks / disadvantages | Conditions under which it may fit | Evidence P05 must provide |
|---|---|---|---|---|
| A. Mostly migrate legacy | Potentially preserves the broadest supply, provider recognition, content, and SEO continuity. | Highest legal/privacy, ownership, duplicate, stale-data, taxonomy/location, media-rights, reachability, claim, and operator risk; can import false supply at scale. | High lawful/useable yield, reliable ownership/reachability, manageable normalization, clear canonical/cutover control, and lower cost per publishable provider than fresh acquisition. | Internal counts/statuses; distinct-business dedupe; ownership/control; provenance/lawful basis; consent; reachability; activity; taxonomy/location; media rights; claims; publishable yield; review time/cost; suppression/rollback. |
| B. Claim-first legacy activation | Requires providers to establish control; can preserve recognition without auto-publishing. | Enumeration/fraud, invalid contact channels, low claim rate, disputes, support load, and invitation lawful-basis risk. A claim still does not prove profile quality. | Approved outreach/legal basis, reliable control challenges, bounded cohort, operator capacity, and clear expectation that claim creates/feeds a Draft profile. | Contact-channel quality; claim history; account/listing mapping; lawful invitation basis; failed-attempt/fraud rates; expected queue volume; per-field provenance; completion/publication yield. |
| C. Curated partial migration | Limits exposure, targets useful market cells, and creates measurable learning with reversible batches. | Selection bias, manual review cost, incomplete continuity, duplicate/coexistence complexity. | A clearly segmentable high-quality cohort and audit evidence that controlled import beats fresh onboarding for that cohort. | Cohort segmentation, sampling method, quality/yield, manual minutes/cost, duplicate/crosswalk quality, lawful basis, rollback/suppression and provider activation results. |
| D. Largely fresh provider onboarding | Opportunity to capture current, versioned consent under an approved flow; controlled taxonomy/geography; uniform publication gates; no dependency on imported supply for pilot. | A new flow can still implement consent incorrectly; may forgo useful content/SEO/provider relationships; legacy obligations and cutover still exist; supply acquisition may be slow. | Fresh curated acquisition is feasible, the approved consent flow is verified, and quality/economics outperform legacy activation; legacy preservation value is low or unsafe. | Legacy SEO/traffic/canonical value; retention/suppression obligations; decommission constraints; consent-flow review; cost of fresh acquisition/onboarding versus audited legacy activation. |
| E. Hybrid | Allows fresh baseline plus selectively audited legacy cohorts and gradual SEO/identity transition. | Highest coexistence complexity: duplicate identities, crosswalk, redirects, two operating paths, claims, and ambiguous source of truth. | Clear cohort rules, structural separation, strong dedupe/canonical ownership, staged reversible batches, and sufficient operator capacity. | Everything needed by C and D plus identity crosswalk, canonical/redirect authority, coexistence duration, cutover triggers, backlog/capacity, and rollback evidence. |

## P05 audit questions before any classification

P05 must establish, with definitions and reproducible evidence:

- counts by internal record type and status, language duplicates, and distinct businesses after deduplication;
- account-to-listing ownership/control and how records were created;
- consent/terms history, lawful basis for current publication, contact, claim, reuse, retention, and deletion/suppression;
- contact reachability and verification status;
- activity/freshness, request/message/claim history, and any internal booking/payment/guarantee state;
- taxonomy, geography, service-area, price, media-rights, and profile-quality distributions;
- current/historical monetization, “Destacado,” subscription, promotion, Digital Consulting, and shop evidence where access permits;
- SEO/canonical/redirect ownership and the cost of coexistence or decommission;
- per-option publishable yield, operator minutes/cost, error rate, queue volume, rollback, and suppression behavior.

P05 should classify candidate records only after lawful access. Candidate outcomes may include migrate, normalize, require claim, re-consent, review manually, deduplicate, suppress, discard, or archive. P06 sets no percentages.

## Controls that remain binding after P05

- `LegacyProviderRecord` is structurally non-discoverable and cannot receive requests.
- A successful claim binds control and may seed a new `ProviderProfile` in `Draft`; it does not publish.
- Imported and fresh profiles pass the same archetype-aware publication gates.
- Each imported field carries source/provenance; batches are enumerable and reversible.
- Suppression is checked before creation so later imports cannot resurrect deleted/suppressed records.
- Authorized exports and production-derived data remain outside this repository.
- Claim interfaces must not reveal whether a target record/contact exists.
- No paid entitlement, owner request, or cold-start pressure bypasses trust, safety, eligibility, or lawful-use gates.

## Roadmap integration

- Stages 1–5 may proceed with curated fresh supply if the owner selects Branch L and legacy is excluded.
- P05 may run as a parallel access-dependent track after owner approval and controls.
- An audited cohort enters Stage 2 only after a separate migration decision and operating-capacity check.
- Legacy claims, import, and migration are not baseline MVP capabilities merely because public listing URLs exist.
- Transactional Branch T has a higher legacy evidence bar; migrated public/contact data cannot establish transaction identity, availability, payment, or review truth.

## Stop conditions

Pause or reject a migration path if lawful basis is absent, ownership/control cannot be verified safely, suppression/rollback cannot be proven, publishable yield is poor, reachability/quality undermines customer trust, manual queue load exceeds the approved stop rule, or cost per publishable provider does not justify the path versus fresh onboarding.
