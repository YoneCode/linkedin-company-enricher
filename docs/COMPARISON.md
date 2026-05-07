# How it compares

Public pricing for several other LinkedIn data tools available on the Apify Store and elsewhere. **Verify the current price on each provider's page before purchase** — pricing on Apify Store is reset by each maker and changes occasionally. Numbers below were captured at the time this document was written.

## Apify Store — domain → LinkedIn company

| Tool | Per 1,000 results | Cookie required | Field count |
|---|---|---|---|
| **`yonecode/linkedin-company-enricher` — Fast Mode** | **$2.00** | No | 4 |
| **`yonecode/linkedin-company-enricher` — Fast + socials** | **$3.50** | No | 7 |
| **`yonecode/linkedin-company-enricher` — Deep Mode** | **$6.00** | Yes (`li_at`) | 20+ |
| **`yonecode/linkedin-company-enricher` — Deep + socials** | **$8.00** | Yes (`li_at`) | 23+ |
| [`harvestapi/linkedin-company`](https://apify.com/harvestapi/linkedin-company) | ~$4 | No | ~12 |
| [`anchor/linkedin-company-url-finder`](https://apify.com/anchor/linkedin-company-url-finder) | URL-only tier | No | 1 (URL) |

## Why the price spread

Different tools target different jobs.

- **URL-finder tier** — single-field tools that return only the LinkedIn URL. Cheaper, but you still need a separate enrichment tool to get industry, size, contacts, etc.
- **Mid-tier** — return a partial profile (name, industry, basic metadata). Useful when you do not need contacts or full descriptions.
- **Full enrichment** — return everything visible to a logged-in user, including specialties, description, follower count, contacts, and supplier classification. Best fit for sales operations, CRM enrichment, and sourcing teams.

This Actor occupies both ends of that spectrum: Fast Mode is a URL-finder tier at $0.002 per result; Deep Mode is full enrichment at $0.006–$0.008.

## What "Deep Mode + socials" includes

The `$0.008` per result on Deep + socials is for a single dataset row that contains all of the following, when LinkedIn publishes them:

- LinkedIn URL, country, match confidence
- Company name, industry, employee size band, headquarters, company type
- Specialties (comma-separated), description / about text
- Outbound website link (cleaned, redirect-unwrapped)
- Follower count
- Email and phone (best-effort, parsed from the company website)
- Facebook, Instagram, and X/Twitter URLs
- E-commerce supplier classification with product categories

A single row at $0.008 replaces an enrichment chain that would otherwise touch a URL-finder, a profile scraper, an email finder, and a social-search step. The unified output also keeps your downstream schema constant.

## Caveats

- All prices are public list prices for self-service use on the Apify Store at the time of writing.
- Volume discounts on Apify generally come from your account plan (5–15% across the platform), not the Actor maker.
- Direct-API enterprise tools (LinkedIn Sales Navigator, ZoomInfo, Apollo.io enterprise tier) are not included here — they bundle entirely different feature sets and are typically priced via annual contracts.

## Independent verification

- Apify Store listing prices: visible on each Actor's page under the Pricing tab.
- Number of fields: visible on each Actor's Output schema documentation.
- Cookie requirement: visible on each Actor's Input schema documentation.

If you find a number on this page that no longer matches the live Apify Store, please open an Issue or email the maintainer — accuracy of comparison data matters more than competitive framing.
