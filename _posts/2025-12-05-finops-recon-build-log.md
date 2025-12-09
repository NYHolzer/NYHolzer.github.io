---
layout: post
title: "Build Log: FinOps Recon – A Practical Reconciliation Engine"
---

`finops-recon` grew out of a real operational need:

> **How do we reconcile data across banks, custodians, and internal ledgers without using 5 CSVs and an hour of manual massaging?**

This project is my attempt to build a **lightweight, Python-based reconciliation engine** that transforms scattered financial data into a clean, comparable, exception-driven workflow.

Below is an overview of how the system works today, how it evolved, and how ChatGPT supported the design.

---

# 1. Goals

I built `finops-recon` to be:

- **Practical** — something an operations team could actually run.
- **Simple to deploy** — pure Python, SQLite, Pandas.
- **Schema-aware** — ingestion tools that clean & normalize fields.
- **Extensible** — new sources can be added without rewriting the engine.
- **Exception-driven** — highlight *breaks*, not everything.

The goal isn’t a full accounting system — it’s a **useful ops tool** that reduces manual friction.

---

# 2. Project Architecture

The repo is organized into four main layers:

finops_recon/
│
├── ingest/ # Tools for importing and normalizing CSVs
│ ├── csv_reader.py
│ └── schema_map.py
│
├── engine/ # Core reconciliation logic
│ ├── comparator.py
│ ├── diff_result.py
│ └── match_rules.py
│
├── output/ # Summaries, exception reports, dataframe formatting
│ └── report_builder.py
│
└── ui/ # CLI command and FastAPI stub for later API
├── cli.py
└── api.py


Each layer has a clear responsibility:

### **Ingest**  
Normalize messy bank/custodian/ledger CSVs into a consistent dataframe.

### **Engine**  
Run matching rules, compute breaks, generate recon results.

### **Output**  
Turn raw diffs into meaningful summaries (totals, mismatches, missing transactions).

### **UI**  
Expose everything through a friendly CLI (and eventually API).

---

# 3. Ingestion Layer – Normalizing Real-World CSVs

The biggest challenge in ops recon is always:

> **“No two CSV exports are ever formatted the same way.”**

The ingestion layer solves this through:

### **`csv_reader.py`**
- Loads arbitrary CSVs.
- Applies field normalization:
  - standardizes date formats  
  - trims & normalizes column names  
  - handles negative/positive amount formats  
  - converts ledger/bank fields into canonical form  

### **`schema_map.py`**
A lightweight “mapping notebook” that allows:

- Each institution to define its own column mapping.
- A central schema (`standard_fields`) to enforce consistency.

Examples:
- `txn_id` ↔ `transactionId` ↔ `Ref #`  
- `amount` ↔ `usd_amount` ↔ `delta`  
- `timestamp` ↔ `date` ↔ `posted_at`

This gives `finops-recon` its power:  
**once data is normalized, comparing it becomes easy.**

ChatGPT assisted here by generating initial mapping patterns and identifying common financial field variants.

---

# 4. Engine Layer – Matching, Diffing, and Exception Logic

The engine performs three core tasks:

---

## **4.1 Record Matching (`match_rules.py`)**

Defines how transactions are matched based on:

- transaction_id OR
- amount + timestamp OR
- counterparty-pattern matching

This module was iterated with ChatGPT by:

- brainstorming match strategies  
- refining ambiguity handling  
- adjusting for common real-world inconsistencies  

---

## **4.2 DataFrame Comparator (`comparator.py`)**

This is the heart of the project:

- Takes two normalized sources (e.g., *bank vs custodian*).
- Aligns them by key.
- Produces three results:
  - **matched** transactions  
  - **missing_from_left**  
  - **missing_from_right**

It outputs a `DiffResult` object with structured fields.

---

## **4.3 Diff Result Model (`diff_result.py`)**

A container class for:

- unmatched records  
- matched pairs  
- summary statistics  
- diagnostics for reconciliation audits  

It cleanly separates the *engine logic* from the *output logic*.

---

# 5. Output Layer – Reports for Humans

Ops teams don’t care about raw diffs — they want clear answers:

- What’s missing?  
- What doesn’t match?  
- What breaks do I need to fix?  

### **`report_builder.py`** turns structured diffs into:

- printable summaries  
- totals  
- mismatches grouped by category  
- CSV/Excel-friendly formatted output  
- CLI tables for quick inspection  

This was heavily refined based on the kind of output real ops teams expect.

---

# 6. UI Layer – CLI + API Stub

Right now the project offers a clean CLI:

---

## **CLI Example**

```bash
python -m finops_recon.cli run \
    --left bank_export.csv \
    --right custodian_export.csv \
    --map-left bank_schema.json \
    --map-right custodian_schema.json

### Outputs

- summary to terminal
- detailed reports to `/outputs/` folder

---

### FastAPI Stub (`api.py`)

This exists for future expansion into:

- running reconciliations from a dashboard
- uploading CSVs via UI
- returning JSON break reports

The scaffolding is already in place thanks to ChatGPT-assisted design.

---

## 7. How ChatGPT Assisted This Build

I used ChatGPT as a **pair engineer**, but *not* as the owner of the logic.

ChatGPT helped with:

- brainstorming ingestion schema fields
- drafting comparator skeletons
- outlining match strategies
- modeling `DiffResult`
- designing CLI UX patterns
- explaining pitfalls in multi-source reconciliation

I retained ownership of:

- field mapping decisions
- what constitutes a break
- how ops teams actually use recon output
- the workflow design

This hybrid approach allowed rapid iteration while staying grounded in real ops workflows.

---

## 8. What’s Next

Planned roadmap items:

- Add support for **multi-ledger reconciliation**
- Build a **web UI** for uploading CSVs
- Create a **recon rule DSL** so matching logic becomes configurable
- Add **BI export hooks** (Looker/Metabase-friendly)
- Implement **scheduled jobs** for continuous recon
- Expand ingestion mapping presets for major custodians/banks
- Add **exception categorization models** (machine-learning assisted)

---

## 9. Why This Project Matters

Reconciliation is the glue of financial operations.  
Every fintech company does it, and every company struggles with it.

`finops-recon` demonstrates:

- Real-world ops understanding
- Data engineering fundamentals
- Clean Python architecture
- Testable, extensible design
- AI-assisted but human-driven engineering

{% include build-log-footer.html %}