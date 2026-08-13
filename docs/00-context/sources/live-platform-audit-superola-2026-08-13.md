# Superola Live Platform — Public-Surface Audit

> Source: `SRC-002` (`LIVE_PLATFORM`). Access date: 2026-08-13. Target: `https://superola.com`.
> Method: unauthenticated observation of publicly served pages, published sitemaps, publicly readable REST endpoints, and client-side configuration objects served to any visitor.
> Default evidence label for §1–§14 and §17: `OBSERVED`; provenance `LIVE_PLATFORM`. §15 and §16 are `INFERRED ONLY` and are labelled `ASSUMPTION` without exception.

This is the bounded audit that `SRC-002` reserved. It records what the current platform *does*, not what the new product *should* do. Nothing here is a requirement, and an observation of legacy behaviour is never evidence that the behaviour is correct or should be preserved.

---

## 1. Scope and limitations

### 1.1 What was done

- Unauthenticated browsing of public pages: home, `explora` (search), listing detail pages across multiple listing types, account, add-listing, claim-listing, shop, digital-consultancy, purpose, and support.
- Retrieval of published sitemaps (`/sitemap.xml` and its 55 child sitemaps) and `robots.txt`.
- Reading of publicly readable REST endpoints that the site itself exposes without credentials.
- Reading of the client-side configuration object the theme serves to every visitor (`CASE27_Explore_Settings`), which declares listing types, filters, sorting, and map settings.
- Sampling of 53 listing detail pages drawn across the full sitemap range (both a 20-page and a 33-page sample, stratified across sitemaps 1–44).

### 1.2 What was deliberately not done

| Boundary | Reason |
|---|---|
| No account was created, and no credentials were entered anywhere | Prohibited action class; also would contaminate the platform's own data |
| No form was submitted — not the contact form, the booking-request form, the support form, the consultancy lead form, the review form, or the claim form | Submitting reaches a real recipient and writes real records |
| No purchase, cart action, or checkout step | Prohibited action class |
| No authenticated surface was observed | Requires credentials; see §18 |
| No bulk extraction of the listing corpus | `AGENTS.md` forbids approving scraping without legal, privacy, copyright, terms, and technical review. Sampling was deliberately small and stratified |
| No personal data was copied into this repository | Provider phone numbers are published in the clear on the live site (§6.3); this document records the **fact and the mechanism**, and cites no individual's number |

### 1.3 Confidence limits that apply to every section below

1. **Public surface only.** Everything here is the *rendered projection*. The database, the admin surface, and the authenticated user experience were not observed. A field absent from a page may still exist in storage, and a field present may be computed rather than stored.
2. **Sampling, not census.** Sampled findings rest on 53 listing pages out of ~43,361 public listing URLs. They are directional signals about the sample and are **not** audited counts, distributions, or corpus-wide facts. No sampled proportion in this document may be extrapolated to the corpus.
3. **Counts are the platform's own self-reported result counts** (§3.2), not verified record counts.
4. **One point in time.** 2026-08-13. The platform is live and can change.
5. **Theme-derived structure is not schema.** The platform runs a commercial WordPress theme (§1.4). Much of the observable structure is the theme's, and the theme ships demo content and demo taxonomies. Distinguishing *configured* from *left over* is frequently impossible from outside — and that distinction itself is a finding (§17.6).

### 1.4 Publicly observable stack signals

`product-context.md` recorded, as `ASSUMPTION` with provenance `EXTERNAL_SECONDARY`, that prior unavailable analysis suggested "WordPress/MyListing or similar tooling", noting no source artifact or direct platform observation supported it. **Direct observation now supports it for the components below.**

Scope of this resolution: **publicly observable legacy stack signals are resolved for these components** — each is evidenced by markup, generator tags, REST namespaces, or network calls served to any visitor. It is **not** a complete internal stack. Hosting, server runtime, database, caching topology, queueing, cron, email delivery, backup, CDN, deployment, and every server-side component that emits no public signal remain unknown, as do plugin settings, versions not advertised, and anything disabled or admin-only. Version strings are self-reported by the software. This is `OBSERVED`, provenance `LIVE_PLATFORM`:

| Component | Observed value | How observed |
|---|---|---|
| CMS | WordPress | `wp-content` / `wp-includes` / `wp-json` paths; `wp-singular`, `wp-theme-*` body classes |
| Theme | **MyListing** (`my-listing`) with a child theme (`my-listing-child`) | `wp-theme-my-listing wp-child-theme-my-listing-child theme-my-listing` body classes; `CASE27_*` JS globals (`case27` is the MyListing vendor namespace) |
| Listing post type | `job_listing`; taxonomy `job_listing_category`; tag taxonomy `case27_job_listing_tags` | Body classes, sitemap names, explore configuration |
| Page builder | Elementor 4.2.2 + Elementor Pro + ElementsKit | `meta[name=generator]`, plugin paths |
| SEO | All in One SEO Pro 4.7.0 | `meta[name=generator]`; sitemap header comment |
| Commerce | WooCommerce (+ PayPal namespace registered) | `woocommerce-js` body class; `wc/store/v1`, `wc/v3`, `paypal/v1` REST namespaces |
| Forms | Contact Form 7 **and** Fluent Forms (both active, used on different surfaces) | Form markup and REST namespaces |
| Multilingual | Polylang (`pll/v1`, `#pll_switcher`) | REST namespace; language switcher; `hreflang` alternates |
| Maps | **Mapbox** | `map_provider: "mapbox"`; Mapbox style/tile/font requests with a client-side access token |
| Anti-spam | Google reCAPTCHA | `_wpcf7_recaptcha_response` field; reCAPTCHA network calls |
| Analytics / pixels | Google Analytics 4 (`G-9ZW2W7RKMZ`), Google Site Kit 1.185.0, PixelYourSite Pro with Facebook and TikTok namespaces | Network requests; REST namespaces; generator tags |
| Performance | WP Rocket 3.18.3 | `meta[name=generator]` |
| Other registered namespaces | LoginPress, Redirection, Jetpack, Envato Elements, `daftplug-progressify` (PWA) | `/wp-json/` namespace index |

**Reconsideration note.** `product-context.md` should be amended to reflect that this implementation claim is no longer an `ASSUMPTION` resting on an unrecovered secondary source. `SRC-009` remains separately unresolved: obtaining the original analysis still matters for *everything else* it claimed, and this observation does not retroactively validate that source's methodology.

---

## 2. Account and authentication surfaces

All `OBSERVED`; provenance `LIVE_PLATFORM`. **No account was created and no credential was entered** — the observations below are of the rendered forms, not of the flows behind them.

### 2.1 Surfaces

| Surface | Path | Notes |
|---|---|---|
| Account (sign-in + register) | `/mi-cuenta/` (ES) / `/my-account/` (EN) | WooCommerce account page hosting a MyListing-styled combined sign-in/register panel |
| Register | `/mi-cuenta/?register` | Same page, register panel |
| Password reset | "¿Ha olvidado su contraseña?" link on the sign-in panel | Not followed |
| Add a listing | `/anadir-listado/` | Type chooser is public; the form behind it was not reached |
| Claim a listing | `/claim-listing/?listing_id={id}` | Gated: renders "You must be logged in to perform this action." |
| Report a listing | "Reportar" action on a listing | Href resolves to `/mi-cuenta/` — i.e. reporting is login-gated, and the gate is a plain redirect with no explanation |

### 2.2 Registration form fields, as rendered

`mylisting_user_role` (radio) · `first_name` · `last_name` · `username` · `email` · `password` · `profile_picture` (file) · `description` ("About Yourself") · `network` (select) + `url` (repeatable social profile rows).

Three observations carry design weight:

1. **The account is typed by marketplace role at registration.** `mylisting_user_role` offers exactly two values, rendered as **`Customer`** and **`Business Owner`**. The visitor must choose one before signing up.
   What is `OBSERVED` is precisely this: **the public registration form requires a mutually exclusive selection between `Customer` and `Business Owner` before an account can be created.** That is contrary in posture to `ADR-004` (*Account not typed by marketplace role*), which is `ACCEPTED` and was ratified by `SRC-014`, and it is first-party evidence for why the ADR exists.
   It does **not** establish what is persisted. Whether every persisted account carries this role, how the role is represented in storage, whether it is mutable, and how historical accounts created under earlier form versions are classified all require P05 verification (§18 Q-A1, §20.4).
2. **Profile picture upload advertises "Tamaño máximo del archivo: 1 GB."** A 1 GB ceiling on an avatar is an unbounded-ingest surface. Whether the server enforces something lower was not tested.
3. **The terms checkbox is not consistently present.** The English register form renders `terms_and_conditions` (checkbox) plus a privacy-policy statement. The Spanish register form rendered at `/mi-cuenta/` **did not include that field**. Spanish is the site's default language (§12.4). Consent provenance for Spanish-language registrations is therefore uncertain from the outside, and is a direct input to the re-consent question in `legacy-data-strategy.md`.

### 2.3 Social login

A `social-login-wrapper` container and the label "O conéctese con" / "Or connect with" are rendered, but **no provider buttons were present** in the served markup. Reading: the capability exists in the theme and is currently unconfigured or disabled. Not confirmable from outside.

### 2.4 What could not be observed

Session handling, password policy, email verification, account recovery, role permissions, provider dashboard, and every authenticated surface. See §18.

---

## 3. Provider / listing types

### 3.1 Declared types

`OBSERVED`. The explore configuration declares **12** listing types, each with its own id, filter set, and tab set:

`entretenimiento-musical` (2209) · `compositores` (306727) · `venue` (530) · `pasteles` (2307) · `comida-catering-abastecimiento` (2310) · `cars` (147) · `fotografos-y-camarografos` (3757) · `floristas` (3761) · `altavoces` (2298) · `actos-de-variedad` (2305) · `servicios-de-eventos` (2294) · `otros-servicios` (3749).

The **add-listing chooser presents 13** — the same 12 plus **`Evento`**. A type that a provider may create but that the public search surface does not expose is a discoverability gap, and it means "listing" is not a single concept even at the type level.

The type ids are not contiguous and span a wide range (147 → 306727), consistent with types added over a long period on top of theme defaults rather than designed as one set.

### 3.2 Corpus size and distribution

`OBSERVED` (platform self-reported result counts, 2026-08-13):

| Type | Results | Share |
|---|---:|---:|
| Música (`entretenimiento-musical`) | 29,203 | 67.4% |
| Todo Lo Demás (`otros-servicios`) | 6,823 | 15.7% |
| Fotógrafos y camarógrafos | 1,619 | 3.7% |
| Pasteles | 1,391 | 3.2% |
| Sitios para fiestas (`venue`) | 1,246 | 2.9% |
| Comida / catering | 1,178 | 2.7% |
| Floristas | 806 | 1.9% |
| Servicios de eventos | 765 | 1.8% |
| Transportación (`cars`) | 239 | 0.6% |
| Actos de variedad | 60 | 0.1% |
| Altavoces | 25 | 0.1% |
| Compositores | not returned | — |
| **Sum of observed counts** | **43,355** | |

**Independent corroboration by a second reproducible method.** The published sitemap set contains 44 `job_listing` sitemaps: 43 at the 1,000-URL cap plus one with 361, i.e. **43,361 public listing URLs**. Both methods are directly reproducible from public endpoints, and they agree to within 6 records (the unreturned `compositores` count).

**The unit is public listing URLs.** It is not users, not providers, not registrations, not accounts, and not businesses, and it must not be restated as any of those anywhere downstream. It counts published, publicly reachable listing URLs at one point in time — which also excludes any record that is unpublished, expired, or trashed, and may include language duplicates (§15).

Against that unit, the observed figure is of the **same order of magnitude** as the owner-reported "approximately 43,000 provider registrations" (`CONFIRMED`, owner-reported, unaudited). Magnitude agreement between two different quantities is a useful sanity check and nothing more; it neither audits the owner's figure nor converts URLs into registrations.

Two-thirds of the corpus is one type. Any migration, taxonomy, or search decision that is right for music and wrong for everything else will look correct in aggregate metrics.

---

## 4. Observable public fields

All `OBSERVED`. Field presence varies by listing and by type; the sample is 53 listing pages.

### 4.1 Fields rendered on a listing detail page

| Field | Notes |
|---|---|
| Title | Present on all sampled listings; quality varies wildly (§17.1) |
| Tagline / short descriptor | Common ("Sierreno de Chicago", "Los Mejores de Chicago") |
| Cover image and logo/avatar | Served from `wp-content/uploads/listing-uploads/{cover,logo}/YYYY/MM/` |
| Long description | Rendered under a type-specific heading ("Sobre Nosotros" for performers, "Sobre la Sede" for venues) |
| Location | A single address string plus one lat/lng pair (§12) |
| "Cómo llegar" | Outbound link to Google Maps directions, built from the address string — note this is Google Maps while the search map is Mapbox |
| Work hours | Weekly schedule with a local-time stamp; the sampled values were overwhelmingly `N/A`, one day reading "Sólo con cita previa" |
| Gallery | Present as a section; frequently empty |
| Video | Section present; separate "Explorar los Videos" tab on the music type |
| Audio | "Audios" tab exists on the music type; the sampled instance rendered empty |
| Category | One or more `job_listing_category` terms; **frequently absent entirely** in the sample |
| Tags | `case27_job_listing_tags`; used as search filters on most types |
| Starting price | Rendered as "A partir de $…" on some listings; venues render "Packages Starting at" |
| Phone | Rendered as a live `tel:` link, publicly, with no authentication (§6.3) |
| Rating / review count | Rendered; zero across the entire sample (§13.1) |

### 4.2 Fields conspicuously absent from the public surface

No email address is exposed (`mailto:` links: zero across the sample). No website field was rendered on the sampled listings. **No public service-area or travel-radius field was observed in the sampled listing pages or in the inspected public search/filter configuration** — a listing declares one point, with nothing publicly stating where it will travel or serve.

67.4% of the platform's observed self-reported type counts fall under the Música listing type, making service-area semantics especially important if those listings represent mobile services; that interpretation requires verification. On the observed public model this is a material structural gap, and it is the gap `ServiceAreaDeclaration` (glossary, P02) was introduced to close.

### 4.3 Tab structure varies by type

- Music (`entretenimiento-musical`): `Perfil` · `Explorar los Videos` · `Audios` · **`Solicitud de Reserva`** · `Comentarios` · `Eventos`.
- Venue: `Perfil` · `Comentarios` · **`Reservar`**.

The `Eventos` tab is of theme type `related_listings` and rendered empty on the sampled listing.

### 4.4 Ownership concentration signal — sample-bound

Every listing page carries a `data-user-id` attribute on its direct-message control, identifying the WordPress user the theme treats as the listing's owner.

Across **53 sampled listing pages, stratified across the full sitemap range, exactly two distinct owner user ids appeared: `1` (47 listings) and `94` (6 listings).**

User id `1` is an id conventionally associated with an installation's initial WordPress account; **its actual role and ownership are UNKNOWN** and were not established here.

**What is observed is the sample, and only the sample.** 53 pages out of ~43,361 public listing URLs is a stratified probe, not a census. Two owner ids appeared in it; a different id could appear on the very next page not sampled. Nothing here establishes the ownership distribution of the corpus, and this document does not assert one.

**What the sample supports is a material ownership-concentration signal.** In a probe deliberately spread across the whole sitemap range — which would be expected to surface variety if variety existed — owner identity did not vary meaningfully, and the id carrying 47 of 53 is `1` — conventionally an installation's initial account, though its actual role here is unverified. That is a strong enough signal to make ownership one of the first things P05 examines, and too weak to support any conclusion about the corpus.

**Why the signal matters enough to prioritise.** Public listing URLs count published records. They do not evidence who created a record, whether an account controls it, whether the described business ever registered, or whether anyone consented to its publication — and the phone numbers on those pages are public (§6.3). The owner-reported "~43,000 provider registrations" and the observed "43,361 public listing URLs" are different quantities regardless of how ownership resolves; the ownership signal simply makes the gap between them worth measuring early rather than late.

**P05 must verify, from internal data:**

1. The **full `post_author` distribution** across all listing records and all statuses — not a sample.
2. **Real ownership**: which records are controlled by an account belonging to the described business, which are platform-created, and which are neither.
3. **Claim and control history**: whether any record was ever claimed, what a claim granted, and what proportion of records has a claimant.
4. The **provenance and lawful basis** of any bulk-created records (§18 Q-A3).

**Conditional consequences — none of these apply unless P05 confirms them.** *If* P05 confirms that a majority of records are platform-created and unclaimed, then: the cold-start advantage implied by the "organic traction" hypothesis in `product-context.md` is materially weakened and must be re-derived from account and activity data rather than listing counts; and a large portion of the corpus would fall to `LegacyProviderRecord` handling under `ADR-009` — records Superola asserts *may* describe a business, structurally outside discovery — rather than to profile migration. *If* P05 finds ownership is genuinely distributed, this section is simply a sampling artefact and those consequences do not arise. Both outcomes remain open on the public evidence.

---

## 5. Category-specific fields

`OBSERVED`. Each listing type declares its own filter set. This is the theme's per-type configuration, and it is the closest thing the legacy platform has to category-specific modelling.

| Type | Type-specific filters (beyond keyword / location / lat / lng / proximity / sort) |
|---|---|
| Música | `category`, `min-price-per-event`, `tags` |
| Compositores | `category`, `min-price-per-event`, `tags` |
| **Venue** | **`minguests`**, **`startprice`** — and notably **no `category` and no `tags`** |
| Pasteles | `category`, `min-price-per-event`, `tags` |
| Comida / catering | `min-price-per-event`, `tags` (no `category`) |
| **Cars** | `category`, **`car-price`**, **`checkboxes`** |
| Fotógrafos | `category`, `min-price-per-event`, `tags`, **`region`** |
| Floristas | **`start-price`**, `tags` (no `category`) |
| Altavoces | `category`, `min-price-per-event`, `tags`, `region` |
| Actos de variedad | `category`, `min-price-per-event`, `tags` |
| Servicios de eventos | `category`, `min-price-per-event`, **`dropdown`**, `tags` |
| Otros servicios | `category`, `min-price-per-event`, `tags`, `region` |

What this shows:

1. **Per-type variation is publicly exhibited.** Venues filter by guest count and package price; transport filters by vehicle price and feature checkboxes; performers filter by per-event starting price. This is consistent with the P01 category-variability hypothesis and with `ADR-007` (shared core plus governed category archetypes). **The public configuration clearly exhibits per-type variation; the design process or governance model that produced it is not observable.**
2. **The same concept appears under several field names**: `min-price-per-event`, `startprice`, `start-price`, plus `car-price`. The public configuration does not expose enough normalized semantics — currency, basis, inclusions, or common field meaning — to treat these price fields as safely comparable without internal reconciliation. This is the condition `PriceStatement` (glossary) exists to prevent.
3. **Two filters are named after their widget, not their meaning**: `checkboxes` (cars) and `dropdown` (servicios de eventos). A filter whose name records its HTML control rather than its semantics cannot be governed, translated, or migrated without opening it.
4. **`region` is applied to only 4 of 12 types.** The additional `region` filter is inconsistently exposed across listing types; the generic location/proximity controls remain platform-wide.
5. **The public Venue search configuration exposes neither category nor tags as search filters** — in the type where sub-classification would plausibly matter most (banquet hall vs ranch vs restaurant vs outdoor).

Additional taxonomies are declared in the explore configuration but were not observed in active use, including `venue-features`, `venue-staff-services`, `custom-taxonomy-car-brand`, and — plainly vestigial — `job-vacancy-type`, `job-qualification`, `job-salary`. The last three are the MyListing job-board demo's taxonomies, still declared on a live event-services marketplace (§17.6).

---

## 6. Customer interaction surfaces

All `OBSERVED`.

### 6.1 Available to an unauthenticated visitor

| Action | Mechanism |
|---|---|
| Search and filter | `/explora/` (§9) |
| Browse listing detail | Public |
| Contact form on the listing | Contact Form 7, fields `your-name` · `your-email` · `tel-21` · `your-message` (message marked *optional*), with hidden `_case27_recipients` and `_case27_post_id` and a reCAPTCHA token. **Submitting requires no account.** |
| "Solicitud de Reserva" / "Reservar" | See §8.1 — the same form |
| Call the provider directly | `tel:` link, no account (§6.3) |
| Share | Social share modal |
| Browse and buy merchandise | WooCommerce shop (§14.1) |
| Request digital-consultancy contact | Fluent Forms lead form (§14.2) |
| Contact support | Fluent Forms form with a role dropdown: Cliente / Miembro / Animador / Vendedor de fiestas / Otros |

### 6.2 Gated behind sign-in

Direct message ("Mensaje directo", a chat control carrying the listing's owner user id) · leave a review ("Deja Reseña") · claim a listing · report a listing.

### 6.3 Contact disclosure — public, unconditional, and not the customer's to trigger

Provider phone numbers are rendered as live `tel:` links on listing detail pages **and** on search-result cards, to anyone, with no account, no request, and no recorded disclosure decision. The sampled listings carried a phone far more often than not; no email was exposed anywhere.

Consequences worth stating plainly:

- The public phone channel allows customer–provider interaction to leave Superola. Any interaction that continues by phone is not observable to the platform unless separately captured. **The share of marketplace activity that follows this path is UNKNOWN**, and it bounds what `SRC-006` analytics could show for that portion.
- Contact details are published without any observable consent signal on the page. Whether the described businesses created or consented to these records is not determinable publicly, and the ownership-concentration signal in §4.4 makes that question urgent rather than answered.
- `ADR-010` (contact-disclosure decision seam) and the `Disclosure decision` concept describe the opposite posture. The legacy behaviour is the baseline against which any V1 disclosure policy will be compared by the owner — including the commercial argument that hiding the number reduces provider contact volume. That argument should be met with evidence, not asserted away.

---

## 7. Provider interaction surfaces

All `OBSERVED`, and thin — because nearly everything a provider does is behind authentication.

| Surface | State |
|---|---|
| Register as "Business Owner" | Public form (§2.2) |
| Add a listing | Public type chooser at `/anadir-listado/`; 13 types; each shows "Elija el tipo"; the form itself was not reached |
| Claim an existing listing | `/claim-listing/?listing_id={id}`, login-gated. The claim entry point is present on the listings observed. What proportion of records is unclaimed, and what a claim grants, is unverified (§13.2) |
| Provider dashboard / listing management | Not observable |
| Respond to an enquiry | Not observable. The listing contact form routes to a hidden `_case27_recipients` value; whether that is the listing owner, an admin address, or both is not determinable from outside (§18 Q-A16). Where enquiries actually land therefore interacts with the ownership question in §4.4 and is unresolved on public evidence |
| Promote / upgrade a listing | No such surface found (§11) |
| Provider-facing marketing | `/consultoria-digital/` sells web and graphic design services to providers (§14.2); the home page and `/proposito/` push free registration |

One locale defect sits on the provider path: `/claim-listing/` is a Spanish-language URL that renders an **English** page titled "My Account" and posts to `/my-account/`. A provider following a claim link from a Spanish listing lands in English (§12.4).

---

## 8. Booking / quote / messaging claims

### 8.1 There is no booking, and no quote — `OBSERVED`

The music type shows a tab labelled **"Solicitud de Reserva"** (booking request); the venue type shows **"Reservar"** (book). The theme's own tab type is `bookings`.

**The tab renders the same four-field Contact Form 7 form as the profile tab**: name, email, phone, and an *optional* message. Both instances share one form id and differ only in their unit tag.

Stated precisely, and bounded to what a visitor can see: **the public surface labelled "Solicitud de Reserva" presents a contact-intake form (name, email, phone, message). It does not publicly demonstrate date reservation, a booking lifecycle, a quote lifecycle, or payment.** No date field, event field, guest count, service selection, budget, response mechanism, quote object, or status is rendered to a visitor.

**What is persisted internally, and whether any separate booking or request state exists behind this form, is UNKNOWN — INTERNAL ACCESS REQUIRED** (§18 Q-A6, Q-A16). A contact-intake surface is not evidence that no request record exists; it is evidence that none is publicly exposed here.

Two readings follow, and only the first is a public-surface claim:

1. **The public labelling asserts more than the public surface demonstrates.** A customer reading "Solicitud de Reserva" may reasonably expect a reservation step. Whether that expectation is met by something not publicly visible is exactly the unknown above.
2. It is consistent with the P01/P02 position that `ServiceRequest`, `ProviderResponse`, and `Quote` must be modelled explicitly and never named more strongly than the evidence supports — consistent with, not proof of.

### 8.2 Messaging

A "Mensaje directo" control exists and is sign-in-gated, carrying the target's user id. In-platform messaging therefore exists in some form. Nothing about its behaviour, retention, notification, or usage is observable from outside. On the sampled listings the control resolved to one of the two observed owner ids (§4.4); whether that pattern holds beyond the sample is unverified, so who actually receives a direct message across the corpus is UNKNOWN.

### 8.3 Comments

The comments tab on the sampled listings rendered **"Comments are closed."** — a WordPress-level state, distinct from having no comments.

---

## 9. Search / filter behaviour

All `OBSERVED`.

### 9.1 Mechanics

- Single search surface at `/explora/` (ES) / `/explore/` (EN).
- **Listing type is selected first**, and the filter set changes with it (§5). Type is a mode, not a filter — a customer cannot search across types.
- URL parameters drive state: `?type={slug}&sort={sort}` plus the active type's filters. State is reflected into the URL (`DisableLiveUrlUpdate: false`), so result pages are linkable.
- Keyword input: `search_keywords`, placeholder "¿Qué buscas?".
- Location input: "¿Dónde buscar?" with a proximity slider.
- Results paginate (12 pages observed on a 239-result type) and a "Cargar más" control exists.
- A map view accompanies results; drag-to-search is **disabled** (`DragSearchEnabled: false`).

### 9.2 Sort options exposed publicly

`listing-entry-date` (default) · `latest` · `nearby` · `top-rated` · `a-z`. **No relevance sort is exposed.** The exposed modes order by recency, proximity, rating, or alphabet.

In the queries run here, `top-rated` on the music type returned, among the first five, a listing titled "897123" and one named "Banda Nueva Categoría" — consistent with the sparse rating activity seen in the sample (§13.1). Whether that reflects the corpus, or only these queries, is not established.

Stated within the public boundary: **no public relevance-ranking control or relevance score was observed. The public search surface exposes explicit sort modes instead. Internal ranking or weighting, if any, is UNKNOWN.** In P02 terms, nothing publicly visible corresponds to `placementBasis`.

### 9.3 Keyword behaviour

Not characterised. Determining whether keyword search covers title only, or also description, category, and tags — and how it behaves across Spanish and English — requires either query experimentation beyond this audit's bounds or internal access. Flagged in §20.6.

---

## 10. Related-provider behaviour

`OBSERVED`. Listing pages render a "You May Also Be Interested In" block.

On a **Chicago** DJ listing, the related listings were DJs in **New Jersey** and **New York City**.

In the observed Chicago DJ example, the related block did not respect geographic proximity. **The mechanism that produced the recommendations is UNKNOWN** — a single observation does not reveal the selection logic, its inputs, or whether geography is weighted at all.

What can be said about the outcome: for a service physically delivered at an event, a related-provider block spanning the country is geographically poor for that use case, and it occupies prominent space on the page.

The related block also republishes phone numbers and starting prices (§6.3), so one page view discloses contact details for several businesses at once.

Cross-type behaviour was not characterised: on a venue listing the block surfaced rental companies, but §17.3 shows those records are themselves typed as venues, so that is more likely a data defect than cross-type recommendation.

---

## 11. Sponsored / Featured behaviour

**No current public evidence of paid marketplace listing monetization was found in this reconnaissance.**

Observed:

- The theme carries a promotion mechanism: every listing card renders `level-{name}` and `priority-{n}` classes. **Every card observed across every type and both sort orders was `level-normal` and `priority-0`.**
- No promotion, upgrade, or feature purchase surface was found anywhere on the public site.
- The WooCommerce catalogue contains merchandise only — no listing package, no subscription, no promotion product was publicly listed (§14.1).
- Third-party ad slots are configured but off: `DisplayAd: false`, with empty publisher and slot ids.
- The word "Destacado" appears in the header search dropdown as a **suggestion-group heading**, not a paid label.

### 11.1 Three distinct questions, three distinct answers

| Question | Status |
|---|---|
| **Current public marketplace monetization evidence** | None found. No publicly purchasable listing package, promotion, upgrade, or subscription, and no promoted card state observed |
| **Historical monetization** | **UNKNOWN.** A past subscription, promotion, or paid-placement programme could have run and been withdrawn, or could exist behind authentication, and would leave no trace on the current public surface. Nothing in this reconnaissance speaks to it — INTERNAL ACCESS REQUIRED (§18 Q-A14) |
| **Digital Consulting** | An **observable, separate commercial line** selling design services to providers (§14.2). Real and live; not marketplace listing monetization |
| **WooCommerce merchandise** | **Observable but not marketplace listing monetization** — branded goods (§14.1), unrelated to listing visibility or provider entitlement |

### 11.2 What this does and does not license

It licenses one narrow reading: at this access date, a visitor cannot buy listing visibility, and no card advertises paid status. It does **not** establish that the owner's subscription and sponsored-placement hypotheses were never attempted, that no legacy revenue or pricing data exists, or that the corpus carries no paid-placement history. `SRC-007` remains open and cannot be answered from the public surface in either direction. Whether `ADR-008`'s reserved policy seam and the `placementBasis` design meet a clean legacy baseline depends on the historical answer, which is unknown.

---

## 12. Public location behaviour

All `OBSERVED`. This is where the owner's "location behavior is unreliable" claim (`CONFIRMED`, `OWNER_INTERVIEW`) meets first-party evidence. **The claim holds, and the specific failure modes are now identified.**

### 12.1 Model

One address string plus one `lat`/`lng` pair per listing, carried in a `data-locations` JSON attribute on every card and detail page. One point. No service area, no radius, no multi-location, no precision indicator, no provenance.

### 12.2 Failure modes observed

| Failure | Evidence from the 33-listing stratified sample |
|---|---|
| **Missing entirely** | 11 of 33 listings (33%) had **no address and no coordinates** |
| **City-centroid only** | Coordinates repeat exactly across unrelated listings — four different Stockton venues all at `37.95770, -121.29080`; multiple Chicago listings all at `41.87560, -87.62440` |
| **Wrong** | A listing named *"Mariachi Los Arrieros de Fresnillo Zacatecas"* is geocoded to **Texcoco de Mora, State of Mexico** — roughly 600 km from the place in its own name. Six of 33 sampled listings resolved to that same Texcoco point, which reads as a default or fallback rather than a location |
| **Inconsistent formatting** | Country stored in two languages within the same field across listings: `"…, United States"` and `"…, Estados Unidos"` |
| **Precision mixed without marking** | The same field holds `"Nuevo León, Mexico"` (state), `"Chicago, Illinois, United States"` (city), and `"4218 S Andes Way, Aurora, Colorado 80013, United States"` (street) with nothing distinguishing them |

This supports `GeoPoint` carrying **mandatory precision and provenance**, and three-valued `LocationEligibility` where `undetermined` is surfaced rather than coerced. A city-centroid point and a street address are not the same fact, and **11 of the 33 sampled listings had no public address or coordinates**. Distance filtering over data of this shape can produce confident but wrong answers — the failure mode the owner described.

### 12.3 Proximity search

Default proximity is **500 km**, and the slider is hidden until a location is entered. A 500 km default permits a very broad candidate area and may produce results that users would not consider nearby. Combined with §12.2, `sort=nearby` has two visible weaknesses: it has no coordinates to rank the listings that carry none, and it treats centroid-collapsed listings as if they sat at identical addresses.

The map's default centre is **`51.492, -0.13` — London**, the MyListing demo default, never changed (§17.6).

### 12.4 Geography and language

- Site default language is **Spanish (`es-MX`)**; English is served under `/en/` with `hreflang` alternates (`es-MX`, `en-US`).
- Spanish and English route structures are parallel (`/explora/` ↔ `/explore/`, `/mi-cuenta/` ↔ `/my-account/`).
- Language leaks across the boundary: `/claim-listing/` renders English (§7); a venue detail page renders the English label "Packages Starting at" inside an otherwise Spanish page.
- Observed listing locations span the United States (Chicago, Stockton, Long Beach, Albuquerque, Aurora, Denver, Austin, New York, New Jersey) and Mexico (Monterrey, Cuernavaca, Uruapan, Zacatecas, Navojoa, Cancún, Mérida, Chihuahua), plus at least one Canadian record (Toronto, Mississauga — §17.2). The owner-reported US/Mexico reach is corroborated; **country-level distribution and activity remain unquantified** (§20.5).

---

## 13. Reviews / claims / reports

### 13.1 No review activity observed in the sampled listings — `OBSERVED`

Across the entire sample, review counts were zero and profiles carried the theme's `no-rating` state. Requesting `sort=top-rated` returned essentially unrated listings in both the music and venue types.

Public review activity appears sparse in the sample, but corpus-wide review records and any private or historical reputation data remain **UNKNOWN — INTERNAL ACCESS REQUIRED**.

Consequences, stated within that boundary: the usefulness of the `top-rated` sort was not demonstrated by the sampled listings or by the queries run here (§9.2), and these observations supply no evidence about what customers would review or whether they would. `A-004` and the P01 position that reviews imply an unobservable booking event are neither challenged nor supported by what is publicly visible.

Whether reviews are login-gated only, or also gated on some interaction, was not determinable — the review form is sign-in-gated and was not opened.

### 13.2 Claims — `OBSERVED`

Every listing observed exposes "Lista de reclamaciones" → `/claim-listing/?listing_id={id}`, login-gated. The claim path is a prominent feature of the live platform, offered on listings generally rather than on a marked subset — which is at least consistent with an expectation that records may not be held by the businesses they describe, though the public surface does not establish that.

What happens after a claim — verification, evidence, operator review, dispute, what the claimant receives — is entirely unobservable, and it is one of the highest-value internal questions (§18), because `ADR-009` makes `OwnershipClaim` bind ownership **without publishing**, and the legacy behaviour is unknown on exactly that point.

### 13.3 Reports — `OBSERVED`

"Reportar" exists on every listing and resolves to `/mi-cuenta/`. Reporting requires an account, and the gate is an unexplained redirect to a login page. Report intake, triage, and moderation state are unobservable. Given the spam content found in the taxonomy (§17.2), the effectiveness of this path is a fair question.

---

## 14. Shop / digital consultancy

### 14.1 Shop — `OBSERVED`

`/tienda/` (ES) / shop (EN) is a WooCommerce store selling **Superola-branded merchandise only**: t-shirt, hoodie/sudadera, jacket, cap collection, VIP cap, beanie, mug collection, phone cover, water bottle. Eight products, duplicated per language by Polylang (16 records in the product sitemap).

- Prices **USD $25.00–$99.97**, confirmed via the store API (`currency_code: "USD"`, minor unit 2). Displayed with a bare `$` and no currency code — on a site serving the United States and Mexico, that is ambiguous to a Mexican visitor.
- Variable products carry options; PayPal is a registered namespace.
- Most product records were last modified in **2024**; several sit in `Uncategorized`.
- **No marketplace product exists** — no listing package, no featured upgrade, no subscription (§11).

So the only publicly purchasable items found in this reconnaissance are merchandise, and the site's own copy presents marketplace participation as free ("Rápido y Gratis", "Es GRATIS!"). That is a statement about the observable public catalogue and the site's own marketing copy — **not** a finding that no marketplace monetization exists or ever existed (§11.1).

### 14.2 Digital consultancy — `OBSERVED`

`/consultoria-digital/` markets **web design and graphic design services to providers** (musicians, artists, composers, business owners), promising a free quote and personalised advice. The page is a lead-generation surface built on Fluent Forms (name, email, a text field, three checkboxes, message) with the CTA "Quiero Una Cotización".

This is a **second, services-based revenue line aimed at the platform's own supply side**, and it is publicly live while no public marketplace-listing monetization was observed in this reconnaissance (§11.1). Nothing in the repository's product, monetization, or roadmap documents accounts for it. It raises questions this audit cannot answer: is it a material revenue source, is it staffed, does it create a conflict between platform neutrality and selling services to listed providers, and does it survive into V1? See §19.

---

## 15. Candidate legacy entities — INFERRED ONLY

> **Every item in this section is `ASSUMPTION`; provenance `TECHNICAL_DISCOVERY`.** These are inferred from public rendering plus published knowledge of the WordPress/MyListing data model. **None of it was observed in a database, and no migration decision may rest on it.** It exists to make the P05 access request specific instead of open-ended.

| Candidate entity | Basis for the inference | What must be verified |
|---|---|---|
| `wp_users` / `wp_usermeta` | Registration form; owner user ids on listings | Real account count; how many ever created a listing; role distribution; last-login data (WordPress does not record last login by default — this may simply not exist) |
| `job_listing` posts (`wp_posts`) | Post type in body classes and sitemaps | Total by status — the 43,361 figure counts only **published** URLs; drafts, pending, expired, and trashed are invisible from outside |
| `wp_postmeta` for listing fields | MyListing stores custom fields as post meta | Actual field set per type; fill rates; value formats; unit conventions |
| `job_listing_category` terms | Category links and sitemap | 118 public terms observed; term counts, hierarchy, orphans, duplicates |
| `case27_job_listing_tags` terms | Tag links and sitemap | 90 public terms observed; who created them and when |
| `region`, `venue-features`, `venue-staff-services`, `custom-taxonomy-car-brand`, `services-available-for`, `servicios-para-eventos`, `job-vacancy-type`, `job-qualification`, `job-salary` | Declared in explore configuration and/or sitemaps | Which are in use, which are vestigial, which are duplicated across languages |
| Message / conversation storage | Sign-in-gated "Mensaje directo" | Whether messages exist at all, volume, retention, participants, and whether any thread ever received a reply |
| Review storage | Review action and rating markup | Almost certainly near-empty (§13.1); confirm and quantify |
| Claim records | Claim path | Whether claims were ever submitted, approved, or disputed — and what approval did |
| Enquiry / contact-form submissions | CF7 and Fluent Forms | Whether submissions are stored at all (CF7 does not store by default without an add-on) or only emailed — this determines whether *any* legacy demand-side evidence exists |
| Polylang translation links | `pll/v1`, language alternates | Whether listings are duplicated per language, and if so whether the duplicates are counted in the 43,361 |
| WooCommerce customers and orders | Store API | Merchandise order history; whether marketplace users and shop customers share accounts |
| Media attachments | `listing-uploads` paths | Volume, storage size, formats, orphans, and rights provenance |

**Two inferences deserve explicit flagging as risks to the corpus count**, because both would change the headline number in opposite directions: Polylang language duplicates could inflate 43,361, while unpublished records are excluded from it entirely.

---

## 16. Candidate legacy fields — INFERRED ONLY

> **`ASSUMPTION`; provenance `TECHNICAL_DISCOVERY`.** Field *names* below that appear in the explore configuration are observed as filter keys; their **storage, types, units, and semantics are inferred**. Rendered labels are observed; the fields behind them are inferred.

| Inferred field | Evidence type | Open question |
|---|---|---|
| `min-price-per-event` / `startprice` / `start-price` / `car-price` | Filter keys (observed) | Currency? Unit? Per hour, set, package, or event? Are `$100` and `$2500` the same basis? |
| `minguests` | Filter key (observed) | Minimum or capacity? Is it ever populated — the sampled venue rendered "Packages Starting at 0" and no guest figure |
| `checkboxes`, `dropdown` | Filter keys (observed) | What do they actually contain? Named after widgets (§5) |
| Phone | Rendered `tel:` link (observed) | Stored format, validation, international handling, duplicates across listings |
| Address string + lat/lng | `data-locations` (observed) | Whether they are stored together, geocoding provenance, precision, and whether the fallback point (§12.2) is data or display |
| Work-hours schedule | Rendered (observed) | Structure, timezone handling, meaning of `N/A` vs unset |
| Gallery / video / audio | Rendered sections (observed) | Storage, external embeds vs uploads, rights provenance |
| Tagline, description | Rendered (observed) | Length limits, language, HTML content |
| `level` / `priority` | Card classes (observed as `normal` / `0`) | Whether any non-default value exists anywhere in the corpus |
| Listing status and expiry | Not observed | MyListing supports listing expiry; whether it is enabled changes what "43,361 published" means over time |
| Terms acceptance | Rendered inconsistently (§2.2) | Whether acceptance is stored per user, with a timestamp and a policy version — decisive for re-consent |

---

## 17. Data-quality observations

All `OBSERVED` unless marked. This section is deliberately concrete: `legacy-data-strategy.md` requires auditable criteria before classification, and these are the defect classes those criteria must catch.

### 17.1 Junk values in production

- Starting price rendered as **"A partir de $000000"** on multiple sampled listings.
- Venue detail rendering **"Packages Starting at 0"**.
- A listing whose **title is "897123"**, returned in the first five results of `top-rated`.
- A listing titled **"Banda Nueva Categoría"** — a placeholder that reads as a taxonomy artefact.

### 17.2 Off-domain and spam content

The platform is an event-services marketplace. The published corpus and taxonomy include:

- Listings: **`smile-dentist-toronto`**, **`renta-de-autos-en-cancun-aeropuerto`**.
- Tags: **`dentis`**, **`plumber`**, **`health`**, **`artificial-grass`**, **`artificial-grass-installation`**, **`florist-mississauga`**, **`order-flowers-online-mississauga`**.
- The `servicios-para-eventos` taxonomy itself contains a term **`plumber`**.
- SEO keyword-stuffing tags: `payasos-cerca-de-mi`, `payasos-economicos`, `payasos-en-atlanta`, `payasos-en-lawrenceville`, `payasos-en-gwinnett`, `payasos-en-norcross`, `payasos-en-marietta`, `contratar-payaso`.

The public taxonomy contains off-domain, duplicate, misspelled, and SEO-shaped terms. This corroborates a taxonomy-quality problem, but public evidence does not establish who created those terms or what permissions allowed them. The owner's provider-authorship explanation remains `OWNER_INTERVIEW` and requires P05 verification.

What is observable is that these terms sit in the *governed* taxonomy rather than in free text — the quality defect reaches the classification layer itself. That outcome is consistent with `ADR-007`'s rule that provider input can only ever be a `CategoryProposal`; it does not demonstrate that provider-authored taxonomy creation was the mechanism that produced it.

### 17.3 Type and category misassignment

- `sort=top-rated` on **venue** returned "Premier Denver Event Rentals", "Chair Rental Co", "Servifiestas Landeros", "ARMA FIESTAS" — **rental companies typed as party venues**.
- The **`cars`** type (labelled "Transportación — Limousines, Exotics, Party Buses") is dominated by **airport car rental and taxi services**: "Renta de Autos en Cancún Aeropuerto", "Veico Car Rental Guadalajara Airport", "Punta Cana Airport Transportation", "Taxi Aeropuerto Cancún", "Veico Car Rental Mérida". Tourism transport, not event transport.
- Many sampled listings carried **no `job_listing_category` term at all**.

A customer filtering for a party venue receives chair-rental companies; a customer filtering for a party bus receives Cancún airport taxis. The type is not a reliable predicate.

### 17.4 Taxonomy duplication and drift

- **118** public categories and **90** public tags for 12 listing types.
- Near-duplicate terms: **`sirreno`** and **`sierreno`** (a misspelling and its correction, both live); `fotografos` / `fotografo` / `photographer` / `fotografo-de-bodas` / `wedding-photographer`.
- `comida-catering-abastecimiento` exists **both as a listing type and as a category**.
- Two sibling taxonomies with English and Spanish names that are **not translations of each other**: `services-available-for` holds 7 *occasion* terms (birthday-parties-adult, celebrations, christmas-parties, conventions, events, bachelor-parties, bachelorette-parties) while `servicios-para-eventos` holds 22 *service* terms (bartender, sommelier, event-planner, makeup-artist, payasos, mago, …). Sibling names, different semantics, split across languages.

### 17.5 Staleness

From sitemap `lastmod` data: **43 of the 44 listing sitemaps were last modified in 2022** (March–November). Within the one actively-changing sitemap, per-URL modification years are 2022: 586, 2023: 352, 2024: 5, 2025: 45, 2026: 12. A fully sampled mid-range sitemap showed **1,000 of 1,000 URLs last modified in 2022**.

Reading (`ASSUMPTION`, provenance `TECHNICAL_DISCOVERY`): the public sitemap modification signals are heavily concentrated in 2022, suggesting that a material portion of the published corpus may not have been republished or modified recently. **Sitemap `lastmod` does not establish creation date, business activity, or data freshness.** Should §20.1 also find records without a controlling account, staleness and ownership would compound for that subset — but that combination is conditional, not established.

Corroborating detail: the site footer reads **"2025 © Todos los derechos reservados"** while the audit date is 2026-08-13.

### 17.6 Unconfigured theme defaults still live

- Map default centre **London (51.492, -0.13)** on a US/Mexico marketplace.
- MyListing demo categories still published: `art-and-history`, `cinemas`, `outdoor-activities`, and the vehicle-class terms `compact`, `sedan`, `suv`.
- Job-board taxonomies `job-vacancy-type`, `job-qualification`, `job-salary` still declared.
- Broken links in the served markup, including an `href` of `http://./` and a truncated `https://superola`.
- Mixed-language UI labels within a single page (§12.4).

### 17.7 Machine access posture

- `robots.txt` disallows only `/wp-admin/` and WooCommerce internals; the full sitemap set is published, so all 43,361 public listing URLs are openly crawlable.
- The WordPress REST API is enabled, but **`job_listing` is not exposed** (`/wp-json/wp/v2/job_listing` returns 404) — listings are not machine-readable through the API.
- **WooCommerce Store API is publicly readable** (`/wp-json/wc/store/v1/products`, `/cart`), returning full product and pricing data without credentials.
- The **Mapbox access token is embedded client-side** and is therefore public. Whether it carries URL restrictions was not tested. Unrestricted public map tokens are a cost-exposure risk.

Net posture: provider contact data is fully open to crawlers via HTML while the structured API is closed — the inverse of `ADR-012`'s allowlist projection with deny-by-default machine access.

---

## 18. Questions requiring internal access

Cannot be answered from the public surface. Each names what access would resolve it.

| # | Question | Access required |
|---|---|---|
| Q-A1 | How many **accounts** exist, how many ever created a listing, and what is the `Customer` / `Business Owner` split? | `wp_users`, `wp_usermeta` |
| Q-A2 | **Whose account is user id `1`, and what is the full ownership distribution** across all listing records and statuses — what share sits with id `1`, with any other internal account, and with accounts belonging to the described businesses? Does the §4.4 sample signal hold, or was it a sampling artefact? | `wp_users`; `wp_posts.post_author` distribution; account-to-business mapping |
| Q-A3 | Where did the bulk-created records come from — import, scrape, purchase, manual entry, or partnership — and **on what legal basis**? | Owner + operator knowledge; import logs |
| Q-A4 | How many listings exist by **status** (publish, draft, pending, expired, trash)? | `wp_posts.post_status` |
| Q-A5 | Do Polylang **language duplicates** inflate the 43,361 figure? | `wp_term_relationships` / Polylang tables |
| Q-A6 | Do **contact-form submissions persist anywhere**, or are they email-only? | CF7 / Fluent Forms configuration and storage |
| Q-A7 | How many **direct messages** exist, between whom, and how many threads ever received a reply? | Messaging tables |
| Q-A8 | How many **claims** were submitted, approved, or rejected — and what did approval grant? | Claim records; operator knowledge |
| Q-A9 | How many **reports** were submitted and how were they resolved? | Report records |
| Q-A10 | Is **listing expiry** enabled, and has it ever run? | MyListing settings |
| Q-A11 | What are the actual **custom field definitions and fill rates** per listing type? | `wp_postmeta` profiling |
| Q-A12 | Is **terms acceptance stored** per user with timestamp and policy version — and what happened for Spanish registrations (§2.2)? | User meta; WooCommerce settings |
| Q-A13 | What **traffic, geography, and acquisition** does GA4 `G-9ZW2W7RKMZ` actually show, and over what date ranges? | `SRC-006`; GA4 access |
| Q-A14 | Has any listing ever carried a **non-default `level` / `priority`**? | `wp_postmeta` |
| Q-A15 | What is the **hosting, cost, and operational footprint** of the current platform? | Owner / hosting access |
| Q-A16 | Where do **listing enquiries actually route** (`_case27_recipients`)? | CF7 form configuration |
| Q-A17 | What is the **media storage volume** under `listing-uploads`, and what rights provenance exists? | Filesystem / storage metrics |
| Q-A18 | Is the public **Mapbox token restricted**, and what does it cost? | Mapbox account |

---

## 19. Owner interview questions

For David to put to the owner. Each exists because this audit found something the repository does not currently account for.

1. **On the corpus (§4.4).** In a 53-page sample spread across the whole site, only two owner ids appeared, and 47 of the 53 referenced user id `1` — an id conventionally associated with an installation's initial WordPress account, though whose account it is here is unverified. That is a signal, not a measurement. How were the listings created — provider self-registration, bulk import, manual entry by staff, or a mix? Where did the records come from, and what permission covers publishing those businesses' names and phone numbers?
2. **On what "43,000 registrations" means.** Does the owner understand this figure as provider *accounts*, or as listing records? The public site evidences 43,361 public listing URLs, which is a different quantity, and the two lead to very different assumptions about cold-start advantage.
3. **On published phone numbers (§6.3).** Provider phone numbers are public and callable without an account, so interaction can leave the platform and continue by phone unobserved. How much activity the owner believes takes that path is exactly what we cannot see. Was publishing the number deliberate, is it negotiable in V1, and what has provider feedback been?
4. **On the "Solicitud de Reserva" label (§8.1).** The booking-request tab is a plain contact form. Was that a deliberate placeholder or an unnoticed gap — and has any customer complained that a "reservation" was not honoured?
5. **On claims (§13.2).** Has anyone ever claimed a listing, and what did the owner give them when they did?
6. **On digital consultancy (§14.2).** Is `/consultoria-digital/` a material revenue line, who staffs it, and does it survive into V1? Does selling design services to listed providers create a neutrality problem the owner is comfortable with?
7. **On the shop (§14.1).** Is merchandise a real revenue line or a brand exercise? Does it need to survive the rebuild?
8. **On the taxonomy (§17.2, §17.4).** Providers appear to have created categories and tags freely, including plumbers and dentists. Does the owner accept that only operators create categories in V1, and that provider input becomes a proposal?
9. **On off-domain listings (§17.2, §17.3).** Airport taxis, dentists, and plumbers are published. Is any of that intentional adjacency, or is all of it unwanted?
10. **On the `Evento` listing type (§3.1).** A 13th type is creatable but not searchable. Was an events feature planned or abandoned?
11. **On staleness (§17.5).** Most content has been untouched since 2022. Which categories or geographies does the owner believe still reflect real, contactable businesses?
12. **On language (§12.4).** Spanish is the default and English is secondary, with leaks between them. Is Spanish-first the intended posture for V1?
13. **On the two-value account role (§2.2).** Registration forces a choice between Customer and Business Owner. Is the owner aligned with `ADR-004`'s position that one account should be able to do both?

---

## 20. What P05 must verify after access

`legacy-data-strategy.md` requires provenance and definitions **before counting**. This audit sharpens what P05 must establish. It also raises a question P05 should settle early: alongside *how many records are clean*, **whose records they are**.

### 20.1 Verify the ownership distribution early

Resolve Q-A2 and Q-A3 near the start, because the answer changes what every later step is classifying. Establish the full `post_author` distribution across all statuses, real ownership and control, and claim history (§4.4).

Two branches follow, and the public evidence selects neither:

- *If* a majority proves platform-created and unclaimed, the leading question becomes "**may these records be published or contacted at all, and on what basis**" — a legal and consent question that would precede the technical ones, with a large portion falling to `LegacyProviderRecord` handling under `ADR-009`.
- *If* ownership proves genuinely distributed, the ownership-concentration signal was a sampling artefact and classification proceeds on ordinary data-quality grounds.

Either way, the branch should be resolved before classification criteria are finalised.

### 20.2 Re-derive the corpus count under stated definitions

Establish, with definitions recorded: total by post status; language duplicates; distinct businesses after deduplication; records with a usable phone; records with a usable location; records with any category. **43,361 is a count of public listing URLs and must never be restated as users, providers, registrations, or accounts.** The defensible figure for any of those quantities is unknown and may be materially smaller.

### 20.3 Build classification criteria against the observed defect classes

`legacy-data-strategy.md` proposes `CLEAN` / `MIGRATABLE` / `NEEDS_REVIEW` / `DUPLICATE` / `STALE` / `INVALID`. The criteria must be auditable against the defect classes this audit found: junk values (§17.1), off-domain and spam content (§17.2), type and category misassignment (§17.3), missing location (33% of the sample), wrong or centroid-only location (§12.2), 2022 staleness (§17.5), and — pending §20.1 — ownership and consent status (§4.4). A record can be perfectly formatted and still be unmigratable if nobody consented to it.

### 20.4 Carry the role-typing and consent questions into the migration design

The public registration form requires a mutually exclusive `Customer` / `Business Owner` selection, a posture `ADR-004` moves away from (§2.2). **Whether every persisted account carries this role, how it is represented, whether it is mutable, and how historical accounts are classified requires P05 verification** — none of that is established publicly. Terms acceptance may also be missing for Spanish registrations (§2.2). Once §20.1 resolves the ownership distribution, P05 must state what happens to each population it finds — accounts with listings, accounts without, and listings without a controlling account — and how `SuppressionRecord` is seeded **before** any import pass, not after.

### 20.5 Quantify geography and activity from data, not from the corpus

Country and market distribution must come from `SRC-006` analytics and from account/activity data. Listing addresses are unreliable (§12) and record counts are not demand. Until then, "the United States and Mexico are priority markets" stays an `ASSUMPTION`.

### 20.6 Characterise search and keyword behaviour properly

Determine what the keyword index actually covers, how it behaves across Spanish and English, and what `nearby` does with records lacking coordinates — a condition seen in 11 of the 33 sampled listings, at an unknown corpus-wide rate. §9.3 is an acknowledged gap in this audit.

### 20.7 Record what the public surface could not evidence

This reconnaissance found **no public evidence** for: paid marketplace listing monetization, current or historical (§11.1); subscription willingness to pay (§11, §14.1); review behaviour (§13.1); a structured request-and-quote flow (§8.1); or in-platform response rates (§6.3, §8.2). **Absence of public evidence is not evidence of absence** — each of these could exist behind authentication or in internal records. P05 must resolve them against internal data and record the result, rather than letting either "it never existed" or "it must have existed" be filled in by inference. The corresponding P01/P02 positions are, on this evidence, neither refuted nor supported.

### 20.8 Verify these findings against internal data before quoting them

No claim in this document is a corpus-wide fact. Sampled claims rest on 53 listing pages; the 43,361 figure is a reproducible count of public listing URLs and nothing else; counts by type are platform self-reported. P05 has the access to convert these into audited facts. Until it does, sampled findings stay sample-bound, §15 and §16 stay `ASSUMPTION`, and the conditional branches in §4.4 and §20.1 stay conditional.

---

## Provenance and integrity notes

- All observations were made on 2026-08-13 against `https://superola.com` from an unauthenticated browser session.
- No account was created; no credential was entered; no form was submitted; no purchase was made; no personal data was copied into this repository.
- Public endpoints were read at low volume and were limited to sitemaps, published pages, and REST endpoints the site serves to any visitor without credentials. No bulk extraction was performed, in line with `AGENTS.md`.
- Provider phone numbers are published in the clear on the live platform. This document records that fact and its mechanism, and deliberately reproduces no individual's contact details.
- Where this audit interprets rather than reports, the claim is labelled `ASSUMPTION` with provenance `TECHNICAL_DISCOVERY` and the observation it rests on is cited.
