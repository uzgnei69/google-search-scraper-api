# google search scraper api: Credit Costs Explained, Real Pricing Math, and How to Pick the Right Plan

A Google search scraper API handles the ugly parts of SERP scraping — proxy rotation, CAPTCHAs, JavaScript rendering, HTML parsing — and hands you structured JSON with a single HTTP request. The part most comparison pages skip is what it actually costs per query, and that's where the real decision lives.

Here's the number that matters upfront: **ScraperAPI bills Google and Bing requests at 25 API credits each**, while a standard webpage costs 1 credit. So a plan labeled "100,000 credits" delivers roughly 4,000 Google searches, not 100,000. This article walks through the full plan list, converts credits into real search counts, compares ScraperAPI with SerpApi's per-search pricing, and covers the limits that only show up in the docs.

> **Quick take:** Most people doing occasional Google SERP work should start with the free trial (7 days, 5,000 credits, no credit card) and watch their actual usage before committing. Low-volume steady use fits Hobby at $49/month; heavier pipelines land on Business at $299/month. [👉 See ScraperAPI's current plans and annual discounts](https://www.scraperapi.com/pricing/?fp_ref=coupons)

## How a Google search scraper API works

You send one HTTP request with your query, country, and language. The provider's infrastructure rotates residential IPs, solves CAPTCHAs, renders pages when needed, and returns parsed results. Building this stack yourself means maintaining proxy pools, browser automation, and selectors that break every time Google tweaks its layout — and Google actively detects automated traffic, so DIY infrastructure tends to get both expensive and unstable.

Typical use cases for this kind of API: SEO rank tracking, competitor research, ad intelligence, keyword research, and feeding SERP features (featured snippets, People Also Ask, local packs) into dashboards or automation.

## ScraperAPI plans: the full price list

ScraperAPI currently shows seven plans on its pricing page. All of them include JS rendering, premium residential and mobile IPs, auto-parsing, CAPTCHA handling, custom sessions and headers, unlimited bandwidth, and a 99.9% uptime guarantee. Annual billing takes **10% off** every price.

| Plan | Price (monthly / annual) | Credits | Concurrency | Approx. Google searches/mo | Buy |
| --- | --- | --- | --- | --- | --- |
| Hobby | $49 / $44.10 | 100,000 | 20 | ~4,000 | [ Start Hobby](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Startup | $149 / $134.10 | 1,000,000 | 50 | ~40,000 | [ Start Startup](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Business | $299 / $269.10 | 3,000,000 | 100 | ~120,000 | [ Start Business](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Scaling | $475 / $427.50 | 5,000,000 | 200 | ~200,000 | [ Start Scaling](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Professional | $975 / $877.50 | 10,500,000 | 300 | ~420,000 | [ Start Professional](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Advanced | $1,975 / $1,777.50 | 21,500,000 | 500 | ~860,000 | [ Start Advanced](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Enterprise | Custom quote | 22,000,000+ | 500+ | Custom | [ Contact sales](https://www.scraperapi.com/pricing/?fp_ref=coupons) |

There's also a free option: registering gets you 1,000 credits with up to 5 concurrent connections, enough to verify the API works against your actual target queries. The "approximate searches" column above is my math, not ScraperAPI's marketing: credits ÷ 25, with no rendering or premium add-ons attached.

## The credit math most reviews get wrong

This is where plan labels and reality part ways. ScraperAPI's docs are explicit about domain-based pricing:

| Target type | Credits per request |
| --- | --- |
| Standard websites | 1 |
| E-commerce (Amazon, Walmart, eBay) | 5 |
| **Google, Bing (all subdomains)** | **25** |
| LinkedIn | 30 |
| Bot-protection bypass (Cloudflare, Datadome, PerimeterX, Akamai) | +10 each |
| `render=true`, `premium=true`, or `screenshot=true` | +10 each |
| `ultra_premium=true` | +30 (paid plans only) |

Two parameters matter a lot for Google scraping, and one of them is free:

- `autoparse=true` returns structured JSON instead of raw HTML at **no extra credit cost**.
- `render=true` adds 10 credits, and for plain Google SERP extraction it's usually unnecessary. Leave it off and every query stays at a flat 25 credits; turn it on and you're at 35 — a 40% cost increase for data you mostly already have.

Two habits keep billing predictable. First, query the cost endpoint before scaling: `https://api.scraperapi.com/account/urlcost?api_key=API_KEY&url=...` accepts all the same parameters as a real request and tells you the exact credit price. Second, check the `sa-credit-cost` response header on live requests to confirm consumption matches expectations.

One genuinely buyer-friendly rule: **ScraperAPI only charges for successful requests** — HTTP 200 and 404 responses, plus requests you cancel before its 70-second window closes. Timed-out and failed attempts after retries don't drain your balance, which matters when you're running tens of thousands of queries a month.

## The dedicated Google SERP endpoint: what it does and how to call it

ScraperAPI offers a structured endpoint specifically for Google search results at `https://api.scraperapi.com/structured/google/search`. It takes `api_key` and `query` as required parameters, plus a set of optional ones that map to Google's own search controls:

- **TLD** — which Google domain to scrape (20 values, from google.com and google.co.uk to google.com.au and google.pl). Defaults to google.com.
- **COUNTRY_CODE** — two-letter geo targeting. The docs note a subtlety: scraping google.com *from* another country (say, from Canada) requires both TLD and COUNTRY_CODE together.
- **UULE / HL / GL** — region encoding, interface language, and result-country weighting.
- **TBS** — time filters: `tbs=h` (past hour) through `tbs=y` (past year).
- **START** — result offset, so `start=10` with 10 results per page starts you on page two.
- **OUTPUT_FORMAT** — JSON (default) or CSV.

The response includes `search_information` (displayed query, total results, time taken) and an `organic_results` array with position, title, snippet, link, displayed link, and sitelinks. Client libraries and code samples exist for cURL, Python, Node.js, PHP, Ruby, and Java.

For large batch jobs there's an async version at `https://async.scraperapi.com/structured/google/search` — a POST request that accepts either a single `query` or a `queries` array, with an optional webhook callback so results push to your server instead of you polling. That's the difference between a script that hangs waiting for 10,000 sequential queries and one that fires them off and gets a callback per batch.

All of this runs on the same credit balance, so the 25-credit-per-search math applies to the structured endpoint too.

## ScraperAPI vs. SerpApi for pure Google search work

If Google queries are your entire workload, SerpApi prices per search rather than per credit, and the comparison is worth doing. Current figures from SerpApi's own pricing page:

| SerpApi plan | Price | Searches/mo | Throughput/hr | Implied cost per 1,000 |
| --- | --- | --- | --- | --- |
| Free | $0 | 250 | 50 | — |
| Starter | $25 | 1,000 | 200 | $25.00 |
| Developer | $75 | 5,000 | 1,000 | $15.00 |
| Production | $150 | 15,000 | 3,000 | $10.00 |
| Big Data | $275 | 30,000 | 6,000 | $9.17 |
| Searcher | $725 | 100,000 | 20,000 | $7.25 |

SerpApi counts only successful searches, but unused searches don't roll over, and every plan caps hourly throughput. Its Production tier and above include a U.S. Legal Shield (up to $2M in coverage for scraping-related legal exposure), and higher Cloud tiers add a ZeroTrace mode that discards query data after each request.

How the two actually stack up for Google search:

- **Cost per 1,000 queries:** ScraperAPI's Business plan works out to roughly $2.49 per 1,000 Google searches — cheaper than SerpApi's Production tier at $10.00. Even Hobby ($12.25 per 1,000) undercuts SerpApi's Developer plan ($15.00).
- **Volume you actually use:** the catch is commit size. SerpApi's Starter gives you 1,000 searches for $25; ScraperAPI's smallest paid plan is $49 for ~4,000. If you'll genuinely use 4,000, ScraperAPI wins on price. If you need 800, you're paying ScraperAPI for headroom you won't touch.
- **Mixed workloads:** the moment your project also touches Amazon product pages, general websites, or protected targets, ScraperAPI's single credit pool covers all of it, while a SERP-only API doesn't.
- **Legal and compliance:** SerpApi's Legal Shield is a real differentiator for compliance-sensitive buyers; ScraperAPI doesn't advertise an equivalent.

If your week involves scraping Google *and* a handful of product pages, [👉 check which credit tier your target domains fall into](https://www.scraperapi.com/pricing/?fp_ref=coupons) before choosing a plan size — the difference between 1-credit and 5-credit targets changes your budget faster than the plan choice does.

## Limits and edge cases hiding in the fine print

Several constraints only appear in the docs or FAQ, and each one bites a specific kind of user:

- **Geotargeting tier locks.** Hobby and Startup plans are limited to US and EU geo targeting. Country-level targeting anywhere in the world starts at Business ($299). If you track rankings in Japan or Brazil on a Hobby plan, you can't.
- **Analytics history.** The usage dashboard keeps only 30 days of history on Hobby and Startup. Business and above have unlimited analytics — relevant if you're building month-over-month rank reports from your own usage data.
- **Concurrency overage behavior.** Exceeding your plan's concurrent limit returns a 429 and the request is turned away — no queue, no extra charge. Spread spikes across the hour or size your concurrency before a big launch.
- **Credit exhaustion options.** Plans pause when credits run out. Auto-upgrade (opt-in on Hobby through Professional) bumps you to the next tier automatically; pay-as-you-go (Scaling and above) continues scraping at a fixed per-credit rate under a monthly spending cap you set. Neither is on by default — decide before your pipeline dies mid-job.
- **Custom-deal minimum pricing.** Discounted custom accounts face higher Ultra Premium credit costs to meet a floor of $3 per 1,000 unrendered requests ($7 rendered). Fine for most people, worth knowing before negotiating a deal.

## Getting started without wasting credits

1. **Sign up for the 7-day free trial** — 5,000 credits, no credit card. [👉 Create your trial account](https://www.scraperapi.com/pricing/?fp_ref=coupons)
2. **Run 20–30 of your real queries** through the API Playground with `autoparse=true` and your target country code. Confirm the JSON structure actually contains what your project needs.
3. **Check exact costs** for each query pattern via the urlcost endpoint before writing production code.
4. **Pick a plan from measured volume.** Monthly Google query count ÷ 1,000 × the per-1,000 cost above beats guessing from plan labels.
5. **Set a `max_cost` per scrape** in production so one misconfigured parameter (an accidental `ultra_premium`, say) can't quietly burn 75 credits per request.

## Which setup fits your situation

- **Testing or under ~4,000 Google searches/month:** start free, then Hobby at $49. Skip `render=true` on SERP queries and every search stays at 25 credits flat.
- **10,000–120,000 searches/month with mixed targets:** Startup or Business. Move to Business specifically if you need non-US/EU country targeting or unlimited analytics history.
- **Volume above 200,000/month:** Scaling and Professional bring the per-1,000 cost down to around $2.26–$2.38, and pay-as-you-go becomes available as an overflow valve.
- **Pure Google search, small volume, legal coverage matters:** SerpApi's Production tier ($150 for 15,000 searches with Legal Shield) is a reasonable alternative — just know you're paying roughly 4× per query versus ScraperAPI's Business tier for the same volume.

Before you commit either way, spend the trial credits on your real workload rather than toy queries — query complexity and target domains move the numbers more than plan choice does. [👉 Start with ScraperAPI's free 5,000-credit trial](https://www.scraperapi.com/pricing/?fp_ref=coupons)
