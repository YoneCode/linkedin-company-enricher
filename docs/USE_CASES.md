# Use cases

Concrete workflows that map to the Actor's two output modes.

## 1. Sales / outbound prospecting

**Problem.** A sales team has a list of 5,000 prospect domains from a paid intent-data provider. The CRM lacks LinkedIn URLs, industry, size, and HQ for most rows.

**Workflow.**
1. Upload the 5,000-row CSV to a public bucket (S3, Google Cloud Storage) or paste the domain column directly.
2. Run the Actor in Deep Mode with a dedicated LinkedIn cookie.
3. Stream results into the CRM via the Apify API or a Make / n8n / Zapier scenario.
4. Filter incoming rows by industry, size, or country to assign to the right sales rep.

**Cost.** 5,000 domains × $0.006 = $30.00 (Deep Mode without socials), or $40.00 with socials. Misses are not billed.

## 2. CRM hygiene and enrichment

**Problem.** A 50,000-row Salesforce / HubSpot account database has stale or missing LinkedIn URLs, employee counts, and headquarters.

**Workflow.**
1. Export accounts to CSV, keeping only the domain or website column.
2. Split into 5,000-row chunks (the Actor handles single runs of any size, but smaller chunks are easier to retry on partial failure).
3. Run each chunk in Fast Mode if you only need the LinkedIn URL, or Deep Mode if you also want size, industry, and HQ.
4. Re-import the enriched CSV into Salesforce / HubSpot via their bulk update API.

**Cost.** 50,000 domains × $0.002 (Fast Mode) = $100. Same job in Deep Mode = $300.

## 3. Recruiter / talent sourcing

**Problem.** A recruiter wants to build a shortlist of fintech companies in Germany with 50–500 employees.

**Workflow.**
1. Pull a starting domain list from any company-database tool (Crunchbase export, BuiltWith, etc.).
2. Run the Actor in Deep Mode.
3. Filter the output by `industry`, `headquarters`, and `company_size` band.

**Cost.** 1,000 domains × $0.006 = $6.00. The filter is downstream — no extra Actor cost.

## 4. E-commerce supplier discovery

**Problem.** A drop-shipping operator needs a list of wholesalers and manufacturers for a target product category.

**Workflow.**
1. Start from a domain list of trade-show exhibitors, niche directories, or competitor referrals.
2. Run the Actor in Deep Mode.
3. Filter rows where `is_ecommerce_supplier` is `true`. The `product_categories` array tells you which subset of supplier types matched.

**Cost.** 2,000 domains × $0.006 = $12.00. Most non-supplier rows still return useful profile data, so they're not wasted.

## 5. Market mapping and competitor research

**Problem.** A product-marketing team wants industry, size, and follower-count distributions across all companies in a given competitive landscape.

**Workflow.**
1. Compile a domain list of every company in the relevant SaaS or industry vertical.
2. Run the Actor in Deep Mode.
3. Aggregate `industry`, `company_size`, and `follower_count` into a chart or pivot table.

**Cost.** 500 domains × $0.006 = $3.00. Data is typically fresh enough for monthly market-pulse reports.

## 6. Lead scoring input

**Problem.** A growth team scores inbound leads using firmographic features (industry, size, country) but only has the domain at form submission time.

**Workflow.**
1. Trigger an Apify run via webhook each time a lead form is submitted (or batch nightly).
2. Use Fast Mode if you only need country and a LinkedIn URL match-confidence signal; Deep Mode if your scoring model uses industry or size bands.
3. Pipe the result into the lead-scoring service or marketing-automation tool.

**Cost.** Per lead: $0.002 (Fast) to $0.008 (Deep + socials). Negligible compared to typical lead-acquisition cost.

## 7. Bulk supplier / vendor verification

**Problem.** A procurement team needs to verify that 1,000 supplier candidates from a sourcing platform actually exist as registered companies with a real LinkedIn presence.

**Workflow.**
1. Run the Actor in Fast Mode.
2. Sort results into "has LinkedIn" (default dataset) vs "no LinkedIn" (`misses` dataset).
3. Companies that fail the test become the audit shortlist for manual verification.

**Cost.** 1,000 domains × $0.002 = $2.00.
