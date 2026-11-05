<!-- PUBLISH AFTER: website-to-markdown is public on the Apify Store -->
# Website to Markdown Crawler: Python example

> Website to Markdown Crawler is being prepared for the Apify Store. The link below goes live when it is published.

A small, runnable example for [Website to Markdown Crawler](https://apify.com/aurelix/website-to-markdown), an Apify Actor by Aurelix.

[![Website to Markdown Crawler: 75-second demo video](https://api.apify.com/v2/key-value-stores/twkCCtTWzsi8fVbx5/records/website-to-markdown-demo-thumb-v1.png)](https://api.apify.com/v2/key-value-stores/twkCCtTWzsi8fVbx5/records/website-to-markdown-demo-v1.mp4)

**[▶ Watch the 75-second demo](https://api.apify.com/v2/key-value-stores/twkCCtTWzsi8fVbx5/records/website-to-markdown-demo-v1.mp4)**

## What it does

Website to Markdown Crawler crawls a website and turns each page into one row of clean Markdown of its main content, with navigation, headers, footers, sidebars, cookie banners and scripts stripped and headings, lists, tables, code blocks and links kept. Each row also carries the URL, title, meta description, language, last-modified date, headings, links and word count for chunking and citations. Crawl one docs section or the whole site, with include and exclude URL globs.

## Why this over alternatives

Compared with the leading alternatives on the Apify Store (full table on the Store page):

- A flat $1.50 per 1,000 pages: 34% under Apify's RAG Web Browser, and no compute-unit billing at 8 GB like Apify's Website Content Crawler.
- Crawls a whole site at 256 MB, robots.txt always obeyed, failed, duplicate and empty pages never charged.
- Not offered: JavaScript rendering, which both Apify Actors have.

## When to use it

- AI developers loading documentation sites into a vector database for RAG
- Teams building a chatbot or support assistant on their own help center
- Researchers turning blogs and knowledge bases into clean text
- AI agents that need a site's pages as Markdown through MCP

## Input example

This is the `apify-docs-to-markdown` input used in `main.py`:

```json
{
  "startUrls": [
    "https://docs.apify.com/academy"
  ],
  "maxPages": 100
}
```

Every input field is documented on the [Store page](https://apify.com/aurelix/website-to-markdown).

## How to run

1. Get an Apify API token from Apify Console (Settings, API & Integrations).
2. Install and run:

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
export APIFY_TOKEN=your_token_here
python main.py
```

The script starts a run of `aurelix/website-to-markdown`, prints a few fields from the first results and writes every result to `results.csv`. Lists and nested objects are stored in the CSV as JSON text. Each run is billed to your Apify account (see Pricing below).

## Sample output

One record as returned by a real run (long text is shortened):

```json
{
  "url": "https://docs.apify.com/academy",
  "title": "Apify Academy | Academy | Apify Documentation",
  "description": "Learn everything about web scraping and automation with our free courses that will turn you into an expert scraper developer.",
  "language": "en",
  "last_modified": "2026-11-04T07:17:58Z",
  "markdown": "# Apify Academy\n\nLearn everything about web scraping and automation with our free courses that will turn you into an expert scraper developer.\n\n## Beginner courses\n\n![](https://docs.apify.com/img/academy/scraping-with-ai.svg)\n#### Web scraping with AI\n\nLearn how to use AI to extract information from... [2198 more characters]",
  "headings": [
    {
      "level": 1,
      "text": "Apify Academy"
    },
    {
      "level": 2,
      "text": "Beginner courses"
    },
    {
      "level": 4,
      "text": "Web scraping with AI"
    },
    {
      "level": 4,
      "text": "Web scraping basics with JS"
    },
    {
      "level": 4,
      "text": "Web scraping basics with Python"
    },
    "... [6 more]"
  ],
  "links": [
    "https://docs.apify.com/academy/scraping-with-ai",
    "https://docs.apify.com/academy/scraping-basics-javascript",
    "https://docs.apify.com/academy/scraping-basics-python",
    "https://docs.apify.com/academy/apify-platform",
    "https://docs.apify.com/academy/api-scraping",
    "... [3 more]"
  ],
  "word_count": 231,
  "crawl_depth": 0,
  "http_status": 200,
  "source_url": "https://docs.apify.com/academy",
  "scraped_at": "2026-11-04T11:25:08Z"
}
```

## Export to CSV

`main.py` writes `results.csv` with the `csv` module. You can also download any run's results directly:

```
https://api.apify.com/v2/datasets/<datasetId>/items?format=csv&clean=true
```

Send your token in an `Authorization: Bearer <token>` header (or `?token=`) if the dataset is not readable without one. Other formats: `format=json`, `format=xlsx`.

## Google Sheets

- Simplest: use Apify's Google Sheets integration on the Actor or a saved task, so each run's results land in a sheet with no token in the sheet itself.
- Quick pull of one run: `=IMPORTDATA("https://api.apify.com/v2/datasets/<datasetId>/items?format=csv&clean=true")`. This only works without a token if your account lets anyone with the dataset ID read it. Do not paste an API token into a sheet formula: anyone who can view or copy the sheet can read it and use your account.

## n8n

- Apify node: choose "Run an Actor", enter `aurelix/website-to-markdown`, paste the input above, then read the run's dataset items.
- HTTP Request node: `POST https://api.apify.com/v2/acts/aurelix~website-to-markdown/run-sync-get-dataset-items` with header `Authorization: Bearer <token>` (stored as an n8n credential) and the input as the JSON body. The response is the list of results. This endpoint waits for the run to finish, so for large runs start the run asynchronously and fetch the dataset when it ends.

## Limits

- No JavaScript rendering: client-side-only pages (an empty app shell) are skipped and listed in the log, not converted.
- Only HTML pages are converted; PDFs, images and other files are skipped.
- Scope is one site per start URL: www. and the bare domain count as one site, but other subdomains are not followed.
- At most two new requests per second per site (slower when robots.txt sets a Crawl-delay). Conversion is CPU-bound: about 1,000 pages an hour on large documentation pages at 256 MB, so the default 1-hour run fits roughly 1,000 to 3,000 pages. The run stops itself before its timeout and ends SUCCEEDED with the pages done so far; raise the timeout or memory for bigger crawls.
- HTML pages over 2 MB are skipped with a log line and not charged, to keep memory low.
- Main-content detection is automatic; on unusual layouts a little boilerplate can remain or a side block can be dropped.
- Pages behind a login, paywall or bot challenge are not accessible.

## Pricing

At the time of writing:

Pay per event: you pay only for what the run returns, plus a small start fee. Bronze, Silver, Gold and higher Apify plans pay less per result.

| Event | Free plan | Paid plans (Bronze, Silver, Gold) |
|---|---|---|
| `page-result` | $1.50 per 1,000 pages | $1.05 per 1,000 pages |
| `apify-actor-start` | $0.001 per run | $0.001 per run |

Example: 1,000 pages cost $1.50 on the free plan and $1.05 on a paid plan, plus the start fee. Apify's free plan includes $5 of credit a month, enough for about 3,300 pages.

Flat $1.50 per 1,000 pages: no compute-unit surprises. Skipped pages are not charged: pages blocked by robots.txt, fetch errors, non-HTML responses, duplicate URLs or identical Markdown, pages that need JavaScript and pages with no main content.

Check the [Store page](https://apify.com/aurelix/website-to-markdown) for current prices.

## Links

- Actor: https://apify.com/aurelix/website-to-markdown
- Questions, bugs and feature requests: the Issues tab on the Store page.

## Licence

The example code in this repository is released under the MIT licence (see `LICENSE`). The Actor itself runs on Apify and is billed through your Apify account.
