# P2P Spend Intelligence 🧾📊

**An end-to-end Procure-to-Pay (P2P) spend-analytics & invoice-reconciliation data platform — built on a 100% free, open-source stack.**

From raw transactions → star-schema warehouse → automated three-way match → self-service dashboard. Models **$240M of indirect spend** across **400 suppliers** and **15,300 invoices**, and automatically flags **~$9.0M** of reconciliation exposure and duplicate-payment risk.

![Stack](https://img.shields.io/badge/stack-PostgreSQL%20%7C%20DuckDB%20%7C%20Python%20%7C%20Chart.js-1F8A80)
![SQL](https://img.shields.io/badge/SQL-window%20functions%20%7C%20CTEs%20%7C%20marts-0B1F3A)
![License](https://img.shields.io/badge/license-MIT-16A34A)
![Cost](https://img.shields.io/badge/cost-%240.00-D97706)

[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https://github.com/YOUR_USERNAME/p2p-spend-intelligence)

> **Live demo:** deploys to Vercel in one click (static site, no build). See [`DEPLOY.md`](DEPLOY.md).

> **Why this project?** Procure-to-Pay is the one domain shared by indirect-procurement teams (United, any FMCG/CPG), quick-commerce supply chains (Blinkit/Zepto), and corporate-card / spend-management businesses (Amex). The exact same primitives — PO, goods receipt, invoice, three-way match, P-card controls, supplier scorecards — appear in all of them. This repo is a portfolio-grade demonstration of those primitives in SQL.

---

## 🧠 The thought process (raw data → decision)

```
┌────────────┐   ┌──────────────┐   ┌───────────────┐   ┌──────────────┐   ┌────────────┐
│ 1. GENERATE│ → │ 2. MODEL     │ → │ 3. RECONCILE  │ → │ 4. ANALYSE   │ → │ 5. VISUALISE│
│ synthetic  │   │ star schema  │   │ 3-way match   │   │ 10 business  │   │ dashboard + │
│ P2P data   │   │ (6 dim/4 fact)│  │ (the control) │   │ questions    │   │ scorecards  │
│ Faker      │   │ PostgreSQL   │   │ SQL views     │   │ window fns   │   │ Chart.js    │
└────────────┘   └──────────────┘   └───────────────┘   └──────────────┘   └────────────┘
```

Procurement's core control is the **three-way match**: never pay an invoice unless it agrees with (a) what we **ordered** (PO) and (b) what we **received** (GRN). Expressing that control as a SQL view — and then measuring everything it catches — is the heart of this project.

---

## 🗂️ Repo structure

```
p2p-spend-intelligence/
├── README.md                     ← you are here
├── LICENSE                       ← MIT
├── sql/
│   ├── 01_schema.sql             ← star-schema DDL
│   ├── 02_marts.sql              ← three-way-match + spend views
│   └── 03_analysis.sql           ← the 10 business questions
├── scripts/
│   ├── generate_data.py          ← Faker synthetic-data generator
│   ├── validate_and_aggregate.py ← runs SQL, emits dashboard JSON
│   └── load_duckdb.py            ← one-command local build
├── data/
│   ├── data_dictionary.md        ← full table/column reference
│   ├── dashboard_data.json       ← pre-computed aggregates
│   └── *.csv                     ← generated tables (10 files)
├── index.html                    ← site root: project hub + walkthrough
├── sql-guide.html                ← SQL course: beginner → advanced
├── dashboard.html                ← interactive dashboard (offline, self-contained)
├── vercel.json                   ← static-deploy config (clean URLs)
├── .vercelignore                 ← keeps CSVs out of the web deploy
└── DEPLOY.md                     ← GitHub + Vercel steps
```

---

## 🚀 Quickstart (zero cost, ~2 minutes)

```bash
# 1. install the only three dependencies
pip install faker pandas duckdb

# 2. generate the synthetic dataset (writes CSVs to data/)
python scripts/generate_data.py

# 3. build the warehouse + run every analysis (DuckDB, no server)
python scripts/load_duckdb.py          # creates p2p.duckdb and prints results

# 4. open the dashboard & walkthrough
open index.html
```

**Prefer PostgreSQL?** The `sql/*.sql` files are Postgres-native:
```bash
createdb p2p && psql p2p -f sql/01_schema.sql
# load the CSVs with \copy, then:
psql p2p -f sql/02_marts.sql -f sql/03_analysis.sql
```

---

## 📊 The 10 analyses

| # | Question | Technique | Headline result |
|---|---|---|---|
| 1 | Invoice reconciliation scorecard | 3-way match view, window % | **76.2%** auto-matched; **$4.1M** exposure in exceptions |
| 2 | Spend Pareto by category | cumulative window sum | top 3 categories = majority of spend |
| 3 | Top-20 suppliers | `RANK()` | supplier concentration / tail spend |
| 4 | Maverick (off-catalog) spend | conditional aggregation | **21.4%** = **$51.3M** off-catalog |
| 5 | Supplier OTIF | on-time **and** in-full | worst suppliers surfaced for QBRs |
| 6 | P-card spend trend | time-series share | P-card = **11.7% of txns** but **<1%** of value |
| 7 | P-card policy violations | threshold filter | **106** txns over the $2,500 limit |
| 8 | Duplicate-invoice detection | self-group + `HAVING` | **288** duplicates, **$4.9M** at risk |
| 9 | Days Payable Outstanding (DPO) | date diff vs terms | NET-30 paid at ~36 days; **27%** late |
| 10 | Price-variance leakage trend | billed vs contract price | monthly leakage vs savings |

Full SQL with line-by-line commentary: [`sql/03_analysis.sql`](sql/03_analysis.sql) · interactive write-up: [`index.html`](index.html).

---

## 🧰 Tech stack (all free / open source)

- **PostgreSQL / DuckDB** — warehouse & SQL engine (DuckDB for zero-setup local runs)
- **Python · Faker · pandas** — synthetic data generation
- **SQL** — star schema, CTEs, window functions, analytical views ("marts")
- **Chart.js** — self-contained dashboard (works offline, deploys free on GitHub Pages)
- *(Optional extensions):* **dbt-core** for the transformation layer, **Metabase** for BI — both free.

---

## 🚀 Deploy it live

Static site — **no build, no server, no env vars.** Push to GitHub, import on [vercel.com/new](https://vercel.com/new), hit Deploy. Clean URLs are on, so `/dashboard` and `/sql-guide` just work. Full steps in [`DEPLOY.md`](DEPLOY.md).

```bash
git init && git add . && git commit -m "P2P Spend Intelligence"
git branch -M main
git remote add origin https://github.com/<you>/p2p-spend-intelligence.git
git push -u origin main
# then import the repo at vercel.com/new  →  live URL
```

---

## 💼 Résumé bullets (quantified)

> Label it a **personal / portfolio project on a synthetic dataset** — the numbers below are this project's real output.

1. **Built an end-to-end Procure-to-Pay spend-analytics platform** (PostgreSQL/DuckDB · Python · SQL) modelling **15,000+ PO lines, $240M spend, 400 suppliers**, with an **automated three-way match** that cleared **76%** of invoice lines hands-free and flagged **$4.1M** of price/quantity/no-receipt exposure for review.
2. **Engineered 10+ analytical SQL models** (window functions, CTEs, reconciliation views) that surfaced **21% ($51M) maverick/off-catalog spend**, **288 duplicate invoices ($4.9M at risk)**, and **106 P-card policy breaches** — an estimated **~$9M** of addressable spend-leakage and payment risk.
3. **Shipped a self-service spend dashboard** (Chart.js, deployable free on GitHub Pages) with supplier OTIF scorecards, DPO vs payment-terms tracking, and a P-card compliance view — replacing ad-hoc spreadsheet reporting with one refreshable source of truth.

---

## 🎯 How this maps to the JD (United — Associate Analyst, Global Procurement)

| JD requirement | Shown by |
|---|---|
| End-to-end invoice reconciliation, PO→payment | Three-way-match view (Q1), DPO (Q9) |
| Catalog maintenance | `dim_item` catalog flags, maverick analysis (Q4) |
| Supplier registration / onboarding | `dim_supplier` onboarding status, OTIF (Q5) |
| **P-card transactions** | P-card trend (Q6) + violations (Q7) |
| Large-dataset Excel / SQL | 15K+ rows, window functions, CTEs |
| Process improvement & automation | Reconciliation automated into one view |
| Coupa / Ariba / SAP vocabulary | Schema uses PO / GRN / three-way-match / GL terms |

---

*Synthetic data only — no confidential information. MIT licensed; fork freely.*
