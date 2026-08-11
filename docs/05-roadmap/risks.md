# Risk Register — Initial Discovery Risks

| ID | Risk | Evidence | Impact | P00 response / next owner |
|---|---|---|---|---|
| R-001 | Reported legacy scale may conceal stale, duplicate, invalid, spam, or unconsented records. | `CONFIRMED` (`OWNER_INTERVIEW`): the owner reported the count; numerical accuracy and record quality remain unverified. | High | Preserve A-001; P05 authorized audit. |
| R-002 | Full marketplace vision may be treated as a single MVP. | `PROPOSED` broad vision (`TECHNICAL_DISCOVERY`); multiple owner concepts are confirmed in SRC-001, while the detailed owner document is pending. | High | Q-001/Q-002; P01 then P06. |
| R-003 | Multiple countries may introduce unbounded payment, tax, legal, language, support, and location scope. | `CONFIRMED` owner-reported historical reach (`OWNER_INTERVIEW`); multi-country launch is `PROPOSED` (`DAVID_DIRECTIVE`) and launch geography is unknown. | High | Q-003; country scope before architecture commitment. |
| R-004 | Sponsored placement may damage relevance and trust if modeled as a generic featured flag. | `CONFIRMED` promotion concept (`OWNER_INTERVIEW`); rules unvalidated. | High | Preserve organic/sponsored separation; Q-010. |
| R-005 | In-platform communications may face adoption, abuse, consent, retention, and privacy problems. | `PROPOSED` intent (`DAVID_DIRECTIVE`); owner policy and user behavior are unvalidated. | High | Q-008; product/compliance/security discovery. |
| R-006 | External profile acquisition may violate terms, privacy, copyright, or consent expectations. | `PROPOSED` idea (`DAVID_DIRECTIVE`) requiring business ownership and legal/technical validation. | High | No implementation; platform-specific legal/technical review. |
| R-007 | Familiar technologies may be selected before requirements and total cost are understood. | `CONFIRMED` candidate background/preferences (`DAVID_DIRECTIVE`); no decisions. | Medium/High | Technology radar holds all candidates; P03 + ADR gates. |
| R-008 | Six-month discussion may become a perceived promise before scope and team are known. | Discussion is `CONFIRMED` (`OWNER_INTERVIEW`); non-contractual treatment is `CONFIRMED` (`DAVID_DIRECTIVE`). | High | Preserve non-contractual qualifier; P06 ranges/dependencies. |
| R-009 | Pending feature and UX artifacts may contradict current context or each other. | `CONFIRMED` (`OWNER_INTERVIEW`): materials exist but are unavailable. | Medium/High | Source register and reconciliation step in P01 Track B. |
| R-010 | Poor taxonomy/geography data may undermine search, SEO, promotions, and migration simultaneously. | `CONFIRMED` owner-reported legacy issues (`OWNER_INTERVIEW`). | High | P01 taxonomy needs + P05 data audit + P02 model. |
