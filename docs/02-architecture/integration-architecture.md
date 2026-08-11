# Integration Architecture — P02/P03 Placeholder

> Status: NOT STARTED. External providers and protocols remain unselected.

Candidate integration categories include identity, payments/subscriptions/payouts, email, SMS, push, maps/geocoding/places, object storage/CDN/media processing, analytics, anti-abuse, observability, automation, and AI models.

Every material integration evaluation must cover:

- business capability and owning domain boundary;
- synchronous/asynchronous contract and failure behavior;
- retries, idempotency, ordering, rate limits, reconciliation, and observability;
- data classification, residency, retention, consent, and provider access;
- development and operational effort, fixed/variable cost, lock-in, and exit path;
- outage/degradation behavior and the evidence threshold for adding complexity.

Messaging content and notification transports require separate ownership even if a vendor product spans both.
