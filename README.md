# LinkedIn Company Finder & Enricher

> Domains in. Full LinkedIn company profiles out.

A managed Apify Actor for B2B teams that need to turn raw company domains into structured LinkedIn data — at scale, on demand, and without running scraping infrastructure.

- **Live on the Apify Store:** https://apify.com/yonecode/linkedin-company-enricher
- **Documentation source:** https://github.com/YoneCode/linkedin-company-enricher

This repository hosts the public documentation, comparison data, and integration examples for the Actor. The source code is proprietary and runs on the Apify platform; this repo is for evaluation, integration planning, and reference.

---

## What it does

Given a list of company domains (or a CSV with a `domain` / `website` column), the Actor returns a normalised dataset of LinkedIn company profiles:

- LinkedIn URL with country code and a 0–1 match-confidence score
- Full company profile when a LinkedIn cookie is supplied: name, industry, employee count, headquarters, company type, specialties, description, follower count, website
- Best-effort contact information (email, phone) parsed from the company website
- Optional social URLs (Facebook, Instagram, X/Twitter)
- An `is_ecommerce_supplier` flag plus product categories — useful for sourcing teams identifying wholesalers, manufacturers, and distributors

Results stream into the dataset row by row as each domain finishes, so you can begin downstream processing before the run completes. Domains with no LinkedIn presence, login-walled pages, and lookup failures are routed to a separate `misses` dataset and are not billed.

## Two modes

| | Fast Mode | Deep Mode |
|---|---|---|
| LinkedIn cookie required | No | Yes |
| Time per domain | 1–3 s | 3–8 s |
| Fields returned | 4 | 20+ |
| Best for | Bulk URL discovery | Full enrichment for sales / CRM |

Fast Mode resolves only the LinkedIn URL plus country and a confidence score. Deep Mode adds the full profile, contacts, and supplier classification.

## Pricing

Pay-per-result. You are billed only when a row carries a real LinkedIn URL. Misses, login-walled pages, and failed lookups are free.

| Result type | Per 1,000 |
|---|---|
| Fast result | $2.00 |
| Fast result + socials | $3.50 |
| Deep result | $6.00 |
| Deep result + socials | $8.00 |

See [`docs/PRICING.md`](docs/PRICING.md) for the full pricing rationale and worked examples.

## Quick start

### Apify Console

1. Open the Actor's Apify Store page.
2. Paste your domains in the Domains field, one per line.
3. Leave LinkedIn Cookies empty for Fast Mode, or paste your `li_at` cookie value for Deep Mode.
4. Click Run. Export results to JSON, CSV, Excel, or HTML when finished.

### Apify API — Python

```python
from apify_client import ApifyClient

client = ApifyClient("YOUR_APIFY_TOKEN")

run = client.actor("yonecode/linkedin-company-enricher").call(run_input={
    "domains": ["airbnb.com", "stripe.com", "shopify.com"],
    "extract_socials": True,
    # "linkedin_cookies": "AQEDAT...",  # uncomment for Deep Mode
})

for item in client.dataset(run["defaultDatasetId"]).iterate_items():
    print(item["domain"], "->", item["linkedin_url"])
```

### Apify API — Node.js

```js
import { ApifyClient } from 'apify-client';

const client = new ApifyClient({ token: 'YOUR_APIFY_TOKEN' });

const run = await client.actor('yonecode/linkedin-company-enricher').call({
    domains: ['airbnb.com', 'stripe.com', 'shopify.com'],
});

const { items } = await client.dataset(run.defaultDatasetId).listItems();
console.log(items);
```

More integration patterns are in [`docs/INTEGRATIONS.md`](docs/INTEGRATIONS.md).

## Documentation

- [`docs/PRICING.md`](docs/PRICING.md) — pricing model, what is and is not billed, sample run costs
- [`docs/COMPARISON.md`](docs/COMPARISON.md) — feature and price comparison vs other Apify Store actors
- [`docs/USE_CASES.md`](docs/USE_CASES.md) — typical workflows for sales, recruiting, sourcing, and analytics teams
- [`docs/INTEGRATIONS.md`](docs/INTEGRATIONS.md) — n8n, Make, Zapier, Google Sheets, and warehouse pipelines
- [`docs/FAQ.md`](docs/FAQ.md) — cookies, accuracy, rate limits, success rates
- [`examples/`](examples/) — sample inputs and outputs

## Use cases

- **Sales teams** matching CRM domain lists to LinkedIn pages and enriching contact metadata
- **Recruiters** building company shortlists by industry, size, or location from a domain list
- **E-commerce sourcing** identifying wholesalers, manufacturers, and distributors via the supplier flag
- **Analytics teams** bulk-enriching domains for market mapping, lead scoring, or competitor research

## Requirements

- An Apify account ([sign up free](https://console.apify.com/sign-up))
- For Deep Mode only: a valid LinkedIn `li_at` session cookie

## Support

- Issues and feature requests: [open a GitHub issue](https://github.com/YoneCode/linkedin-company-enricher/issues) or use the Apify Store page
- Email: [YoneCodee@proton.me](mailto:YoneCodee@proton.me)

## License

The contents of this repository (documentation, examples, comparison data) are released under the [MIT License](LICENSE) — fork, adapt, and reference freely. The Actor source code itself is proprietary and is not redistributed; it runs as a hosted service on the Apify platform.
