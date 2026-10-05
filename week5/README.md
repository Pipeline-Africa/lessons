# Week 5: Web Scraping & File Processing

**Data Engineering Course · Pipeline Africa**

---

## Overview

Last week the data arrived as JSON because somebody built an API for it. Most of the web is not like that. This week you learn to take data from pages built for human eyes — static HTML with BeautifulSoup, JavaScript-rendered pages with Playwright — and then to choose, deliberately, what format it lands in: CSV, JSON, Excel or Parquet.

By Friday you will have scraped a live job board, validated the harvest, stored it as Parquet and CSV, and loaded it into PostgreSQL with a pipeline that is safe to run twice.

---

## Learning Objectives

By the end of this week you will be able to:

1. Read HTML structure and write CSS selectors that survive a redesign
2. Parse HTML with BeautifulSoup — `find`, `select`, attributes, and tree navigation
3. Find selectors in your browser's DevTools and test them before writing Python
4. Scrape a static site politely: headers, timeouts, retries, rate limits and caching
5. Follow pagination and detail-page links, resolving relative URLs correctly
6. Recognise a JavaScript-rendered page and scrape it with Playwright
7. Wait for elements properly, handle infinite scroll, and fill in forms
8. Spot the hidden JSON API behind a dynamic page — and skip the browser
9. Read and write CSV, JSON/JSONL, Excel and Parquet, and justify which you used
10. Explain what each format costs you in size, speed and type fidelity
11. Scrape responsibly: `robots.txt`, Terms of Service, personal data, rate limiting

---

## Schedule

### Technical

| Day | Topic | File |
|-----|-------|------|
| 1 | HTML, the DOM & CSS Selectors | [lesson1_html_and_selectors.ipynb](lesson1_html_and_selectors.ipynb) |
| 2 | Static Scraping with requests & BeautifulSoup | [lesson2_static_scraping.ipynb](lesson2_static_scraping.ipynb) |
| 3 | Dynamic Scraping with Playwright | [lesson3_playwright.ipynb](lesson3_playwright.ipynb) |
| 3–4 | File Processing: CSV, JSON, Excel & Parquet | [lesson4_file_formats.ipynb](lesson4_file_formats.ipynb) |
| 4 | Lab: Remote Job Market Pipeline | [labs/lab5_job_market_pipeline.ipynb](labs/lab5_job_market_pipeline.ipynb) |
| 5 | Scraping & File Format Exercises | [exercises/scraping_exercises.ipynb](exercises/scraping_exercises.ipynb) |

> **Order matters.** Lesson 1 works entirely offline on saved pages — do it first, and get comfortable with selectors before adding the network. Lesson 3 needs Lesson 2's `requests` version for contrast.

### Activities

| Activity | File |
|----------|------|
| Discussion + Reading Club + Project Review | [activities/week5_activities.ipynb](activities/week5_activities.ipynb) |

---

## Practice Sites

Every live example in this week points at a site **built for scraping practice**. They are stable, they do not mind the traffic, and their HTML has not changed in years — so you debug your selectors, not someone's redesign.

| Site | Used for | Why |
|------|----------|-----|
| [books.toscrape.com](https://books.toscrape.com) | Lesson 2, Exercises 7–12 | 1,000 books, 50 pages, detail pages — a complete static site |
| [quotes.toscrape.com/js](https://quotes.toscrape.com/js) | Lesson 3, Exercises 13–18 | the same content as the static page, but rendered by JavaScript |
| [quotes.toscrape.com/scroll](https://quotes.toscrape.com/scroll) | Lesson 3, Exercise 17 | infinite scroll backed by a hidden JSON API |
| [realpython.github.io/fake-jobs](https://realpython.github.io/fake-jobs/) | Lab 5 | 100 job postings, each with a detail page |

**Never point your first scraper at a real company's website.** You will be debugging a selector and an IP ban at the same time.

### Offline practice pages

`data/pages/` holds saved HTML so Lesson 1 and Exercises 1–6 need no internet at all:

| File | Contents |
|------|----------|
| `job_board.html` | 6 job postings with messy whitespace, two missing salaries, and a "next" link |
| `job_board_page2.html` | 4 more postings, with a "previous" link |
| `salaries_table.html` | two HTML `<table>` elements for `pd.read_html()` |

---

## Setup

### Python packages

```bash
pip install requests beautifulsoup4 lxml pandas pyarrow openpyxl playwright psycopg2-binary python-dotenv
playwright install chromium
```

The second command downloads the browser Playwright drives (~150 MB). Run it once per machine.

### PostgreSQL

Create this week's database:

```sql
CREATE DATABASE jobs_db;
```

### Environment variables

Create a file called `.env` in the **`week5/`** folder:

```
DB_HOST=localhost
DB_PORT=5432
DB_NAME=jobs_db
DB_USER=postgres
DB_PASSWORD=your_password_here
```

**Do not commit `.env`.** It is already listed in the repository's `.gitignore` — verify with `git check-ignore -v week5/.env` before your first commit.

### If Playwright fails to launch on Windows

Launching a browser needs asyncio subprocess support, which some older Jupyter builds on Windows lack. If `pw.chromium.launch()` raises `NotImplementedError`:

```bash
pip install -U jupyter ipykernel tornado
```

then restart the kernel. If it persists, run your scraper as a `.py` script with Playwright's **sync** API — which is how Lab 5 is built anyway. Lesson 3.2 covers this in full.

---

## Files in This Week

```
week5/
├── README.md                              ← You are here
├── lesson1_html_and_selectors.ipynb       ← HTML, the DOM, BeautifulSoup, CSS selectors, ethics
├── lesson2_static_scraping.ipynb          ← requests, polite fetching, pagination, detail pages, caching
├── lesson3_playwright.ipynb               ← async API, locators, waiting, scroll, forms, hidden APIs
├── lesson4_file_formats.ipynb             ← CSV, JSON/JSONL, Excel, Parquet, partitioning, benchmarks
├── activities/
│   └── week5_activities.ipynb             ← Discussion, Reading Club, Project Review
├── data/
│   ├── pages/
│   │   ├── job_board.html                 ← Offline practice page 1 (6 postings)
│   │   ├── job_board_page2.html           ← Offline practice page 2 (4 postings)
│   │   └── salaries_table.html            ← Two HTML tables for pd.read_html()
│   ├── sample_jobs.json                   ← Nested JSON for json_normalize
│   ├── raw/                               ← Created by the lab (raw JSONL)
│   └── clean/                             ← Created by the lab (jobs.parquet, jobs.csv)
├── exercises/
│   └── scraping_exercises.ipynb           ← 24 exercises across 5 sections
└── labs/
    ├── lab5_job_market_pipeline.ipynb     ← The week's pipeline, step by step
    └── scrape_jobs.py                     ← Written by the lab's %%writefile cell
```

---

## Project: Remote Job Market Pipeline

**Pipeline:** `Website → Playwright → JSONL → pandas → CSV + Parquet → PostgreSQL`

**Requirements:**

1. Scrape all 100 postings from the Fake Python job board with Playwright, driven by a standalone `.py` script
2. Follow 25 of them to their detail pages for the full description
3. Save the raw output as JSON Lines, untouched, before any cleaning
4. Validate the harvest: row count, nulls, duplicates, and the distribution of every column
5. Clean and derive: `city`, `region`, `posted_date`, `seniority`, `role_family`, skill flags, `scraped_at`
6. Save `data/clean/jobs.parquet` (pipeline) and `data/clean/jobs.csv` (humans)
7. Load into a PostgreSQL table `job_postings` with `job_url` as the primary key and an `ON CONFLICT` upsert
8. Prove the load is **idempotent** — run it twice, assert the row count is unchanged
9. Answer five business questions in SQL

**Deliverable:** 100 rows in `job_postings`, a Parquet file whose types survive a round-trip, a log of the full run in `logs/lab5.log`, and five answered questions.

See [labs/lab5_job_market_pipeline.ipynb](labs/lab5_job_market_pipeline.ipynb) for the step-by-step guide.

---

## Scraping Responsibly

Not optional, and not only a legal matter — it is what separates an engineer from a nuisance.

| Rule | How |
|------|-----|
| Prefer an API | Check for one *first*; scrape only when there is none |
| Check `robots.txt` | `site.com/robots.txt` before you start |
| Read the Terms of Service | Look for "automated", "crawl", "scrape" |
| Rate-limit every loop | `time.sleep(1)` between requests, and always a page cap |
| Identify your scraper | A `User-Agent` with your project name and a contact address |
| Cache what you fetch | Iterate on saved HTML, not on the live site |
| Avoid personal data | The NDPR and GDPR apply to scraped names, emails and phone numbers |
| Never bypass a login or paywall | That is not scraping any more |

Push your completed lab and exercises to GitHub before Week 6.
