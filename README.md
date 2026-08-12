# FASHIONIQ Sales Analytics

> An end-to-end sales analytics project using Python and Power BI to evaluate platform performance, product contribution, and order health across one year of Indonesian fashion retail transactions.

---

## Overview

FASHIONIQ is a multi-platform Indonesian fashion retailer selling across 5 platforms, 38 cities and a 20-product catalogue. This project takes one calendar year of raw transaction records (January–December 2025) through data understanding, cleaning, exploratory analysis, business insight and a Power BI dashboard.

Reporting already existed at the transaction level, but there was no evidence base for where commercial attention should go: platforms, cities, products and materials were all reported, with no established view of which dimensions actually behave differently. The analysis answers two questions the raw reporting could not — **which dimensions genuinely differentiate performance, and where does the largest operational exposure sit?** It deliberately stops at *what is happening* rather than *why*, because the dataset holds no cost, campaign, customer-identity or status-transition data.

---

## Business Questions

| # | Question |
|---|---|
| Q1 | How large is recorded sales activity, and does it move across the year? |
| Q2 | Which platforms contribute the most sales and order volume — is leadership volume-driven or basket-driven? |
| Q3 | Which products generate the most revenue, and are they the same products that move the most units? |
| Q4 | How healthy is the order pipeline — what share of orders completes, and what value sits outside completion? |
| Q5 | Is sales or operational concentration visible by platform, city, product or material? |
| Q6 | Does discounting behave as a basket-building lever? |
| Q7 | Does customer rating carry a usable operational signal? |

Questions the dataset cannot support — profitability, customer retention, marketing attribution, seasonality — were identified early and **excluded rather than answered with proxies**.

---

## Dataset

| Attribute | Value |
|---|---|
| Records (line items) | **15,000** |
| Columns | **20** |
| Distinct orders | **5,000** |
| Units sold | **26,453** |
| Recorded sales | **Rp6,975,804,400** |
| Sales AOV | **Rp1,395,161** |
| Time period | **January–December 2025** (365 distinct days) |

The data is at **order-item grain** — one row is one product line inside an order, not one order. Every order carries 2–4 lines, so order-level metrics are computed from a separate 5,000-row order view.

**Key dimensions:** `Product Name`, `Platform` (5), `Order Status` (5), `City` (38), `Material` (29), `Size`, `Gender` *(a product attribute, not a customer attribute)*, `Payment Method`, `Customer Rating`, plus eight financial fields (`Unit Price`, `Quantity`, `Discount (%)`, `Subtotal`, `Discount Amount`, `Total Sales`, `Shipping Fee`, `Grand Total`). `Category` is analyst-derived, not present in the source data.

> The term **"recorded sales"** is used throughout instead of "revenue", because the figure includes all order statuses.

---

## Analytical Workflow

```text
Data Understanding → Data Cleaning → EDA → Business Insights → Power BI Dashboard → Recommendations
```

| Stage | What happened |
|---|---|
| **Data Understanding** | Profiled the raw file for schema, missingness, duplicates, cardinality and grain — establishing that every column is either order-level or line-level before any metric was defined. |
| **Data Cleaning** | Parsed six Rupiah-formatted columns that pandas had silently misread (`185.000` → `185.0`), typed `Order Date` with an explicit format, and validated every financial identity — 0 rows deleted, 0 imputed. |
| **EDA** | Worked through the seven business questions with a stated metric, evidence and an explicit caveat about what each result does *not* support. |
| **Business Insights** | Converted EDA output into finalised findings with interpretation and business implication, correcting two figures carried over from EDA. |
| **Power BI Dashboard** | Built a five-page report on the cleaned dataset, including a validation page that proves its own totals. |
| **Recommendations** | Framed as *investigate / monitor / evaluate / track*, since the analysis is observational. |

---

## Tools & Technologies

**Data Analysis** — Python 3.12 · pandas · NumPy · Matplotlib · seaborn

**Business Intelligence** — Power BI · DAX (dedicated `_Measures` table)

**Development** — Jupyter Notebook

---

## Key Business Insights

### 1. Order health is the largest operational issue
**31.4% of orders (1,570)** never reach a `Completed` state, holding **Rp2.18bn** — 31.3% of recorded sales. This is the single highest-priority finding in the analysis.

### 2. Returned orders dominate the non-completed exposure
`Returned` is the largest non-completed status by both count (**694 orders**) and value (**Rp979M, 44.9%** of non-completed sales). `Returned` and `Cancelled` together account for 21.3% of all orders and Rp1.47bn.

### 3. Non-completion is broad-based, not concentrated in large orders
Completed and non-completed orders carry nearly identical average values (**≈Rp1.398M vs ≈Rp1.389M**). The exposure scales with order *count*, which is why completion is tracked as a count-based KPI rather than a value-at-risk figure.

### 4. Platform scale and platform quality are different dimensions
**Toko Fashion App** contributes **35.3%** of recorded sales and the highest order volume (1,753). **FashionKita** — third on both orders and sales — leads on AOV (**Rp1.41M**) and completion rate (**70.7%**, vs StyleMart's **68.3%**). Completion-rate ranking does not follow sales ranking.

### 5. Platform sales ranking merely restates order volume
Across the five platforms, `corr(order count, recorded sales)` = **1.000**, while AOV spans only **1.03×** end to end. A sales-by-platform chart adds nothing to a volume chart — a finding that directly shaped the dashboard layout.

### 6. Product revenue and product volume identify different products
The top 5 by sales value (**Gamis Rp619M**, Jaket Denim, Celana Jeans, Dress Formal, Jaket Bomber) share **no overlap** with the top 5 by units (**Kaos Pendek 1,882**, Hijab, Celana Pendek, Kemeja Casual, Kaos Panjang). Across the catalogue, sales track average unit price (r = **0.864**) rather than unit volume (r = **−0.565**).

**What the analysis ruled out:** geography does not differentiate performance (top city Jakarta holds 4.86% against a 2.63% even split, and ranks 34th of 38 on AOV), discounting shows no association with larger baskets, customer rating carries no usable signal (`corr(rating, Grand Total)` = 0.004), and there is no growth, decline or confirmable seasonal pattern in the year.

---

## Power BI Dashboard

`dashboard/FASHIONIQ.pbix` — five pages, each answering one question:

| Page | Purpose |
|---|---|
| **00 Data Validation** | Proves the dashboard's own arithmetic — integrity KPIs plus a status breakdown that must sum back to the headline totals. |
| **01 Executive Overview** | Scale at a glance, with `Completed Rate` sitting in the KPI row so sales are never read without it. |
| **02 Platform Performance** | Orders and sales placed side by side, making it self-evident that the two rankings are the same ranking. |
| **03 Product Performance** | Top 5 by sales and Top 5 by units placed adjacent, so the zero overlap is visible before any number is read. |
| **04 Order Health** | Completion rate and non-completed value shown together, by status, by platform and by product. |

> The `.pbix` file must be opened locally in Power BI Desktop — GitHub cannot render it. Four final dashboard screenshots are also included under `visuals/dashboard/` so the report can be reviewed directly from the repository. The DAX expressions are stored in a compressed model section and could not be inspected directly, so the report documents measures by name and by the fields they present.

---

## Project Structure

```text
fashion-sales-analytics-pipeline/
│
├── data/
│   ├── raw/fashion_sales.csv              # immutable source file
│   └── processed/fashion_sales_clean.csv  # cleaned, correctly typed
│
├── notebooks/
│   ├── 01_data_understanding.ipynb
│   ├── 02_data_cleaning.ipynb
│   ├── 03_eda.ipynb
│   └── 04_business_insights.ipynb
│
├── reports/
│   ├── business_insights.md               # finalised findings
│   └── final_report.md                    # full case study
│
├── dashboard/
│   └── FASHIONIQ.pbix                     # 5-page Power BI report
│
├── visuals/
│   └── dashboard/                         # final dashboard screenshots
│       ├── Executive Overview
│       ├── Platform Performance
│       ├── Product Performance
│       └── Order Health
│
└── README.md
```

- `data/` — `raw/` is treated as immutable; the cleaning notebook writes only to `processed/`
- `notebooks/` — the four-stage analytical workflow, in order
- `reports/` — business-facing findings and the full project narrative
- `dashboard/` — the Power BI report built on the cleaned dataset
- `visuals/dashboard/` — final screenshots of the four analytical dashboard pages for repository preview

Helper functions live inside the notebooks rather than in a separate module.

---

## Key Skills Demonstrated

- **Data cleaning** — locale-aware financial parsing, explicit date formats, type validation on round trip
- **Data validation** — financial identity checks (`Subtotal`, `Discount Amount`, `Total Sales`, `Grand Total`), re-asserted at every pipeline stage
- **Grain analysis** — separating order-level from line-item attributes before defining any metric
- **Exploratory data analysis** — business-motivated, with stated caveats on every result
- **KPI development** — fixed metric definitions applied consistently from notebook to dashboard
- **Power BI dashboard design & DAX** — five pages designed around the findings most likely to be misread
- **Business analysis** — interpretation and implication separated from finding
- **Statistical reasoning** — correlation reported with sample size and treated as directional, not robust

---

## Limitations

- **No `Customer ID`** — 395 customer names exist, but 391 appear across multiple cities, so retention, repeat purchase and lifetime value are not analysable.
- **No cost or margin data** — no product, platform or city can be described as "most profitable".
- **No marketing or campaign data** — no sales difference can be attributed to promotional activity.
- **Order status is a snapshot, not a lifecycle** — no status-transition timestamps, so `Pending` and `Shipped` orders cannot be classified as failures, and non-completed value is not treated as lost revenue.
- **Single year (Jan–Dec 2025)** — monthly movement is described as fluctuation, not seasonality; recurrence cannot be established from one year.
- **Observational data** — associations only; no experiment, control group or intervention log.
- **`Cotton` / `Katun` labelling unresolved** — the two values behave as distinct catalogue variants and were deliberately not merged, but whether they are the same fabric was never established.

---

## Further Analysis

Future opportunities, **not current findings** — ordered by how much they would change a decision:

1. **Platform × Order Status** (orders and sales) — the highest-value next step; would turn a flat 31.4% rate into something targetable, using data the project already holds.
2. **Product × Order Status for the full catalogue** — the current view is Top 5 by completion rate and excludes the top revenue product (Gamis).
3. **City × Order Status** — whether non-completion concentrates geographically is untested.
4. **Root-cause investigation of returns** — requires return reasons, fit or quality flags, none of which exist in the data.
5. **Customer-level and profitability analysis** — possible only if `Customer ID` and cost data become available.

---

## Dashboard Preview

The repository includes four final dashboard screenshots under `visuals/dashboard/`:

1. **Executive Overview** — overall sales scale, platform contribution, product contribution and order-status mix.
2. **Platform Performance** — order volume, recorded sales, sales distribution and AOV by platform.
3. **Product Performance** — revenue leaders, unit-volume leaders, material mix and product-level sales contribution.
4. **Order Health** — completion performance, non-completed exposure, platform completion rate and product completion rate.

The screenshots are provided as a static portfolio preview; the interactive `.pbix` remains the primary dashboard artifact.

---

## Detailed Documentation

- [Business Insights](reports/business_insights.md) — finalised findings, interpretations and implications
- [Final Report](reports/final_report.md) — full case study: methodology, evidence, dashboard design rationale, limitations
- [01 Data Understanding](notebooks/01_data_understanding.ipynb) · [02 Data Cleaning](notebooks/02_data_cleaning.ipynb) · [03 EDA](notebooks/03_eda.ipynb) · [04 Business Insights](notebooks/04_business_insights.ipynb)

---

## How to Explore the Project

1. **Start here** — this README for the 60-second view
2. **[reports/final_report.md](reports/final_report.md)** — the full case study, including why each decision was made
3. **[reports/business_insights.md](reports/business_insights.md)** — the business-facing findings in isolation
4. **[notebooks/](notebooks/)** — the analytical work itself, in numbered order
5. **`dashboard/FASHIONIQ.pbix`** — open in Power BI Desktop (Windows)

To run the notebooks locally:

```bash
pip install pandas numpy matplotlib seaborn jupyter
```

Notebooks were built on **Python 3.12**. Run them in order from `notebooks/`; `02_data_cleaning.ipynb` regenerates `data/processed/fashion_sales_clean.csv` from the raw file.
