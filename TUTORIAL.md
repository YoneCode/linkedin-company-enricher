# Tutorial — How to enrich domains with LinkedIn data

This tutorial walks you through your first run of the **LinkedIn Company Finder & Enricher** Actor on Apify, from creating an account to exporting your enriched data. No prior experience required.

The Actor takes a list of company domains and returns LinkedIn data for each — name, industry, size, headquarters, contacts, and more.

---

## Before you start

You will need:

- A free Apify account
- A list of company domains (e.g. `airbnb.com`, `stripe.com`)
- About 5 minutes for your first run

If you want the full company profile (industry, size, contacts, etc.) you also need:

- A LinkedIn account
- The `li_at` cookie value from that account (we'll show you how to copy it in Step 5)

> ⚠️ **Use a separate LinkedIn account for data work.** Never use your main personal LinkedIn account.

---

## Step 1 — Create a free Apify account

1. Go to https://console.apify.com/sign-up
2. Sign up with your email or Google account.
3. Confirm your email.

The free tier includes monthly platform credit, which is enough to test this Actor on a few hundred domains.

---

## Step 2 — Open the Actor

1. Go to https://apify.com/yonecode/linkedin-company-enricher
2. Click the green **Try for free** button (or **Run** if you are already signed in).

You will land on the Actor's input form in your Apify Console.

---

## Step 3 — Choose a mode

The Actor has two modes:

| Mode | What you get | Cookie required |
|---|---|---|
| **Fast Mode** | The LinkedIn URL only (plus country and a match-confidence score) | No |
| **Deep Mode** | The full LinkedIn profile — name, industry, size, HQ, contacts, etc. | Yes |

If you only need to find LinkedIn pages for a domain list, use **Fast Mode** — you can skip Step 5.

If you need the full profile, you need **Deep Mode**, which requires your LinkedIn cookie. Continue to Step 5 after Step 4.

---

## Step 4 — Paste your domain list

In the Actor's input form, find the **Domain Names** field. Paste your domains, one per line. Example:

```
airbnb.com
stripe.com
shopify.com
```

You can paste anywhere from 1 to 50,000+ domains. For very large lists, see the "Bulk via CSV" section below.

If you are using Fast Mode, **skip to Step 7**. If you are using Deep Mode, continue.

---

## Step 5 — Get your LinkedIn `li_at` cookie (Deep Mode only)

1. Open a new browser tab and go to https://www.linkedin.com — make sure you are logged in.
2. Press **F12** to open the browser's Developer Tools.
3. Click the **Application** tab (Chrome / Edge) or the **Storage** tab (Firefox).
4. In the left panel, expand **Cookies** and click `https://www.linkedin.com`.
5. In the cookie list, find the row named `li_at`.
6. Click that row and copy the **Value** column. It starts with `AQE...` and is a long string.

Keep that string handy for the next step.

---

## Step 6 — Paste the cookie into the Actor

In the Actor's input form, find the **LinkedIn Cookies** field and paste the cookie value you just copied.

The field accepts three formats — use whichever is easiest:

- The bare value: `AQEDAT...`
- The full Cookie header: `li_at=AQE...; JSESSIONID=...`
- A JSON array exported by a browser extension like **EditThisCookie**

> 🔒 The Actor does not store or log your cookie. It is held in memory only during the run.

---

## Step 7 — Run it

Click the green **Start** button at the bottom of the Actor input form.

You will be taken to the Run page, which shows:

- **Log** — what the Actor is doing in real time
- **Dataset** — your enriched results, streaming in row by row as each domain finishes

A typical 10-domain run takes 30–60 seconds.

---

## Step 8 — Read your results

When the run shows **SUCCEEDED**:

1. Click the **Storage** tab on the run page.
2. Click **Default dataset** to see your enriched rows.
3. Click any row to see all fields for that domain.

Each row contains the fields described in the [Output schema](https://apify.com/yonecode/linkedin-company-enricher) — for example, in Deep Mode you get `company_name`, `industry`, `company_size`, `headquarters`, `description`, `follower_count`, `email`, `phone`, and more.

If a domain has no LinkedIn page, or the page hit a login wall, the row goes to the **`misses`** dataset (also visible on the Storage tab) and you are not charged for it.

---

## Step 9 — Export your results

On the Default dataset page, click **Export**. Choose your format:

- **JSON** — best for code / API integrations
- **CSV** — best for Excel, Google Sheets, or your CRM
- **Excel** — direct `.xlsx` download
- **HTML** — a quick browser-friendly view

You can also use the dataset's API URL to pull results into your own tool — see the **API** tab on the dataset page.

---

## Bulk via CSV — for very large lists

If you have thousands of domains in a CSV file:

1. Upload your CSV to a public URL (Google Drive shared link, S3 bucket, GitHub raw file, etc.). The CSV needs a column named `domain` or `website`.
2. In the Actor's input form, paste the URL into the **CSV file URL** field.
3. Leave the Domain Names field empty.
4. Run as normal.

The Actor will read the CSV, normalise each domain, and process them all.

---

## Programmatic usage — Apify API

If you want to trigger runs from your own code:

### Python

```python
from apify_client import ApifyClient

client = ApifyClient("YOUR_APIFY_TOKEN")

run = client.actor("yonecode/linkedin-company-enricher").call(run_input={
    "domains": ["airbnb.com", "stripe.com"],
    "extract_socials": True,
    # "linkedin_cookies": "AQE...",   # uncomment for Deep Mode
})

for item in client.dataset(run["defaultDatasetId"]).iterate_items():
    print(item["domain"], item.get("company_name"), item.get("industry"))
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

Get your Apify API token from **Settings → Integrations** in your Apify Console.

---

## Common questions

### How much does it cost?

You pay only when a row carries a real LinkedIn URL. Misses, login walls, and failed lookups are free.

| Result type | Per 1,000 |
|---|---|
| Fast result | $2.00 |
| Fast + socials | $3.50 |
| Deep result | $6.00 |
| Deep + socials | $8.00 |

See [`docs/PRICING.md`](docs/PRICING.md) for full pricing details and worked examples.

### My run shows "AUTH_WALL_DETECTED" or "Cookie expired"

Your `li_at` cookie has expired. Repeat Step 5 to get a fresh cookie value. LinkedIn cookies typically last several weeks.

### My results are mostly in the `misses` dataset

A few things to check:

- Your input contains real domain names, not URLs (`airbnb.com`, not `https://airbnb.com/something`).
- The companies you are looking up actually have LinkedIn company pages.
- For Deep Mode: your `li_at` cookie is fresh and from an account in good standing.

### Can I run it on a schedule?

Yes. On the Actor's page click **Schedule** and pick a frequency. Schedules use the same input as a one-off run.

### How do I share a run with a teammate?

Click the **Share** button on the run page. Apify generates a shareable URL — anyone with the link can view the run, log, and dataset (read-only) without an Apify account.

---

## Next steps

- Read [`docs/USE_CASES.md`](docs/USE_CASES.md) for example workflows (sales prospecting, CRM enrichment, supplier discovery, recruiter sourcing).
- Read [`docs/INTEGRATIONS.md`](docs/INTEGRATIONS.md) to plug results into n8n, Make, Zapier, Google Sheets, or a data warehouse.
- Read [`docs/FAQ.md`](docs/FAQ.md) for answers to less common questions.

---

## Get help

- Open a [GitHub issue](https://github.com/YoneCode/linkedin-company-enricher/issues)
- Email [YoneCodee@proton.me](mailto:YoneCodee@proton.me)
- Submit feedback on the Apify Store page
