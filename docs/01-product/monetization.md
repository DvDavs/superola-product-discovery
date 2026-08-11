# Monetization — P01 Recommendation

> Status: `PROPOSED — OWNER VALIDATION REQUIRED` except where an owner concept is explicitly marked `CONFIRMED`. No price or package is approved.

## Provider subscription

- `CONFIRMED (owner hypothesis, unvalidated)`; provenance `OWNER_INTERVIEW`: providers may pay a modest recurring subscription after a free period.
- `CONFIRMED (illustrative, not approved)`: approximately USD 40/month and a possible three-month free period were discussed. These are not P01 recommendations or validated willingness to pay.

### Alternatives evaluated

| Approach | Value | Cost/risk | P01 disposition |
|---|---|---|---|
| Charge at registration/publication | Early revenue test. | Adds supply friction before Superola proves demand; confounds willingness-to-pay with cold-start and legacy quality. | `REJECTED` for initial launch hypothesis. |
| Free onboarding plus fixed calendar trial | Simple to explain. | Trial can expire before a provider is publishable or receives a meaningful opportunity. | Not recommended without validation; time alone is a weak value clock. |
| Free onboarding plus value-aware trial, then simple paid entitlement | Lets providers experience discovery/requests and gives Superola conversion evidence. | Requires clear trial start/end, entitlements, billing/compliance, support, and enough local demand. | `PROPOSED` staged recommendation. |
| Permanently free supply with later promotion only | Maximizes onboarding and postpones subscription billing. | Does not test the owner-confirmed subscription model and may make promotion distort the product. | Keep as fallback experiment if subscription evidence fails. |

### Recommended staging

**V1.0 launch:** free onboarding/publication eligibility and value events only; measure profile activation, discovery, requests, response, quote/offer, and outcome signals. Do not build generalized plan, trial, proration, or entitlement automation. A future trial clock should not begin before a provider is publishable and able to receive relevant demand. Do not promise an arbitrary free-period duration.

**V1.x monetization gate:** introduce a simple provider subscription/trial only after all of the following are true:

- the owner validates the package intent and charging policy;
- selected category/geography cohorts show credible supply and meaningful demand;
- providers can observe attributable value, not just impressions;
- basic entitlements and downgrade/cancellation behavior are understandable;
- billing geography, currency, tax, receipts, refunds, support, privacy, and accounting responsibilities are accepted;
- trial activation, conversion, retention, and churn reasons can be measured.

This stages collection and implementable entitlements, not the business idea. Later discovery may evaluate a clean monetization boundary without selecting or building it in P01.

## Sponsored placement

- `CONFIRMED (owner concept, unvalidated)`; provenance `OWNER_INTERVIEW`: paid visibility by category + geography, potentially with rotating inventory.
- `CONFIRMED (illustrative, not approved)`: approximately ten positions and USD 100/month were discussed. P01 does not validate either number.

### Staged recommendation

**V1.0:** establish a credible organic marketplace and ranking. Do not sell sponsorship merely because a result page exists.

**V1.x or later:** test sponsored inventory only when:

1. the category/geography has sufficient trustworthy eligible providers and meaningful customer demand;
2. organic eligibility/relevance/quality are defined and measured independently;
3. sponsored supply is scarce enough to constitute sellable inventory and providers show demand for it;
4. disclosure, allocation/rotation, eligibility, fairness, cancellation, and moderation policies are owner-approved;
5. sponsored impressions, clicks, requests, lead quality, and complaints can be attributed separately;
6. the operator can support sales, billing, disputes, and reporting without disproportionate manual work.

Payment must never bypass profile quality, trust, or category/geography eligibility. Sponsored results must be visibly and semantically distinct in human and machine channels.

## Local reinvestment

- `CONFIRMED (business strategy idea)`; provenance `OWNER_INTERVIEW`: part of local revenue might fund local advertising.
- `CONFIRMED`; provenance `DAVID_DIRECTIVE`: this remains business strategy, not P01 software scope.

Before using it as a product requirement, define unit economics, attribution, budget authority, geography, and whether the program is operationally sustainable.

## Evidence the monetization model still needs

- Which provider outcome is valuable enough to pay for: publication, qualified requests, conversion tools, analytics, or another capability.
- Whether willingness to pay differs by category, geography, maturity, and request volume.
- What stays free so the marketplace can maintain adequate supply and customer choice.
- Trial start event, eligibility, duration/usage basis, re-entry, downgrade, cancellation, and reactivation policy.
- Provider subscription tax/currency/support responsibilities in the approved launch market.
- Organic marketplace liquidity and provider demand before sponsored inventory is sold.

Transaction fees on customer booking/payment are not evaluated as V1 revenue because booking, payment, and payout are explicitly deferred from the recommended launch slice.
