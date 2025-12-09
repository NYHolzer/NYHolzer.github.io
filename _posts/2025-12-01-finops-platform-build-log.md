---
layout: post
title: "Build Log: FinOps Platform – Analyst Module & Core"
---

This project started as a simple idea:

> Could I build a **public finops lab** where I experiment with modular analysts, trading helpers, and operations automation – and keep it clean enough that other people could actually use it?

`finops-platform` is my answer to that question.  
Right now it focuses on an **Analyst** module that pulls SEC data, computes basic metrics, and renders a readable HTML report for a given ticker, backed by a small shared `platform_core`.

---

## 1. Goals

I wanted this repo to:

- Be a **real Python package** (`pip install -e .` style), not just a loose script.
- Provide a **simple CLI** (`finops`) that anyone can run.
- Showcase **clean modules** for:
  - fetching SEC data (EDGAR)
  - parsing financial statements into DataFrames
  - computing simple metrics
  - generating human-readable reports
- Include tests so I can refactor without guessing.
- Be built in a way that **modular “apps”** (Analyst, Trader, future Budget, etc.) can share infrastructure.

ChatGPT acted as a **pair programmer** here – I used it to help sketch the module layout, refine some of the helper functions, and tighten up edge cases, but all of the structure and decisions are tailored to the workflows I care about.

---

## 2. High-level Architecture

The repo is structured around a couple of main pieces:

- `analyst/` – the current “main” module
  - `edgar.py` – SEC/EDGAR helpers
  - `financials.py` – fetches & normalizes financial statement data into Pandas
  - `metrics.py` – computes things like current ratio, ROE, etc.
  - `report.py` – orchestration layer that builds an HTML report
  - `summarize.py` – tiny TF-IDF based text summarizer
- `platform_core/`
  - `config.py` – loads `.env`, SEC contact email, default ticker, etc.
  - `publish.py` – builds docs for each module (Analyst, Trader) into `docs/<module>/`
  - `report_template.py` – shared HTML layout renderer
- `trader/`
  - `report.py` – currently a placeholder HTML report; a stub for future trading tools
- `cli.py`
  - exposes a `finops` CLI entrypoint via `pyproject.toml`

There is also:

- `.github/workflows/publish-modules.yml` – a GitHub Actions workflow that calls `python -m platform_core.publish analyst` / `trader` and commits the rendered docs.
- `tests/` – pytest suite covering EDGAR helpers, financial parsing, metrics, CLI smoke tests, and the report template.

---

## 3. Analyst Module – From EDGAR to HTML Report

The **Analyst** module is the most complete part today.

### 3.1 Fetching SEC Data (`analyst/edgar.py`)

This file handles the messy bits of talking to the SEC:

- Resolves **ticker → CIK**, with:
  - support for passing a CIK directly
  - a local JSON cache
  - a small built-in fallback map
- Uses **proper SEC headers** with a contact email (per fair-use guidance).
- Builds URLs under `https://data.sec.gov` correctly.
- Downloads the **latest primary filing HTML** and stores it under `data/filings/…`.
- Uses BeautifulSoup to extract section blocks from the filing HTML.

This is one of the places where I iterated heavily with ChatGPT:
- I used it to spot issues with headers and URL construction.
- Then tightened the implementation to be more robust and cache-friendly.

### 3.2 Parsing Financials (`analyst/financials.py`)

This module focuses on turning SEC data into something usable:

- Downloads JSON data from the SEC company facts API.
- Extracts common keys like `Assets`, `AssetsCurrent`, `LiabilitiesCurrent`, etc.
- Normalizes it into **Pandas DataFrames**, keyed by period and statement item.
- Provides `load_balance_sheet(...)` helpers used by metrics.

The goal is not to replicate a full XBRL engine, but to get to:
> _“Give me a clean time series of the fields I care about so I can compute basic ratios.”_

### 3.3 Metrics (`analyst/metrics.py`)

This module provides metric functions like:

- `current_ratio(...)`
- ROE and similar metrics

It also defines a small `METRIC_CATEGORIES` dict to group metrics by theme (liquidity, profitability, leverage, etc.), which will be useful later for explaining metrics in English.

There are tests in `tests/test_metrics_*.py` that:

- Check the calculation against known inputs
- Make sure metric aliases resolve properly

### 3.4 Text Summaries (`analyst/summarize.py`)

This is a lightweight **extractive summarizer**:

- Splits text into sentences with a regex.
- Uses `TfidfVectorizer` to score sentences.
- Returns the top-k sentences in original order.

It’s intentionally tiny and dependency-light (just scikit-learn + numpy), but gives a nice way to pick out key sentences from long sections like MD&A or Risk Factors.

### 3.5 Putting It Together (`analyst/report.py`)

The report module glues everything:

- Uses `latest_filing_meta(...)` and `download_latest_primary_document_html(...)` from `edgar.py`.
- Pulls out specific sections (e.g. MD&A, Risk Factors) via `extract_section_texts(...)`.
- Loads financials and computes metrics (`current_ratio` for now, extensible later).
- Cleans the text for summarization, then calls `top_sentences_tfidf(...)`.
- Renders a **complete HTML page** via `platform_core.report_template.render_page(...)`.

From the outside, the flow is:

```bash
finops analyst AAPL
# → fetch SEC data
# → compute metrics
# → render docs/analyst/index.html

## 4. Shared Platform Core

### `platform_core/config.py`

- Loads `.env` using `python-dotenv`.
- Exposes settings like:
  - `SEC_CONTACT_EMAIL`
  - `SEC_USER_AGENT`
  - `ANALYST_DEFAULT_TICKER`
- Centralizes configuration so modules do not hard-code settings.

---

### `platform_core/report_template.py`

- Defines `render_page(...)`, which:
  - Builds the full HTML document shell.
  - Injects the module-specific HTML body.
  - Adds simple navigation between modules (Analyst, Trader, etc.).

---

### `platform_core/publish.py`

- Receives a module name (`analyst`, `trader`, etc.) and calls that module’s `render_report(...)`.
- Writes output to:  
  `docs/<module>/index.html`
- This is used by the GitHub Actions workflow so that:
  - Each run generates fresh HTML for every module.
  - The workflow commits updated `docs/` output back into the repo.

This design makes future modules (e.g., `budget`, `risk`, etc.) easy to add — all they need is a `render_report(...)` function and a docs directory.

---

## 5. CLI & Packaging

The CLI is defined in `cli.py` and wired via `pyproject.toml`.

### Commands

- The CLI entrypoint is named **`finops`**.

Example usage:

```bash
finops analyst              # uses ANALYST_DEFAULT_TICKER or AAPL
finops analyst TSLA        # explicit ticker

Internally the CLI:

- Parses arguments with `argparse`.
- Routes `finops analyst` to `analyst.report.render_report(...)`.
- Prints a success/failure message and the output file path.

There is also a smoke test (`tests/tests_cli_smoke.py`) ensuring the CLI does not crash.

---

## 6. Tests

The `tests/`directory currently covers:

- EDGAR helpers (`test_edgar_helpers.py`)
- Financial statement parsing (`test_financials_parse.py`)
- Metric functions & aliases (`test_metrics_*`)
- Report template rendering (`test_report_template.py`)
- CLI smoke tests

ChatGPT helped with:

- Drafting initial test structures
- Hardening edge cases
- Reducing regressions when refactoring

`pytest` ensures the platform behaves consistently as new modules evolve.

---

## 7. How ChatGPT Fits In

Throughout this project I used ChatGPT as a **pair programmer and rubber duck**, specifically for:

- Sketching the initial module layout (`analyst`, `platform_core`, `trader`)
- Improving the EDGAR request logic and error handling
- Prototyping the TF-IDF summarizer
- Drafting early versions of test files
- Iterating UX for the CLI
- Refining configuration patterns

But the core decisions —  
**what problems to solve, what data to fetch, how reports should look for ops teams** —  
came from real financial operations experience.

ChatGPT sped up iteration; it did not define the problem.

---

## 8. What’s Next

Planned enhancements:

- Broader metrics catalog (profitability, leverage, cash-flow, efficiency)
- Real Trader module with pricing, positions, and basic analytics
- Better HTML styling for Analyst reports
- REST API endpoint so reports can be generated externally
- Potential integrations with other finops tools

For now, `finops-platform` remains a public playground for experimenting with **Python, data workflows, and AI-assisted fintech tooling**.

{% include build-log-footer.html %}