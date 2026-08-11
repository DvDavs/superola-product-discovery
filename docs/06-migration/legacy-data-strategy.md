# Legacy Data Strategy — P05 Placeholder

> Status: NOT STARTED. The reported ~43,000 registrations do not imply that all records should or may migrate.

## Audit scope

Profile users, businesses/providers, activity/last login, valid contact data, duplicates, categories/taxonomy, locations/coordinates, media, conversations, reviews, ownership, consent/terms, spam/bots, and staleness. Establish provenance and definitions before counting.

## Candidate classifications

`PROPOSED`: `CLEAN`, `MIGRATABLE`, `NEEDS_REVIEW`, `DUPLICATE`, `STALE`, `INVALID`. These labels require auditable criteria before use.

## Required controls

Authorized least-privilege access; no production data committed to this repository; profiling in an approved environment; data minimization; masked/synthetic examples; retention and deletion rules; migration rehearsal; reconciliation; rollback/cutover; account ownership and re-consent strategy; legal/privacy review.

## Decision outputs

P05 should recommend what to migrate, transform, re-verify, re-onboard, archive, or discard, with counts, quality/confidence, user communications, risks, cost, and approval gates.
