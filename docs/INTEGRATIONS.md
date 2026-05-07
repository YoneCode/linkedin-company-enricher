# Integrations

The Actor runs as a standard Apify Actor, so anything that can call the Apify API can use it. Common integration patterns:

## Direct API calls

### Python

```python
from apify_client import ApifyClient

client = ApifyClient("YOUR_APIFY_TOKEN")

run = client.actor("yonecode/linkedin-company-enricher").call(run_input={
    "domains": ["airbnb.com", "stripe.com"],
    "extract_socials": True,
    "linkedin_cookies": "AQEDA...",   # Deep Mode
})

for item in client.dataset(run["defaultDatasetId"]).iterate_items():
    print(item["domain"], item["company_name"], item["industry"])
```

### Node.js

```js
import { ApifyClient } from 'apify-client';

const client = new ApifyClient({ token: 'YOUR_APIFY_TOKEN' });

const run = await client.actor('yonecode/linkedin-company-enricher').call({
    domains: ['airbnb.com', 'stripe.com'],
    extract_socials: true,
});

const { items } = await client.dataset(run.defaultDatasetId).listItems();
console.log(items);
```

### cURL — synchronous (small batches only)

```bash
curl -X POST \
  "https://api.apify.com/v2/acts/yonecode~linkedin-company-enricher/run-sync-get-dataset-items?token=YOUR_APIFY_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"domains":["airbnb.com","stripe.com"]}'
```

## Workflow / no-code platforms

### n8n

Use the official Apify node (`Apify > Run Actor > Wait for finish`). Set the Actor to `yonecode/linkedin-company-enricher`, pass an array of domains as the input, and read the resulting dataset in the next node.

### Make (Integromat)

Use the Apify module:

1. **Run an Actor** — pick `yonecode/linkedin-company-enricher`, supply the input JSON.
2. **Get Dataset Items** — chained on the previous step's `defaultDatasetId`.
3. Branch downstream to your CRM, Google Sheets, or warehouse.

### Zapier

Apify has a Zapier integration with `Run Actor` and `Watch Dataset` triggers. The Actor input is a JSON blob; the dataset items become the trigger payload for the next Zap step.

## Bulk inputs via CSV URL

For very large jobs (10,000+ domains), upload a CSV to a public bucket and pass its URL via `csv_file_url`. The Actor downloads the CSV and reads the first column named `domain` or `website`.

```json
{
  "csv_file_url": "https://storage.example.com/prospects.csv",
  "include_company_details": true,
  "include_contact_info": true
}
```

## Streaming results into a warehouse

The Actor writes each row to the dataset as it completes, so you can begin downstream processing before the run finishes:

- **BigQuery / Snowflake / Redshift.** Poll the dataset's incremental endpoint or set up an Apify Webhook on `RUN.SUCCEEDED` to trigger a one-shot loader.
- **Postgres / MySQL.** A short Python script using `apify-client` and `psycopg` / `mysqlclient` is usually enough; less than 50 lines.

## Webhooks

Configure a webhook on the Actor (Settings → Webhooks) to fire on `ACTOR.RUN.SUCCEEDED`. The webhook payload includes the run ID and dataset ID, which downstream services can use to fetch results.

## Rate limits and concurrency

- Actor concurrency cap: 10 parallel domains per run (default 5). Increase carefully — higher concurrency raises the chance of LinkedIn login-walling Deep Mode requests.
- Apify platform concurrency: depends on your Apify plan. Free tier permits a small number of concurrent runs.

## Schema

A stable, fully-typed JSON schema is published in the Actor's Output schema page on Apify. Every successful run emits the same field set; missing values are `null`, never absent — useful for ingestion into strict warehouse schemas.
