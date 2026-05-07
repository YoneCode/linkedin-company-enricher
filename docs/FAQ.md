# FAQ

## What's the difference between Fast Mode and Deep Mode?

**Fast Mode** is URL discovery only. You provide a domain, the Actor returns the matching LinkedIn company page URL, the country code parsed from the LinkedIn subdomain, a 0–1 match-confidence score, and the normalised input domain. No LinkedIn cookie is needed. Typical run time: 1–3 seconds per domain.

**Deep Mode** requires your LinkedIn `li_at` session cookie. The Actor pulls each company's full profile (name, industry, employee size band, headquarters, type, specialties, description, follower count, website, contacts, supplier classification) typically in 3–8 seconds per domain on a fresh cookie.

You only need Deep Mode if your downstream workflow uses anything beyond the LinkedIn URL itself.

## Why does Deep Mode want my LinkedIn cookie?

Without being logged in, LinkedIn shows a stripped-down public preview of company pages and pushes most automated traffic to a login wall. With your `li_at` cookie, the Actor can pull the full profile from your own logged-in session.

## How do I get my `li_at` cookie?

1. Log into LinkedIn in your browser.
2. Open DevTools (F12) → Application tab → Cookies → `https://www.linkedin.com`.
3. Find the cookie named `li_at` and copy its Value (it starts with `AQE...`).
4. Paste it into the Actor's LinkedIn Cookies field.

The Actor accepts three formats: bare value (`AQED...`), Cookie header (`li_at=...; JSESSIONID=...`), or a JSON cookie array exported by browser extensions like *EditThisCookie*.

## Will using my LinkedIn cookie get my account banned?

LinkedIn does monitor for automation. Risk is low at modest volume (under a few hundred profiles per day), but **never use your primary account**. Create a separate LinkedIn profile dedicated to data work. The Actor uses Apify's residential proxy network and human-like timing to minimise risk, but no setup is 100% safe.

## Why didn't the Actor find a LinkedIn page for my domain?

A few possibilities:

- The company genuinely has no LinkedIn presence.
- The page exists but the company name on LinkedIn differs from the domain in a way that didn't surface in the top search results.
- The domain is a redirect, holding-company, or product-microsite that has no dedicated LinkedIn page (e.g., a sub-brand of a larger group).
- A small percentage of LinkedIn pages refuse anonymous browsing and hit the login wall in Deep Mode.

In every case, the row appears in the `misses` dataset with an explanatory `error` field, and you are not billed for it.

## What does the `match_confidence` score mean?

A 0.0–1.0 number measuring how closely the LinkedIn page name matches your input domain.

- ≥ 0.85 — reliable match
- 0.70–0.85 — almost certainly correct, worth a sanity check on edge cases
- below 0.70 — rare; usually means the company uses an unusual public name on LinkedIn

The score reflects name similarity only, not page legitimacy. A high-confidence match against a fake or impersonator page is theoretically possible but uncommon.

## Why is `extract_socials` off by default?

Each domain with socials enabled performs three additional searches (Facebook, Instagram, X/Twitter), which adds run time and cost. Most workflows don't need socials, so they are opt-in.

## Can I bulk-process tens of thousands of domains?

Yes. Use the `csv_file_url` input with a CSV containing a `domain` or `website` column. Deep Mode concurrency is capped at 10 (default 5) to keep success rates high. For very large jobs, run 5,000-domain chunks rather than a single 50,000-domain run — easier to retry on partial failure, and cheaper to debug if anything goes wrong.

## Does this work for personal LinkedIn profiles (`/in/...`)?

No. This Actor is scoped to **company pages** (`/company/...`).

## What proxy does Deep Mode use? Can I use my own?

Deep Mode uses Apify's Residential proxy by default. You can override `proxy_configuration` to provide your own — but datacenter proxies are not supported, because LinkedIn blocks them on sight.

## Does the Actor store my LinkedIn cookie?

No. The cookie is held in memory only during the run and is never written to logs, datasets, key-value stores, or build artifacts. The cookie field is marked secret in the Apify Input schema.

## How fresh is the data?

The Actor pulls live LinkedIn data on every run. There is no internal cache. If a company updates its LinkedIn page on Monday, a run on Tuesday will reflect the change.

## What happens if my LinkedIn cookie expires mid-run?

The Actor detects the expired-cookie redirect signature immediately and surfaces a clear warning in the run log. Any rows already extracted before the expiry are kept; remaining rows are routed to `misses` with an `error` describing the cookie issue. No charge for the unfinished work.

## Are there support / SLA options for high-volume customers?

For large recurring runs (over 100,000 domains/month) email [YoneCodee@proton.me](mailto:YoneCodee@proton.me) to discuss reserved capacity, custom output schemas, or dedicated support.
