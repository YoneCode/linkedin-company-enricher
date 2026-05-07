# Pricing

Pay-per-result. The Actor charges you only when a row in the default dataset carries a valid LinkedIn URL. Misses, login-walled pages, and failed lookups are routed to a separate `misses` dataset at no charge.

## Rate card

| Charge event | What you receive | Per 1,000 results | Per result |
|---|---|---|---|
| `fast-result` | Fast Mode row: LinkedIn URL, country, match confidence, normalised domain | $2.00 | $0.002 |
| `fast-socials-result` | Fast Mode row + Facebook, Instagram, and X/Twitter URLs | $3.50 | $0.0035 |
| `deep-result` | Full company profile: name, industry, size, HQ, type, specialties, description, follower count, website, email, phone, supplier flag, product categories | $6.00 | $0.006 |
| `deep-socials-result` | Deep result + Facebook, Instagram, and X/Twitter URLs | $8.00 | $0.008 |

## What you do not pay for

- Domains with no LinkedIn page — saved to the `misses` dataset
- LinkedIn pages that hit a login wall in Deep Mode — saved to `misses`
- Failed lookups (network error, timeout, invalid input) — saved to `misses` with an `error` field

You can inspect the `misses` dataset on the run's Storage tab to see exactly which domains were skipped and why.

## Worked examples

### 1,000 domains, Fast Mode, 90% hit rate

- 900 billable results × $0.002 = **$1.80**
- 100 misses billed at $0.00

### 1,000 domains, Fast Mode + socials, 90% hit rate

- 900 billable results × $0.0035 = **$3.15**

### 1,000 domains, Deep Mode + socials, fresh cookie, 95% hit rate

- 950 billable results × $0.008 = **$7.60**

### 10,000 domains, Deep Mode, 92% hit rate

- 9,200 billable results × $0.006 = **$55.20**

## Why pay-per-result and not per-credit

Two reasons:

1. **Predictable.** You know exactly what a 5,000-domain run will cost before pressing Run.
2. **Aligned incentives.** A row only counts if the Actor delivered something useful. There is no charge for empty hits, retries, internal pagination, or scraping overhead.

## Volume

There is no rate-card discount tier — the per-result price is the same at 100 domains and 100,000 domains. Concurrency is capped per run for reliability (default 5 parallel domains, max 10), so very large jobs split into chunks complete most efficiently.

## Apify platform fees

Standard Apify platform usage applies on top of the per-result price (proxy bandwidth, compute, storage). On any non-trivial Deep Mode run those are typically a small fraction of the per-result cost. See [Apify Pricing](https://apify.com/pricing) for platform plan details.
