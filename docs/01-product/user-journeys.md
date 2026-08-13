# User Journeys — P01 Product States

> Status: `PROPOSED — OWNER VALIDATION REQUIRED`. These are product states for the recommended Marketplace Leads slice, not screens or an approved UX design. P04 owns interaction architecture.

## Customer journey — direct provider RFQ

```text
Need
  → Search
  → Evaluate results
  → Provider profile
  → Customer contact/account verification
  → Direct structured request/RFQ
  → Provider response
  → Quote or clarification
  → In-platform conversation
  → Customer-reported outcome (proceed / not proceeding / unknown)
  → V1 ENDS
```

| State | Customer outcome | System/product state | Important alternate states |
|---|---|---|---|
| Need | Express a service need in a launch market/category. | Intent exists; no durable request yet. | Unsupported category/geography; optional natural-language experiment falls back to deterministic entry. |
| Search | Find candidate providers by governed service and geography. | Meaningful query and filter context can be measured. | Empty results: explain coverage, allow correction/expansion without silently broadcasting, and measure unmet demand. |
| Evaluate results | Compare credible organic candidates. | Results distinguish relevance from any future sponsorship. | Thin/stale supply, duplicate profile, or reported content; no invented availability. |
| Provider profile | Assess service, area, media, facts, freshness, and request fit. | Marketplace profile is publishable and permissioned. | Provider not accepting RFQs; date/resource availability remains unknown until category semantics or provider response establish it; stale or deactivated profile. |
| Customer participation | Establish a reachable owner for a durable request. | `PROPOSED`: just-in-time contact/account control is verified before provider delivery; request status can be revisited, withdrawn, or closed. | Verification abandonment/failure; unreachable contact; privacy/consent concern. Pre-submit account and guest-then-verify remain alternatives for P04 validation. |
| Request | Send event/service context to the selected provider. | Exactly one recipient and one independent RFQ lifecycle, with customer consent, recipient provenance, status, abuse controls, and notification attempt. | Invalid/duplicate/spam request; provider stops accepting RFQs; uncertain direct-contact disclosure. |
| Provider response | Learn whether the provider can engage. | Response is quote, clarification, or decline; receipt and notification are observable. | No response, response deadline policy not yet approved, notification failure, provider stale. |
| Quote/offer | Receive category-appropriate scope, price basis, and conditions where feasible. | Minimum response may be a text offer; structured fields, revision, or expiry exist only when the selected category requires them. Quote remains distinct from booking/payment. | Customer changes material request details; provider revises/withdraws; provider needs more information; category may be quote-only. |
| Conversation | Clarify fit and terms in context. | Asynchronous text linked to the RFQ; notifications are separate delivery events. | Spam/harassment/report/block; unsafe content; parties request external contact; retention/contact policy unresolved. Presence, typing, read receipts, attachments, audio/video, SMS, and push are outside the baseline. |
| Decision | State whether the customer intends to proceed, not proceed, or has not reported an outcome. | Record `customer-reported proceed`, `not proceeding`, or `unknown`, plus reporter/time when available; make no transaction claim. | Outcome not disclosed; conversation goes off-platform; provider withdraws. |

### Customer exception behavior

- **Empty results:** do not convert the query into an automatic broadcast. Offer honest coverage guidance or adjusted search; preserve demand signal.
- **Unavailable/decline:** return the customer to discovery with a reusable private draft where privacy and consent allow. Any new RFQ requires deliberate provider selection and fresh confirmation; it is never an automatic reroute.
- **No response:** show the request state and provide a manual path back to discovery; response expectations and reminders require policy validation.
- **Quote change/expiry:** when the selected category uses revisions or expiry, retain the current status and require acknowledgement; never imply a booking. Do not impose this machinery universally.
- **Spam/abuse/report:** allow reporting/blocking, protect contact details, preserve moderation evidence, and provide safe exit.
- **Contact disclosure:** policy remains open (`A-010`). V1 proposes an in-platform thread and minimal disclosure; the owner must validate when direct details may be shared.
- **Multiple providers:** a customer may deliberately create separate one-recipient RFQs from a private draft; V1 has no recipient set, shared response window, automatic closure of other RFQs, or automatic fan-out. Simultaneous-open and closure behavior require owner/user validation.

## Provider journey — smallest coherent value loop

```text
Register
  → Establish business (or conditionally claim an approved imported profile)
  → Define services/category
  → Define geography/service area
  → Complete profile/media
  → Publication
  → Discovery
  → Direct request
  → Quote / clarify / decline
  → Conversation
  → Customer-reported outcome signal
  → Value/monetization evidence loop
```

| State | Provider outcome | Product state | Important alternate states |
|---|---|---|---|
| Register | Establish a reachable account. | Account/contact control confirmed to the defined minimum. | Invalid contact, duplicate account, abandoned registration. |
| Establish/claim business | Control the correct marketplace presence. | Fresh-provider establishment is baseline. Claim/dispute exists only for an approved audited imported/preloaded cohort; duplicates use manual exception handling. | Duplicate profile; conditional claim conflict; legacy ownership/consent uncertain. |
| Services/category | Describe governed offerings. | Provider selects supported taxonomy; cannot create uncontrolled public categories. | Unsupported service; multi-category breadth creates quality/entitlement questions. |
| Geography/service area | Receive requests for which the provider is eligible to be considered. | Base location and service area use category-appropriate semantics; the provider still evaluates actual feasibility and may clarify, decline, offer, or not respond. | Route/delivery/fixed-location model does not fit default; P02 category behavior required. |
| Profile/media | Become credible enough for evaluation. | Required publication facts/media are complete, permissioned, and moderated as needed. | Incomplete profile remains draft; media rights/content issue; quality remediation. |
| Publication | Enter eligible organic supply. | Publishable, active, freshness state; payment cannot bypass trust/quality gates. | Rejected/suspended/deactivated; provider cancels participation; stale revalidation needed. |
| Discovery | Receive relevant public exposure. | Organic eligibility and attribution are measurable. | No impressions due to weak local demand, profile quality, or relevance; future sponsorship remains separate. |
| Request | Receive a customer-selected opportunity with context. | Direct RFQ receipt and notification outcome are recorded. | Spam/duplicate/irrelevant request; notification failure; unavailable provider. |
| Respond | Decide quickly and honestly. | Quote, clarification, or decline; no-response state observable. | Stale/unresponsive provider; report; response after customer closed request. |
| Quote/offer | Describe category-appropriate scope and terms. | A minimum text offer may carry currency/basis and conditions; extra structured fields/version/expiry are category-gated. | Revision/withdrawal; request changed; cannot quote without more information. |
| Conversation | Resolve questions toward a decision. | Baseline is asynchronous text attached to the RFQ, with reporting/blocking. | Contact boundary friction, abuse, notification failure, off-platform movement. |
| Outcome signal | Understand whether marketplace activity produced value. | Customer-reported proceed/not-proceeding/unknown with provenance; not called a booking or conversion without proof. | Customer does not disclose outcome; attribution uncertain. |
| Value/monetization evidence | Compare repeated attributable value with participation effort. | Free onboarding/eligibility and value events only; paid entitlement automation waits for validated packaging and gates. | Provider cancels/deactivates; no value, poor-fit demand, or future package/pricing objection. |

### Provider lifecycle safeguards

- Incomplete profiles cannot silently become public or paid-promoted.
- Duplicate conflicts need manual exception resolution; claim/dispute operations exist only for an approved imported-profile cohort.
- Stale/unresponsive providers need notification/freshness checks and reversible deactivation, not permanent hidden penalties by default.
- Notification delivery failure is an operational state, not evidence that the provider ignored a request.
- Cancellation/deactivation must stop new request eligibility while preserving legally required marketplace records and user-visible closure states.

## Recommended V1 endpoint and later states

The proposed V1 ends after the customer-reported outcome signal. The following states are separate later slices and are not implied by quote acceptance:

```text
FUTURE: Booking commitment
  → Customer payment/deposit
  → Provider payout
  → Cancellation/refund/dispute/protection
  → Service completion
  → Review/reputation event
```

Each later state requires its own auditable event, compliance/operations model, and owner approval. A future review must state what Superola can verify: account participation, platform-recorded decision, platform booking, platform payment, or service completion are not equivalent.
