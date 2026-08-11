# Superola Product Context

## Purpose and evidence boundary

This is the first durable orientation document for future agents and reviewers. It summarizes the P00 evidence and directives now separated in the source register. Claims retain the repository evidence labels; owner-reported data is not independently audited. See `SRC-001` for the durable owner-evidence representation and `SRC-010` for David's P00 directives.

## What Superola is

- Evidence: `CONFIRMED`. Provenance: `DAVID_DIRECTIVE`. The repository must treat Superola as a two-sided marketplace connecting event customers and service providers, not only as a directory.
- Evidence: `PROPOSED`. Provenance: `DAVID_DIRECTIVE`. Candidate customer event contexts include weddings, quinceañeras, parties, dances, corporate events, private celebrations, and other events; P01 must validate this scope.
- Evidence: `CONFIRMED` (owner-reported, unaudited). Provenance: `OWNER_INTERVIEW`. Reported provider registrations included bands, norteño groups, mariachis, DJs, venues, limousine/transportation, cake, and makeup services.
- Evidence: `PROPOSED`. Provenance: `DAVID_DIRECTIVE`. The broader candidate marketplace may also include catering, photography, decoration, and other event services.
- Evidence: `PROPOSED`. Provenance: `TECHNICAL_DISCOVERY`. The working marketplace journey is discovery → evaluation → availability → communication → quote/pricing → possible booking → interaction → review. Exact V1 stages remain unvalidated.

## Current state

- Evidence: `CONFIRMED` (owner-reported, unaudited). Provenance: `OWNER_INTERVIEW`. The legacy platform was built quickly, received limited ongoing attention, and organically accumulated approximately 43,000 provider registrations.
- Evidence: `CONFIRMED` (owner-reported, unaudited). Provenance: `OWNER_INTERVIEW`. Activity occurred between people seeking services and registered providers.
- Evidence: `CONFIRMED` (owner-reported, unaudited). Provenance: `OWNER_INTERVIEW`. Platform reach included the United States, Mexico, and possibly other countries; activity by country is not verified.
- Evidence: `CONFIRMED` (owner-reported, unaudited). Provenance: `OWNER_INTERVIEW`. Approximate category counts included 4,000 DJs, 5,000 bands, 4,000 norteño groups, and 5,000 mariachis.
- Evidence: `ASSUMPTION`. Provenance: `EXTERNAL_SECONDARY`. The P00 brief references unavailable prior analysis suggesting WordPress/MyListing or similar tooling; no source artifact or direct platform observation currently supports that implementation claim.
- Evidence: `CONFIRMED` (owner-reported). Provenance: `OWNER_INTERVIEW`. Location behavior is unreliable, data became saturated, provider-entered category creation likely damaged taxonomy quality, and the owner wants a different design and a properly rebuilt product.

## Intended transformation

- Evidence: `CONFIRMED`. Provenance: `OWNER_INTERVIEW`. The owner wants a new product built properly rather than indefinitely patching the current implementation.
- Evidence: `PROPOSED`. Provenance: `TECHNICAL_DISCOVERY`. The working model includes trustworthy service/geographic discovery, provider profiles, availability, in-platform communications, requests/quotes, explicit monetization, and possible later transactions and reputation features.
- Evidence: `PROPOSED`. Provenance: `DAVID_DIRECTIVE`. Web is the candidate initial channel, SEO is a first-class evaluation concern, and domain/API work should preserve a future mobile path without requiring mobile in the first release.
- Evidence: `PROPOSED`. Provenance: `DAVID_DIRECTIVE`. The product should evaluate support for the United States, Mexico, and potentially broader markets; this is not an approved first-release geography.
- Evidence: `ASSUMPTION`. Provenance: `TECHNICAL_DISCOVERY`. The United States and Mexico may be priority initial launch markets; this requires an explicit owner/business decision.

## Business opportunity

- Evidence: `CONFIRMED` (owner hypothesis, unvalidated). Provenance: `OWNER_INTERVIEW`. Providers may pay a modest recurring subscription after an initial free period; example figures around USD 40/month were illustrative rather than approved pricing.
- Evidence: `CONFIRMED` (owner hypothesis, unvalidated). Provenance: `OWNER_INTERVIEW`. Category + geography could define sponsored placement inventory; rotating promoted positions and illustrative pricing around USD 100/month were discussed but not approved.
- Evidence: `CONFIRMED` (business strategy idea). Provenance: `OWNER_INTERVIEW`. A portion of revenue from a geography might be reinvested in local advertising. Current treatment as non-software scope is provenance `DAVID_DIRECTIVE`.
- Evidence: `PROPOSED`. Provenance: `TECHNICAL_DISCOVERY`. Organic marketplace traction, if verified and usable, could reduce cold-start risk; provider quality, activity, ownership, consent, and migration viability remain unknown.

## Structural product considerations

- Evidence: `PROPOSED`. Provenance: `DAVID_DIRECTIVE`. Messaging and notification delivery are separate capabilities.
- Evidence: `PROPOSED`. Provenance: `DAVID_DIRECTIVE`. Organic ranking and paid placement are separate mechanisms and trust surfaces.
- Evidence: `PROPOSED`. Provenance: `DAVID_DIRECTIVE`. Geography, service area, taxonomy, availability, internationalization, SEO, monetization entitlements, privacy, and cost require structural evaluation from the start.
- Evidence: `PROPOSED`. Provenance: `DAVID_DIRECTIVE`. Internationalization discovery must cover UI localization, system-taxonomy translations, vendor-generated multilingual content, currency, timezone, locale/date formatting, geography, measurement units, search language, and translated SEO pages.
- Evidence: `PROPOSED`. Provenance: `TECHNICAL_DISCOVERY`. Natural-language intent may augment deterministic marketplace filtering and ranking; it should not replace a sound search foundation.
- Evidence: `ASSUMPTION`. Provenance: `TECHNICAL_DISCOVERY`. External provider discovery and claimable profiles may be useful, but any implementation requires platform-terms, privacy, copyright, consent, accuracy, and legal validation.

## Known constraints

- Evidence: `CONFIRMED`. Provenance: `DAVID_DIRECTIVE`. David is the final human technical decision-maker for this discovery repository and will communicate with the Superola owner.
- Evidence: `CONFIRMED`. Provenance: `DAVID_DIRECTIVE`. Material decisions must be persisted because external architecture/orchestration review occurs outside Codex.
- Evidence: `CONFIRMED`. Provenance: `OWNER_INTERVIEW`. The owner expects constructive technical feedback, spending discipline, brainstorming before commitment, and specialist hiring only when justified.
- Evidence: `CONFIRMED` (informal). Provenance: `OWNER_INTERVIEW`. Roughly six months was discussed in relation to a serious production V1.
- Evidence: `CONFIRMED`. Provenance: `DAVID_DIRECTIVE`. Treat the six-month discussion as a planning hypothesis, not a contractual deadline, until scope and capacity are validated.
- Evidence: `CONFIRMED`. Provenance: `DAVID_DIRECTIVE`. Employer-facing artifacts must support Spanish and English.
- Evidence: `CONFIRMED`. Provenance: `DAVID_DIRECTIVE`. P00/P00.1 must not select a final stack or build production software.

## Known unknowns

- Actual legacy user, provider, activity, content, location, message, review, consent, spam, and data-quality characteristics.
- Owner-approved definition of MVP, booking/payment scope, availability semantics, marketplace transaction boundaries, and launch geographies.
- Validated willingness to pay, plan structure, trial model, sponsored inventory rules, pricing, and unit economics.
- Contents of the owner's feature/monetization document and existing UI/UX materials.
- Traffic, SEO, analytics, infrastructure, operational, compliance, and migration constraints.

See `assumptions.md` and `docs/01-product/open-questions.md` for owned registers rather than resolving these in chat.
