# agentic scraping: why AI agents get blocked, and how to wire ScraperAPI into Claude or Cursor without burning credits

Agentic scraping means handing a web data job to an AI agent and letting it work out the steps itself: search, open pages, compare what it found against the goal, and extract structured fields. The model part is the easy bit. What actually decides whether the whole thing works is the retrieval layer underneath it, because an LLM can't make HTTP requests on its own, and the moment it tries through ordinary means, anti-bot systems start feeding it CAPTCHAs. This piece covers how the pattern works, where it breaks, and how ScraperAPI slots in as that retrieval layer — including the credit math that decides whether your agent runs for a month or dies in an afternoon.

> **The short version:** there are two ways to start free. A 7-day trial gives you **5,000 API credits with no credit card required**; the permanent free plan gives 1,000 credits with up to 5 concurrent requests. Either way, point your agent at the MCP server with one config snippet and it can call Google, Amazon, Walmart, eBay, and Redfin as structured tools. 👉 [Start the 5,000-credit trial and test your real targets](https://www.scraperapi.com/?fp_ref=coupons). One warning before you scale anything: Google SERP requests cost 25 credits each and LinkedIn costs 30, so read the credit table further down before letting an agent loose.

## What agentic scraping actually is

A traditional scraper follows a fixed pipeline: fetch this URL, parse these fields, done. An agentic setup replaces that with a loop. The agent receives a goal in plain language, plans a next action, executes it, observes the result, checks whether it has everything the goal asked for, and either continues or extracts the data and stops.

Two details in that loop matter more than people expect. First, the agent doesn't read raw HTML — well-built scraping systems strip out irrelevant code and hand the model rendered text or simplified markdown, which keeps the context window focused on actual content instead of markup soup. Second, the agent needs a stopping condition: a cap on actions, pages, or tokens. Without one, it keeps acting after it already found enough data. Hold that thought; it becomes very concrete when we get to pricing.

There's also a failure-mode difference worth knowing before you build. Traditional scrapers fail loudly — errors and empty fields. Agents fail quietly: they can return plausible-but-wrong data and carry on. A price-monitoring agent that "succeeds" with fabricated numbers is worse than one that crashes. Which brings us to the actual bottleneck.

## Why agents fail on protected sites

There are two failure modes, and both show up fast in practice. The first: an agent's built-in fetch tools make plain HTTP requests, so anti-bot systems flag the datacenter IP and answer with a 403, a CAPTCHA, or a blocked page. The agent gets nothing useful, or worse, a silent empty result it treats as truth.

The second is browser-driven agents. One third-party benchmark published in 2026 tested AI agents at scale and found they were blocked on roughly 61–74% of protected targets, at $12–48 per thousand pages, versus around 3% blocked and $1.50–3.00 per thousand for managed scraping APIs. Treat those as one lab's numbers rather than gospel, but the pattern they point to isn't controversial: the expensive part of agent scraping is unglamorous infrastructure — rotating proxies, JavaScript rendering, CAPTCHA solving, and per-domain logic that a raw agent simply doesn't carry.

This is the gap ScraperAPI occupies. It routes every request through 40M+ proxies with CAPTCHA handling and JS rendering, and the company claims up to a 99% success rate on protected sites like Amazon and Google. When a site updates its anti-bot protection, the adaptation happens on their side. That matters more than any single benchmark number: your agent's behavior doesn't change at all.

## Where ScraperAPI fits in an agent stack

The cleanest answer: your agent plans and reasons, ScraperAPI fetches. The connection is an MCP server, and setup is one config snippet pointing at `mcp.scraperapi.com/mcp` with your API key sent as an Authorization header. The remote server auto-starts with your client — nothing to install locally. The same config works in Claude Code, Cursor, Windsurf, and Cline.

What the agent gets through that server matters for your token budget. There are structured tools for Google Search, Amazon, Walmart, eBay, and Redfin that return JSON fields — price, title, results — instead of raw HTML to parse, which uses far fewer LLM tokens. A generic scrape tool covers raw HTML or markdown from any other URL. If MCP isn't your thing, there's a LangChain integration the company describes as two lines of Python, plus LlamaIndex, a CLI that prints the exact credit cost before it runs, and no-code routes through n8n and Zapier.

👉 [Open ScraperAPI's AI agent setup guide](https://www.scraperapi.com/solutions/ai/cli/?fp_ref=coupons) if you want the integration details before signing up.

For fairness: ScraperAPI's own comparison page positions it against Firecrawl, Playwright MCP, and Bright Data MCP, arguing that lightweight MCP scrapers like a local Playwright setup fetch pages without real unblocking and fail on protected sites, while enterprise web-access MCPs bring zones, latency, and enterprise pricing. That's a vendor talking about competitors, so weight it accordingly — but the "transparent credit pricing" part of the pitch is verifiable, and it's the next section.

## ScraperAPI plans and prices

All paid plans share the same feature set: JavaScript rendering, premium residential and mobile IPs, advanced anti-bot bypassing, structured data endpoints, DataPipeline, unlimited bandwidth, and a stated 99.9% uptime. What changes as you move up is credits, concurrency, geotargeting range, and billing flexibility — higher tiers buy more volume and wider reach, not better scraping quality.

All paid plans take **10% off with annual billing**. Prices below are the current listed monthly rates.

| Plan | Monthly price | Annual (per month) | Credits | Concurrency | Sign up |
| --- | --- | --- | --- | --- | --- |
| Free | $0 | — | 1,000 | 5 | [ Start free](https://www.scraperapi.com/?fp_ref=coupons) |
| Hobby | $49 | $44.10 | 100,000 | 20 | [ Get Hobby](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Startup | $149 | $134.10 | 1,000,000 | 50 | [ Get Startup](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Business | $299 | $269.10 | 3,000,000 | 100 | [ Get Business](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Scaling | $475 | $427.50 | 5,000,000 | 200 | [ Get Scaling](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Professional | $975 | $877.50 | 10,500,000 | 300 | [ Get Professional](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Advanced | $1,975 | $1,777.50 | 21,500,000 | 500 | [ Get Advanced](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Enterprise | Custom | Custom | 22M+ | 500+ | [ Contact sales](https://www.scraperapi.com/contact-sales/?fp_ref=coupons) |

A few differences that only show up if you read past the headline numbers:

- **Geotargeting is restricted on the two cheapest tiers.** Hobby and Startup can only target the US and EU; national-level targeting across 50+ countries starts at Business.
- **Analytics history is capped at 30 days on Hobby and Startup**, unlimited from Business up. If you're running recurring monitoring jobs and want to compare over time, that's a real constraint.
- **What happens when you run out differs by tier.** By default, scraping pauses until credits renew. Auto-upgrade (your account bumps to the next plan automatically when credits run out — opt-in) is available on Hobby through Professional. Pay-as-you-go, which keeps scraping past your limit at a fixed per-credit rate up to a monthly spending cap you set, is available on Scaling, Professional, Advanced, and Enterprise only. The pricing page's feature list shows "Pay as you go" on every row; the FAQ clarifies the actual availability. Don't plan a workload around PAYG on Hobby, Startup, or Business — it isn't there.

## The credit math most agent guides skip

Every request through ScraperAPI consumes credits, and the cost depends heavily on the target domain and the parameters you send. This is where agent budgets quietly fall apart.

| What you scrape | Credits per request |
| --- | --- |
| A standard page | 1 |
| Amazon, Walmart, or eBay pages | 5 |
| Google or Bing SERPs (all subdomains) | 25 |
| LinkedIn | 30 |
| Sites behind Cloudflare, Datadome, or PerimeterX | +10 when the bypass kicks in |
| Sites behind Akamai | 10 |

Add-ons stack on top: `render=true`, `premium=true`, or `screenshot=true` each add 10 credits; `ultra_premium=true` adds 30; `ultra_premium` plus rendering reaches 75 credits for a single request. Only paid plans can use the ultra-premium tiers.

Run the numbers and the multipliers get real fast. An agent price-checking ten Amazon listings burns 50 credits. Twenty Google searches cost 500 — half the free plan's entire allowance. And at the extreme end, one cost analysis of the $49 Hobby plan worked out that its 100,000 credits cover 100,000 plain static pages, roughly 4,000–20,000 typical Amazon pages with rendering, or fewer than 1,500 requests against a fully rendered Cloudflare-protected target. Same plan, a 70x spread in real output. "100,000 credits" and "100,000 requests" are different numbers.

Three levers keep this under control:

1. The Domain Cost Estimator in your dashboard shows the credit cost of any URL before you scrape it.
2. There's a `/account/urlcost` API endpoint — pass your API key and a target URL, and it returns the exact cost including any parameters you plan to use.
3. The `max_cost` parameter caps what any single scrape can consume.

And every response includes a `sa-credit-cost` header reporting what that request actually consumed, so you can audit your agent's spending after the fact rather than guessing from the invoice.

One billing detail worth internalizing: only 200 and 404 responses are charged, so blocked attempts don't eat your credits. Requests you cancel before the roughly 70-second processing window finishes do get charged, though — don't kill long-running jobs impulsively. Exceeding your plan's concurrency limit returns a 429 and the request is turned away rather than queued; there's no extra charge for going over, you just retry. Finally, unused credits don't roll over — they expire at each renewal, so sizing a plan far above your real monthly usage just buys waste.

👉 [Look up the credit cost of your target URL](https://www.scraperapi.com/?fp_ref=coupons) before you commit to a plan size.

## Setting up the MCP server

The whole thing takes about five minutes. First, create an account and copy your API key from the dashboard. Second, paste one snippet into your Claude Code MCP config or Cursor MCP settings, pointing at the endpoint above with the key in the Authorization header. Third, start your client — the remote server comes up on its own, and there's nothing to install.

The same config works in Windsurf and Cline, and ScraperAPI's docs have per-client setup guides if your editor isn't covered here. Behaviorally, the difference shows up immediately: a request routed through the MCP server goes out through rotating proxies with rendering and CAPTCHA handling, so your agent gets either the real page or a clear error message — a plain fetch tool gives you a 403 and lets the agent happily report failure as fact.

## Who this setup suits — and who should skip it

Skip it if your targets are static, unprotected public pages. A plain HTTP fetch costs nothing, agents' built-in tools work fine there, and paying per credit adds friction for no benefit. It's also overkill for one-off manual extractions — spinning up an agent for a single page you could read yourself is tooling theater.

The setup earns its keep when agents need protected-site data on any kind of schedule: Amazon price monitoring, Google SERP tracking, anything behind Cloudflare, PerimeterX, or Akamai. It also fits data pipelines — teams use it to build RAG datasets and training sets, pointing it at any URL and getting back clean text, markdown, or structured JSON that can be stored as JSONL or loaded straight into a vector database, with proxy rotation, rendering, and retries handled for you. ScraperAPI says more than 10,000 teams use it in some form, which at least tells you the pattern isn't niche.

## Quick answers before you commit

**Do failed requests cost credits?** No. Only 200 and 404 responses are billed. Blocked attempts don't consume credits; requests you cancel before the 70-second processing window do.

**What happens when my credits run out?** Scraping pauses until your credits renew, unless you turn on auto-upgrade (Hobby through Professional) or pay-as-you-go (Scaling and above), which keeps requests running at a fixed per-credit rate up to a monthly cap you set.

**Can my agent get structured data instead of HTML?** Yes — raw HTML, clean markdown, or structured JSON, with dedicated structured endpoints for Google, Amazon, Walmart, eBay, and Redfin returning fields like price, title, and results.

**Does my agent need a different setup for each tool I use?** No. One account and one pool of credits covers the MCP server, CLI, LangChain, LlamaIndex, and the no-code integrations. Switching how your agent connects doesn't change billing.

## Picking a plan before you sign up

Experimenting with agentic scraping for the first time? Take the 7-day trial — 5,000 credits is enough to test whether your actual target sites are even scrapeable, and it needs no credit card. Agents doing regular Amazon lookups? Hobby or Startup covers most testing, but check the geotargeting limit first if you need anything outside the US and EU; Business is where national targeting starts. Heavy Google or LinkedIn SERP usage? Budget around the 25- and 30-credit multipliers from the start, and prefer Scaling or higher so pay-as-you-go can absorb a busy day without pausing your workflows mid-job.

The last thing to do before choosing a tier: run your real target URLs through the cost checker, then multiply by your expected monthly volume. It's the difference between "my agent worked in the demo" and "my agent works in production" — and between a predictable bill and a plan that's too small or twice as big as you need. 👉 [Compare every plan and start with 5,000 free trial credits](https://www.scraperapi.com/?fp_ref=coupons)
