# FASHIONIQ Sales Analytics

**Project:** FASHIONIQ — End-to-End Fashion Sales Analytics
**Scope:** 15,000 order-item records · 5,000 orders · 26,453 units · Rp6,975,804,400 recorded sales · Jan–Dec 2025
**Deliverables:** 4 analysis notebooks · 1 cleaned dataset · 1 business insights report · 1 Power BI dashboard (5 pages)

---

## 1. Executive Summary

FASHIONIQ is a multi-platform Indonesian fashion retailer selling across 5 platforms, 38 cities and a 20-product catalogue. This project takes one calendar year of raw transaction records (2025) through data understanding, cleaning, exploratory analysis, business insight and a Power BI dashboard, in order to answer a question the business could not answer from its raw reporting: **which dimensions actually differentiate performance, and where is the largest operational exposure?**

**Scale.** The dataset covers 5,000 orders containing 15,000 product lines, 26,453 units and Rp6,975,804,400 in recorded sales, at an AOV of Rp1,395,161. Sales, orders, units and AOV show relatively low month-to-month variation across the year (coefficient of variation between 1.9% and 4.7%), so this is not a growth or decline story.

**The most important finding is order health.** 31.4% of orders (1,570) never reach a `Completed` state, and those orders hold Rp2,181,031,200 — 31.3% of recorded sales. Returned orders are the single largest component (694 orders, Rp979,337,300, or 44.9% of non-completed sales value). Because average order value is nearly identical across statuses (~Rp1.39M non-completed vs ~Rp1.40M completed), the exposure is broad-based rather than concentrated in large transactions.

**Sales are concentrated in one platform, but scale and quality do not move together.** Toko Fashion App contributes 35.3% of recorded sales and the highest order volume (1,753 orders), while FashionKita — third on both orders and sales — leads on AOV (Rp1.41M) and completion rate (70.7%). Platform sales ranking follows order-count ranking exactly (r = 1.000 across the five platforms), while AOV varies by only 1.03× end to end.

**Product value and product volume identify different products.** The top 5 products by sales value (led by Gamis, Rp619M) share no overlap with the top 5 by units sold (led by Kaos Pendek, 1,882 units). Across the catalogue, recorded sales track average unit price (r = 0.864) rather than unit volume (r = −0.565).

**Business implication.** Two things follow directly. First, non-completion is the largest operational issue in the data and should be managed as a count-based KPI at platform and product level, with returns as the priority sub-category. Second, single "top platform" and "top product" rankings are misleading in this business: platform ranking is a volume statement, and product ranking depends entirely on whether the question is about revenue or about units. The dashboard was designed to make both of those distinctions visible on the page rather than leaving them to interpretation.

---

## 2. Business Understanding

### Business context

FASHIONIQ records sales through five distinct platforms (`Toko Fashion App`, `StyleMart`, `FashionKita`, `OutfitNow`, `FashionHub`), ships to 38 Indonesian cities, and sells 20 product types across 29 material variants. Reporting exists at the transaction level, but commercial attention has no evidence base for allocation: platforms, cities, products and materials are all reported, and there is no established view of which of those dimensions actually behaves differently from the others.

### Why this analysis is useful

Reporting effort and merchandising attention are finite. If sales differences between cities are within ordinary variation, a city-level plan spends effort on noise. If platform sales ranking merely restates platform order counts, a sales-by-platform chart adds nothing to a volume chart. Conversely, if a third of orders never complete, every unqualified sales figure in the business overstates realised activity by roughly a third. The purpose of the analysis is to separate those cases from each other.

### Analytical objective

Establish, with evidence, which dimensions differentiate performance; quantify order health; and specify a small set of metrics worth putting in front of stakeholders on a recurring basis. The project deliberately stops at *what is happening* and does not claim to explain *why*, because the dataset contains no cost, campaign, customer-identity or status-transition data.

### Key business questions

| # | Question | Answered in |
|---|---|---|
| Q1 | How large is recorded sales activity, and does it move across the year? | EDA §7 |
| Q2 | Which platforms contribute the most sales and order volume, and is leadership volume-driven or basket-driven? | EDA §9, §16.1 · Insight 2 |
| Q3 | Which products generate the most revenue, and are they the same products that move the most units? | EDA §11, §16.2 · Insight 1 |
| Q4 | How healthy is the order pipeline — what share of orders completes, and what value sits outside completion? | EDA §8 · Insight 3 |
| Q5 | Is sales or operational concentration visible by platform, city, product or material? | EDA §9–§12 |
| Q6 | Does discounting behave as a basket-building lever? | EDA §14 · Insight 4 |
| Q7 | Does customer rating carry a usable operational signal? | EDA §13 · Insight 5 |

Questions the dataset cannot support — profitability, customer retention, marketing attribution, seasonality — were identified early and excluded rather than answered with proxies. They are documented in §10.

---

## 3. Dataset Overview

**Source file:** `data/raw/fashion_sales.csv` — semicolon-delimited, UTF-8 with BOM, Indonesian Rupiah number formatting (`.` as thousands separator).
**Cleaned file:** `data/processed/fashion_sales_clean.csv` — same shape, corrected types.

| Property | Value |
|---|---|
| Rows (line items) | 15,000 |
| Columns | 20 |
| Distinct orders (`Order ID`) | 5,000 |
| Line items per order | 2, 3 or 4 (1,500 / 2,000 / 1,500 orders) |
| Units sold (`Quantity` sum) | 26,453 |
| Date coverage | 2025-01-01 to 2025-12-31 (365 distinct days, 12 months) |
| Platforms | 5 |
| Cities | 38 |
| Product types | 20 |
| Material variants | 29 |

### Field groups

| Group | Fields | Notes |
|---|---|---|
| Identifiers | `Order ID`, `Customer Name` | `Order ID` is the order key; no `Customer ID` exists |
| Time | `Order Date` | `dd/mm/yyyy HH:MM` in the raw file |
| Product | `Product Name`, `Size`, `Material`, `Gender` | `Gender` is a *product* attribute, not a customer attribute |
| Platform / channel | `Platform`, `Payment Method` | 5 and 4 distinct values |
| Order status | `Order Status` | `Completed`, `Returned`, `Cancelled`, `Shipped`, `Pending` |
| Financial | `Unit Price`, `Quantity`, `Discount (%)`, `Subtotal`, `Discount Amount`, `Total Sales`, `Shipping Fee`, `Grand Total` | Six Rupiah-formatted columns require locale-aware parsing |
| Service | `Customer Rating` | 1–5 integer scale, order-level |

### Transaction structure

The dataset is **order-item grain**: one row is one product line inside an order, not one order. Every order carries between two and four lines. `Total Sales` is a genuine per-line value and is summable at row level; `Shipping Fee` and `Grand Total` are order-level values repeated across each line of the order and must never be summed at row level. This distinction is enforced throughout the project by building a separate order-level view (`order_df`, 5,000 rows) for order-level metrics.

---

## 4. Data Understanding

`01_data_understanding.ipynb` profiled the raw file without modifying it: schema, data dictionary, missingness, duplicates, cardinality and logical consistency checks.

**Structure and quality baseline.** The raw file loads as 15,000 × 20 with **zero missing values**, **zero full-row duplicates** and **zero duplicates on the order-item key** (`Order ID` + `Product Name`). This baseline was re-asserted at the start of `02_data_cleaning.ipynb` and again in `03_eda.ipynb`, so any drift between stages would fail loudly rather than silently.

**Grain and attribute levels.** `03_eda.ipynb` §5.3 tested each column for whether it is constant within an `Order ID`. `Order Date`, `Customer Name`, `City`, `Payment Method`, `Order Status`, `Platform`, `Shipping Fee`, `Grand Total` and `Customer Rating` are constant within an order — they are order-level attributes. `Product Name`, `Unit Price`, `Quantity`, `Subtotal`, `Discount Amount`, `Total Sales`, `Size`, `Material` and `Gender` vary within an order and are genuinely line-level. `Customer Rating` sitting on every line while being an order-level attribute is the trap this test caught: treating it as a product-level signal would over-count multi-line orders.

**Financial parsing risk.** Dot-count profiling on the raw text revealed the parsing problem precisely, rather than as a vague warning:

| Column | 0 dots | 1 dot | 2 dots |
|---|---:|---:|---:|
| `Unit Price` | 0 | 15,000 | 0 |
| `Subtotal` | 0 | 14,478 | 522 |
| `Discount Amount` | 14,278 | 722 | 0 |
| `Total Sales` | 0 | 14,490 | 510 |
| `Shipping Fee` | 0 | 15,000 | 0 |
| `Grand Total` | 0 | 2,908 | 12,092 |

Columns whose values never cross Rp1,000,000 (one dot) were silently accepted by pandas as small floats — `185.000` read as `185.0`. Columns with a mix of one- and two-dot values could not be parsed at all and fell back to text for the entire column. Two different failure modes, one root cause.

**Identifier limitation.** There is no `Customer ID`. `Customer Name` has 395 distinct values, but 391 of those names appear across more than one city at order level, so names cannot be resolved to customers. This closed off customer-level analysis before it was attempted.

**Metric definitions** were fixed before any analysis, to prevent "sales" or "AOV" being redefined between sections:

| Metric | Definition | Grain |
|---|---|---|
| Units sold | `Quantity` sum | line item |
| Order count | distinct `Order ID` | order |
| Recorded sales | `Total Sales` sum | line item, summable |
| Sales AOV | recorded sales ÷ order count (excludes shipping) | order |
| Order-level Grand Total | mean of order-level `Grand Total` | order |

The term **"recorded sales"** is used throughout instead of "revenue", because the figure includes all order statuses. §6.4 quantifies why that label matters.

---

## 5. Data Cleaning

`02_data_cleaning.ipynb` treats `data/raw/` as immutable and writes only to `data/processed/`. The dataset had no missing values, no duplicates and no invalid rows, so no row was deleted, imputed or de-duplicated. The defects were **representational, not structural**, and the cleaning work was correspondingly about type correctness and value validity.

### 5.1 Financial columns (6)

All six Rupiah columns were parsed from their **raw string representation** through a single `parse_rupiah()` function (strip every `.`, then convert to numeric), deliberately bypassing pandas' automatic inference — including for the columns that already looked numeric, since `185.0` is silently wrong rather than merely untyped.

Result: **0 failed conversions** across all six columns. Spot check on row 0: `Unit Price` `"185.000"` → 185,000; `Grand Total` `"883.000"` → 883,000.

### 5.2 `Order Date`

All 15,000 rows were confirmed to match the `dd/mm/yyyy HH:MM` pattern before parsing, so an explicit format string (`%d/%m/%Y %H:%M`) could be used rather than automatic inference, which would have had to guess day/month order. Result: **0 `NaT` values**, range 2025-01-01 08:49 to 2025-12-31 17:23.

### 5.3 Categorical standardization — the `Katun` / `Cotton` decision

`Material` contains both `Katun` (Indonesian for cotton, 2,061 rows) and `Cotton` (251 rows). The obvious move — merging them — was investigated rather than applied, and the evidence rejected it:

- **Zero product overlap.** `Cotton` appears only on `Jaket Denim`; `Katun` appears across 8 entirely different products, and `Jaket Denim` is never labelled `Katun`.
- **Different price segment.** Mean `Unit Price` Rp431,048 (`Cotton`) vs Rp242,953 (`Katun`) — a ~77% gap.
- **Different demographic profile.** `Cotton` is 100% `Pria`; `Katun` splits 75.4% / 24.6% `Pria` / `Wanita`.
- **Structural confirmation.** Every one of the 20 products carries exactly 3 material variants, consistent with a controlled catalogue rather than free-text entry. `Dress Formal` similarly offers `Sutra` and `Silk Blend` as two coexisting variants.

**Decision: do not merge.** Collapsing them would conflate two price segments and two product lines. This is recorded as an unresolved labelling question rather than a resolved one (see §10), because the evidence shows they behave as distinct catalogue values without proving that they *are* different fabrics.

A wider sweep of `Gender`, `Size`, `Payment Method`, `Order Status`, `Platform` and `City` found clean, non-overlapping category sets and **no leading/trailing whitespace anywhere** in the text columns, so no further standardization was applied.

### 5.4 Validation — before / after evidence

| Check | Raw | After cleaning |
|---|---|---|
| Shape | 15,000 × 20 | 15,000 × 20 |
| Missing values | 0 | 0 |
| Full-row duplicates | 0 | 0 |
| Duplicate `Order ID` + `Product Name` | 0 | 0 |
| Financial columns numeric | 0 of 6 trustworthy | 6 of 6 (`int64`) |
| `Order Date` dtype | text | `datetime64` |
| `Subtotal` = `Unit Price` × `Quantity` | 0 mismatches | 0 mismatches |
| `Discount Amount` = round(`Subtotal` × `Discount (%)`) | 0 mismatches | 0 mismatches |
| `Total Sales` = `Subtotal` − `Discount Amount` | 0 mismatches | 0 mismatches |
| `Grand Total` = Σ`Total Sales` + `Shipping Fee` (per order) | — | 0 mismatches |

Financial columns were cast to `int64` only after confirming every value was already a whole number, and the exported file was reloaded and re-checked to confirm dtypes survived the round trip. Cleaning changed representation, not content.

---

## 6. Exploratory Data Analysis

`03_eda.ipynb` works through business-motivated questions, each with a stated metric, evidence, interpretation and an explicit caveat about what the evidence does not support.

### 6.1 Overall sales and order performance

| Metric | Value |
|---|---|
| Line items | 15,000 |
| Orders | 5,000 |
| Units sold | 26,453 |
| Recorded sales | Rp6,975,804,400 |
| Sales AOV (excl. shipping) | Rp1,395,161 |
| Order-level Grand Total AOV | Rp1,418,186 |
| Line items per order | 3.00 |
| Units per order | 5.29 |

Four time series were tested at monthly granularity — recorded sales (CoV 4.7%), orders (4.5%), units, and AOV (1.9%). All four are flat. Sales range from Rp542.2M (September) to Rp619.2M (October), a 14.2% high-to-low spread; orders range from 388 to 456; units per order stays between 5.15 and 5.41 in every month.

**Reading:** no growth story, no decline story, and — with a single year of data — **no confirmed seasonality**. The notebook describes monthly movement as *fluctuation*, not seasonality, because establishing recurrence would require comparable months from at least one other year. Basket-size shift was ruled out as a mechanism, but no alternative mechanism was identified; below 5% CoV the most defensible reading is ordinary noise.

### 6.2 Platform performance

| Platform | Orders | Order share | Recorded sales | Sales share | Sales AOV | Units/order |
|---|---:|---:|---:|---:|---:|---:|
| Toko Fashion App | 1,753 | 35.1% | Rp2,459,498,600 | 35.3% | Rp1,403,023 | 5.36 |
| StyleMart | 1,134 | 22.7% | Rp1,550,422,300 | 22.2% | Rp1,367,215 | 5.18 |
| FashionKita | 1,000 | 20.0% | Rp1,414,191,200 | 20.3% | Rp1,414,191 | 5.34 |
| OutfitNow | 658 | 13.2% | Rp923,708,300 | 13.2% | Rp1,403,812 | 5.32 |
| FashionHub | 455 | 9.1% | Rp627,984,000 | 9.0% | Rp1,380,185 | 5.16 |

Recorded sales span a 3.92× range across platforms; order counts span 3.85×; **AOV spans 1.03×**. Correlation across the five platforms: `corr(order count, recorded sales)` = **1.000**, `corr(AOV, recorded sales)` = 0.218. The sales ranking and the order-count ranking are the same ranking.

> **Correction carried from EDA to Insights.** The platform table in `03_eda.ipynb` §9 reported `avg_units_per_order` as `NaN` — a Series/DataFrame index-alignment error. `04_business_insights.ipynb` recomputed the column correctly (5.16–5.36) and flagged the correction explicitly. All other figures in that table matched.

### 6.3 Product performance

**Top 5 by recorded sales:** Gamis Rp619,178,000 (8.88%) · Jaket Denim Rp478,830,300 · Celana Jeans Rp477,506,000 · Dress Formal Rp427,203,100 · Jaket Bomber Rp406,594,800.

**Top 5 by units sold:** Kaos Pendek 1,882 · Hijab 1,873 · Celana Pendek 1,857 · Kemeja Casual 1,549 · Kaos Panjang 1,547.

Only **2 of the top 8 by sales** (Blouse, Kemeja Casual) also appear in the top 8 by units. Across all 20 products, `corr(avg unit price, recorded sales)` = **0.864** while `corr(quantity, recorded sales)` = **−0.565**: the products moving the most units are systematically the cheaper ones, and their volume does not compensate for their price point. Concentration is mild — top 5 = 34.5% and top 10 = 60.9% of recorded sales, against 25% and 50% under an even split across 20 products.

### 6.4 Order status and order health

| Status | Orders | % of orders | Recorded sales | % of sales | Avg Grand Total |
|---|---:|---:|---:|---:|---:|
| Completed | 3,430 | 68.6% | Rp4,794,773,200 | 68.7% | Rp1,420,918 |
| Returned | 694 | 13.9% | Rp979,337,300 | 14.0% | Rp1,434,150 |
| Cancelled | 371 | 7.4% | Rp488,457,500 | 7.0% | Rp1,339,425 |
| Shipped | 263 | 5.3% | Rp362,525,900 | 5.2% | Rp1,401,505 |
| Pending | 242 | 4.8% | Rp350,710,500 | 5.0% | Rp1,472,556 |

Sales share tracks order share almost exactly at every status, and average order value spans only 9.9% top to bottom across the five statuses. Grouped: **Completed 3,430 orders / Rp4,794,773,200 (68.7%)** vs **non-completed 1,570 orders / Rp2,181,031,200 (31.3%)**.

The notebook is explicit that this is a **snapshot, not a lifecycle**: there are no status-transition timestamps, so `Pending` and `Shipped` orders cannot be classified as failures, and non-completed value is not treated as "lost revenue".

### 6.5 Discounting

`Discount (%)` takes only **two values in the entire dataset: 0 and 10**. 4.8% of line items (722 of 15,000) and 4.8% of orders (241 of 5,000) carry any discount; 95.2% of orders sit at a zero order-level discount rate.

Discounted orders average a Grand Total of **Rp1,292,861** against **Rp1,424,532** for undiscounted orders — 9.2% *lower*, not higher. Order-level correlations: discount rate vs units per order = −0.007, vs Grand Total = −0.050 (both negligible).

> **Conflicting evidence, resolved.** `03_eda.ipynb` §16.3 reported a moderate positive correlation (0.453) between average discount rate and AOV *across the five platforms*. `04_business_insights.ipynb` relies on the order-level result (5,000 observations) over the platform-level one (5 aggregated points) and does not treat the platform figure as evidence of a discount–AOV relationship.

### 6.6 Other investigated dimensions

- **Geography.** Sales spread thinly across 38 cities: the top city (Jakarta) holds 4.86% against a 2.63% even split; top 3 = 13.1% (even split 7.9%); top 10 = 38.4% (even split 26.3%). Jakarta leads on orders (252, against a 38-city mean of 132) but ranks **34th of 38 on AOV**. At ~132 orders per city per year, city-level AOV differences of a few percent are not distinguishable from ordinary variation.
- **Category, material, gender.** `Category` does not exist in the source data; it was **derived by the analyst** by mapping the 20 products into 5 groups (Tops 39.6%, Bottoms 25.3%, Dresses 19.9%, Outerwear 12.7%, Accessories 2.5%). The strong Category × Gender skew (4 of 5 categories above 65/35) is a property of that taxonomy — Dresses are 100% `Wanita` and Outerwear 100% `Pria` by construction — and says nothing about customers, because `Gender` is a product attribute.
- **Customer rating.** `corr(rating, Grand Total)` = **0.004**. Average order value spans 1.1% across the five rating levels. Platform averages run 2.815–3.073 (spread 0.258 on a 1–5 scale); status averages 2.916–3.091, with `Returned` orders rated only 0.056 below `Completed`. Mean rating 2.97, with 59.4% of orders rated 3 or lower — reported without interpretation, since there is no benchmark, prior period or feedback field to interpret it against.
- **Order economics.** The top 10% of orders by value hold 17.6% of total order value (10% under a perfectly even distribution), so there is no high-value cohort carrying the business. Every order carries 2–4 line items at 1.76 units per line; shipping fees run Rp17,000–27,000 (mean Rp23,025).

---

## 7. Business Insights

This section reflects the finalised conclusions in `reports/business_insights.md`, which were written against the completed Power BI dashboard.

### 7.1 Executive findings

- **Order health is the largest operational issue:** 31.4% of orders (1,570) are non-completed, representing Rp2.18bn in recorded sales.
- **Sales are concentrated:** Toko Fashion App contributes 35.3% of total sales, while FashionKita leads on AOV (Rp1.41M) and completion rate (70.7%) despite a smaller order base.
- **Product value and volume tell different stories:** Gamis leads by sales value (Rp619M) while Kaos Pendek, Hijab and Celana Pendek lead by units sold, with no overlap between the two rankings.
- **Returned orders are the largest non-completed category:** Rp979M, or 44.9% of non-completed sales value.
- **Non-completion is broad-based by order value:** completed and non-completed orders carry nearly identical average values (~Rp1.40M vs ~Rp1.39M).

### 7.2 Platform performance

**Finding.** Toko Fashion App generates the highest order volume (1,753 orders) and the highest sales value (Rp2,459M, 35.3%), roughly 1.6× the next-ranked platform, StyleMart (Rp1,550M, 22.2%). Its AOV (Rp1.40M) and completion rate (68.6%) are close to the overall average and are both surpassed by FashionKita (1,000 orders, AOV Rp1.41M, completion rate 70.7%). StyleMart holds the second-highest order count (1,134) but the **lowest AOV of all five platforms** (Rp1.37M). Completion rates span 66.1%–70.7%, and their ranking (FashionKita > Toko Fashion App > StyleMart > OutfitNow > FashionHub) does not follow the sales ranking (Toko Fashion App > StyleMart > FashionKita > OutfitNow > FashionHub).

**Interpretation.** Scale (orders, sales) and quality (AOV, completion rate) track different dynamics. The largest platform by volume is not the platform with the best per-order economics or fulfilment reliability. This does not indicate that Toko Fashion App is underperforming — it indicates that its results are driven primarily by transaction count rather than transaction quality. Completion rate behaves as an independent dimension rather than a byproduct of scale.

**Business implication.** Because Toko Fashion App carries over a third of total revenue, any completion-rate issue on that platform has an outsized effect on total performance. Platform-level completion rate should be tracked as its own KPI, separate from sales and order targets. FashionKita is a template worth investigating: if the drivers behind its AOV and completion-rate advantage can be identified, they may be transferable to higher-volume platforms, where the same gain would be worth more in absolute terms.

> **Reconciliation note.** The completion-rate figures above are the finalised dashboard values and are retained as the business-facing source. Recomputing completion rate directly from the cleaned dataset at order grain (distinct completed orders ÷ distinct orders) gives 65.9%–70.9% with the **same platform ranking**; small differences are consistent with the grain used inside the DAX measure. The measure definitions inside `FASHIONIQ.pbix` could not be inspected directly (the model is stored compressed), so this is noted rather than resolved.

### 7.3 Product performance

**Finding.** The top 5 products by sales value (Gamis Rp619M, Jaket Denim Rp479M, Celana Jeans Rp478M, Dress Formal Rp427M, Jaket Bomber Rp407M) share **no overlap** with the top 5 by units sold (Kaos Pendek 1,882, Hijab 1,873, Celana Pendek 1,857, Kemeja Casual 1,549, Kaos Panjang 1,547). Gamis converts 1,191 units into Rp619M (≈Rp520K per unit) while Kemeja Casual converts a higher unit count (1,549) into Rp375M (≈Rp242K per unit). Dress Formal ranks #4 by sales on only 716 units — the lowest unit count in the top 11 — at an implied ≈Rp597K per unit, roughly double most other top-11 products (mostly Rp240K–430K). By comparison, Rok Midi ranks #6 (Rp385.0M) on 1,375 units.

**Interpretation.** Two product tiers operate side by side: a lower-volume, higher price-per-unit tier that drives the sales-value ranking, and a higher-volume, lower price-per-unit tier that drives the units ranking. Dress Formal is the clearest premium-priced outlier, reaching a top sales position through unit price rather than sales frequency.

**Business implication.** Sales-value ranking alone understates which products actually move through the business at high frequency. Category and inventory planning should track both leaderboards together, since a high-volume, lower-value product (Kaos Pendek, Hijab) warrants different stocking and pricing decisions than a low-volume, high-value one (Gamis). Dress Formal's contribution appears more dependent on its higher unit price than on sales frequency, so it should be monitored separately from high-frequency products

**Material concentration.** Within the Top 8 materials shown in the dashboard, Katun accounts for 24.6% of orders and Polyester 20.9%, making them the two largest material groups in the displayed distribution. With the remaining six materials (`Satin`, `Twill`, `Jersey`, `Cotton Combed`, `Linen`, `Chambray`) each between roughly 6.7% and 10.7%. Demand is concentrated but not dominated by a single fabric. These two materials warrant closer monitoring in assortment and inventory planning; the view reflects order distribution only and contains no supplier, cost or lead-time data, so **sourcing or procurement risk cannot be assessed from it**.

> *Precision note:* the material view is the dashboard donut, which is filtered to the top 8 materials by units sold, and `Material` is a line-item attribute. The percentages therefore represent each material's share of orders within that top-8 view, not of the 5,000 distinct orders.

### 7.4 Order health

**Finding.** Of 5,000 orders, 3,430 (68.6%) are `Completed` and 1,570 (31.4%) are non-completed: `Returned` 694 (13.9%), `Cancelled` 371 (7.4%), `Shipped` 263 (5.3%), `Pending` 242 (4.8%). In sales terms, completed orders represent Rp4,794,773,200 (68.7%) and non-completed orders Rp2,181,031,200 (31.3%). `Returned` alone carries Rp979,337,300 — **44.9% of non-completed sales value**, against 44.2% of non-completed order volume. Implied AOV is ≈Rp1.398M for completed orders and ≈Rp1.389M for non-completed ones.

**Interpretation.** Just under a third of order volume and sales value sits outside a completed state. `Shipped` and `Pending` orders may still convert as they progress; `Returned` and `Cancelled` are currently recorded in states that do not indicate completion, while `Shipped` and `Pending` may still convert. Returns are both the most frequent non-completed status and a proportionally large share of the associated revenue exposure. Non-completion is not concentrated among unusually high- or low-value orders, the exposure scales with order count.

**Business implication.** Completed-order rate is a primary health metric. Because `Returned` and `Cancelled` orders alone represent 21.3% of all orders and Rp1.47bn in sales value, understanding the drivers behind those two statuses specifically is the more urgent operational priority. Since exposure is broad-based, completion-rate improvements should be projected using the overall AOV rather than weighted toward a particular order-value segment.

### 7.5 Cross-dashboard insights

- **The largest platform and the most efficient platform are not the same platform.** Toko Fashion App leads orders and sales (Platform Performance page); FashionKita leads AOV and completion rate (Platform Performance + Order Health). Viewed alone, the Platform Performance page suggests Toko Fashion App is the standout; only cross-referencing completion rate reveals that a smaller platform outperforms it on reliability and per-order value. A platform scorecard combining sales share, AOV and completion rate gives a more complete view than any single page.
- **High-value and high-volume product leadership are two separate stories.** Gamis leads sales value while ranking outside the top 5 by units; Kaos Pendek, Hijab and Celana Pendek lead units without reaching the top sales tier. Neither ranking alone reveals the pattern, only viewing them side by side does.
- **Order-volume concentration does not translate into proportional revenue efficiency.** StyleMart holds the second-highest order count (1,134) but the lowest AOV (Rp1.37M), while FashionKita, with fewer orders (1,000), achieves a higher AOV (Rp1.41M) and a meaningfully higher completion rate (70.7% vs 68.3%). Growth strategies for the two should therefore differ.

---

## 8. Power BI Dashboard

The finalised dashboard (`dashboard/FASHIONIQ.pbix`) is built on the cleaned dataset with a dedicated `_Measures` table and is organised as five pages, each answering one question. Page and visual descriptions below are taken from the report layout inside the `.pbix` file; the DAX expressions themselves are stored in a compressed model section and could not be read directly, so measures are described by name and by the fields they present.

### 8.1 Data Validation (`00_Validation`)

**Question:** can the numbers on the following four pages be trusted?

This page exists because the dashboard is the last artifact in a four-stage pipeline, and a stakeholder has no way to see the notebook assertions that guarded each stage. It carries integrity KPIs — `Data Integrity Rate`, `Invalid Records`, `Missing Critical Fields`, `Total Records` — alongside a second card row that restates the headline figures under a `Validation` prefix (`Validation Orders`, `Validation Units`, `Validation Sales`, `Validation AOV`, `Validation Status Total`).

A reconciliation table then presents the full core measure set side by side: `Orders`, `Units Sold`, `Recorded Sales`, `AOV`, `Completed Orders`, `Completed Rate`, `Completed Sales`, `Non-Completed Orders`, `Non-Completed Rate`, `Non-Completed Sales`, with a second table breaking `Orders` and `Recorded Sales` down by `Order Status`. The analytical purpose is that the status breakdown must sum back to the totals — the dashboard proves its own arithmetic on the page rather than asking the reader to assume it.

### 8.2 Executive Overview (`01_Executive Overview`)

**Question:** how big is the business, and where does it sit at a glance?

**KPI row:** `Recorded Sales`, `Orders`, `Units Sold`, `AOV`, `Completed Rate`.

**Visuals:** recorded sales by `Platform` (bar), top 8 products by recorded sales (bar, Top-N filtered by `Recorded Sales`), and orders by `Order Status` (column).

The design choice that matters here is the inclusion of `Completed Rate` in the executive KPI row. The EDA established that recorded sales overstate realised activity by roughly a third, so an executive page that showed sales without completion rate would misrepresent the business on its most-viewed screen. Placing the status column chart beneath the sales charts makes the same point visually: the sales bars and the status distribution are read together, not separately.

### 8.3 Platform Performance (`02_Platform Performance`)

**Question:** which channels carry the business, and is their position volume-driven or value-driven?

**KPI row:** `Recorded Sales`, `Orders`, `AOV`.

**Visuals:** *Orders by Platform* (bar) and *Sales by Platform* (bar) placed side by side; *Sales Distribution by Platform* (donut); AOV by `Platform` (column).

The paired orders/sales bars are the point of the page: because the two rankings are identical (r = 1.000), seeing them adjacent makes it self-evident that the sales chart restates the volume chart. The donut is included specifically to express **concentration** — a single platform holding roughly a third of sales reads more directly as a share of a whole than as a bar. The AOV column chart is deliberately separated from the sales visuals so that its narrow 1.03× range is read as a stability check rather than as a ranking.

### 8.4 Product Performance (`03_Product Performance`)

**Question:** which products generate revenue, which move units, and are they the same products?

**KPI row:** `Recorded Sales`, `Units Sold`, `AOV`.

**Visuals:** *Top 5 Products by Sales* (bar, Top-N by `Recorded Sales`) and *Top 5 Sold Products* (bar, Top-N by `Units Sold`) side by side; orders by `Material` (donut, top 8 materials); a scatter of `Units Sold` (X) against `Recorded Sales` (Y) by `Product Name`; and a ranked table showing `Sales Rank`, `Sales Share`, `Product Name`, `Units Sold` and `Recorded Sales` together.

This page is designed around the single most misreadable finding in the project. The two Top-5 bars are placed adjacent precisely because they contain no products in common — a viewer sees the divergence before reading a number. The scatter generalises the same point to the full catalogue: products spread along the X axis (volume) do not order themselves along the Y axis (sales). The table then makes the pairing explicit at row level, so a merchandising decision can read revenue share and unit movement for the same product in one place.

### 8.5 Order Health (`04_Order Health`)

**Question:** how much of the recorded activity actually completes, and where is the exposure?

**KPI row:** `Completed Orders`, `Completed Rate`, `Completed Sales`, `Non-Completed Sales`, `Non-Completed Rate`.

**Visuals:** *Total Orders by Status* (bar) and recorded sales by `Order Status` (bar); `Completed Rate` by `Platform` (column); *Top 5 Products by Completion Rate* (column, Top-N by `Completed Rate`); and a table of `Order Status` with `Orders` and `Recorded Sales`.

The KPI row is built to prevent the two framings of the same problem from being separated: a count rate and a value figure sit next to each other, so neither can be quoted alone. The status bars in count and in value are paired for the same reason, and their near-identical shape is itself the evidence for the "non-completion is value-neutral" conclusion. The completion-rate-by-platform column chart is the visual that supplies the platform quality dimension the Platform Performance page deliberately leaves out, which is what makes the cross-page insight in §7.5 possible. The Top 5 Products by Completion Rate view has a known blind spot: it is a Top-N view, so it does not include the top revenue product (Gamis), and therefore cannot confirm or rule out a completion-rate risk there — a limitation carried forward to §11.

---

## 9. Business Recommendations

These are the finalised recommendations from `reports/business_insights.md`. Each traces to a finding above. Because the analysis is observational and the dataset contains no cost, campaign or customer data, they are framed as *investigate*, *monitor*, *evaluate* and *track* rather than as instructions to change price, discount level, product range or channel strategy.

### 9.1 Platform strategy

- **Investigate the drivers behind Toko Fashion App's sales concentration** by comparing order frequency, AOV and completion rate against FashionKita, to determine whether FashionKita's efficiency advantages (higher AOV, higher completion rate) can be replicated on higher-volume platforms. *(→ §7.2, §7.5)*
- **Evaluate order-value levers for StyleMart** such as bundling or premium product placement, given its strong order volume paired with the lowest AOV of any platform. *(→ §7.2)*
- **Build a combined platform scorecard** (sales share, AOV, completion rate) rather than relying on sales ranking alone, since these metrics demonstrably do not move together. *(→ §7.5)*

### 9.2 Product strategy

- **Maintain separate visibility into sales-value and unit-volume rankings** when making assortment and promotion decisions, since the two leaderboards currently identify different products. *(→ §7.3)*
- **Review pricing and demand data for Dress Formal specifically**, given its unusually high implied price per unit relative to other top-11 products, to confirm whether this reflects intentional premium positioning. *(→ §7.3)*
- **Monitor `Katun` and `Polyester` more closely** in assortment and inventory planning given their combined 45.5% share of observed order volume, treating the remaining six materials as a more evenly distributed secondary group. This reflects order-volume patterns only; sourcing, cost or supply-chain conclusions would require data not present in the dashboard. *(→ §7.3)*

### 9.3 Order / operational management

- **Treat return reduction as the top operational priority within order health**, since `Returned` orders represent both the largest non-completed order count (694) and the largest non-completed revenue exposure (Rp979M, 44.9% of non-completed sales). *(→ §7.4)*
- **Establish a routine review of `Cancelled` orders (371 orders, Rp488M) separately from `Shipped`/`Pending` orders**, since cancelled and returned orders have already exited the pipeline while shipped and pending orders may still convert. *(→ §7.4)*
- **Track completion rate by platform and by product as an ongoing KPI**, given the spread already observed (66.1%–70.7% by platform; 69.6%–70.1% among the top completion-rate products). *(→ §7.2, §8.5)*
- **Report recorded sales with a completed-only figure alongside it**, and monitor non-completion as an order-count rate rather than a value-at-risk figure, since order value is essentially uniform across statuses. *(→ §6.4, §7.4)*

### 9.4 Further analysis

- **Obtain a platform-by-order-status cross-tabulation** (orders and sales) to confirm which platform carries the largest absolute non-completed revenue exposure — the current dashboards support this only as an inference from combined figures, not as a direct measurement.
- **Obtain completion rate for all top sales-value products** (particularly Gamis), since the current Top 5 by Completion Rate view does not include the top revenue product and cannot confirm or rule out a completion-rate risk there.
- **Layer customer, cost or return-reason data onto this analysis** if it becomes available, to move from *what is happening* — which this project establishes — toward *why it is happening*.

---

## 10. Limitations

These constraints bear directly on the conclusions above and were established during the project, not appended as disclaimers.

- **No `Customer ID`.** 395 distinct `Customer Name` values exist, but 391 of them appear across more than one city, so names cannot be resolved to customers. **Retention, repeat-purchase rate, purchase frequency and customer lifetime value are not analysable in this project**, and no recommendation depends on them.
- **No cost or margin data.** `Total Sales` and `Grand Total` are revenue measures, not profit. **No product, platform or city in this project can be described as "most profitable"**, which is why the strong price–sales correlation (r = 0.864) supports no pricing action.
- **No marketing, campaign or promotion data.** There is no campaign identifier, spend figure or promotion calendar, so no sales difference can be attributed to promotional activity — including the 241 discounted orders, whose discounts have no recorded cause.
- **No demographic or geographic context.** The dataset carries city names and nothing else about those cities: no population, income or coverage data. City differences therefore **cannot be attributed to purchasing power, market maturity or customer preference**. This is the primary reason geography was excluded from the prioritised insights.
- **Material labels remain unresolved.** `Cotton` (2.3% of sales) and `Katun` (13.0%) exist as separate values. The cleaning stage established that they behave as distinct catalogue variants and deliberately did **not** merge them, but whether they represent the same fabric was never resolved, so no material-level conclusion is drawn beyond order distribution.
- **Order status is a snapshot, not a lifecycle.** There are no status-transition timestamps, so it is not possible to tell whether `Pending` and `Shipped` orders (10.1% of orders) will convert to `Completed` or `Cancelled`. The 31.4% figure should be read with that in mind, and non-completed value is not treated as lost revenue.
- **Single year — no seasonality.** Coverage is exactly Jan–Dec 2025. Monthly sales, orders, units and AOV are all flat (CoV 1.9%–4.7%), but recurring seasonal patterns cannot be established from one year at all.
- **`Category` is analyst-derived; `Gender` is a product attribute.** `Category` does not exist in the source data and was built by mapping 20 products into 5 groups. The strong Category × Gender skew is a property of that taxonomy, not a customer-demographic finding.
- **Observational data — associations only.** There is no experiment, control group or intervention log. In particular, the discount finding cannot separate a discount effect from a selection effect, and the platform finding cannot establish whether volume produces sales or both follow from something unobserved.
- **Basket structure is unusually uniform.** Every order carries exactly 2, 3 or 4 line items, `Discount (%)` takes only two values (0 and 10), and the top 10% of orders by value hold only 17.6% of total value. There is no long tail of very small or very large orders, which limits what basket-expansion analysis this dataset can support.
- **Small n on aggregate correlations.** Platform correlations are computed across 5 points and product correlations across 20. They are reported with their coefficients and treated as directional descriptions, not statistically robust estimates.
- **Some cross-dimensional questions were not tested.** Platform × Order Status and City × Order Status cross-tabulations (orders and sales) were not produced in the notebooks; the dashboard supplies completion rate by platform and by product, but not non-completed sales by platform or city.

---

## 11. Further Analysis

These are future analytical opportunities, **not established findings**. They are ordered by how much they would change a decision.

1. **Platform × Order Status (orders and sales).** The highest-value next analysis. Completion rate by platform is available on the dashboard, but non-completed *sales* by platform is not, so the question of which platform carries the largest absolute revenue exposure is currently an inference rather than a measurement. Answering it would turn a flat 31.4% rate into something targetable.
2. **Product × Order Status for the full catalogue.** The current view is Top 5 by completion rate and excludes the top revenue product (Gamis), so a completion-rate risk on the highest-revenue item can neither be confirmed nor ruled out.
3. **City × Order Status.** Whether non-completion concentrates geographically is untested. Given ~132 orders per city per year, this would need to be read with sample-size caution.
4. **Root-cause investigation of non-completion**, particularly returns. This requires data the project does not have — return reasons, fit or quality flags, delivery outcomes — and is the point at which the analysis would move from *what* to *why*.
5. **The 241 discounted orders as a distinct cohort.** With a single 10% discount level applied to under 5% of orders, the current programme does not contain the variation needed to test a discount–basket relationship. Establishing what those orders share, and on what basis the discount was applied, would come before any change to discounting.
6. **Customer-level analysis if a `Customer ID` becomes available** — retention, repeat purchase and lifetime value, none of which are currently possible.
7. **Profitability analysis if cost data becomes available** — the point at which "highest revenue" could become "most valuable".
8. **Resolving the `Cotton` / `Katun` labelling** with catalogue or supplier reference data, which would be a prerequisite for using material mix to explain platform or city differences.
9. **A second year of transactions**, without which seasonality cannot be tested at all.

---

## 12. Conclusion

This project established a validated view of one year of FASHIONIQ transaction activity, from a raw file whose six financial columns were unusable as loaded through to a five-page dashboard with an integrity page that proves its own totals. The analytical value lies less in the size of the business — Rp6,975,804,400 across 5,000 orders and 26,453 units — than in what the analysis ruled out: geography does not differentiate performance, discounting shows no association with larger baskets, customer rating carries no usable signal about transaction economics, and there is no growth, decline or confirmable seasonal pattern in the year.

**The most important business issue is order health.** 31.4% of orders and Rp2.18bn in recorded sales sit outside a completed state, with returned orders accounting for 44.9% of that exposure. Because average order value is nearly identical across statuses, this is a volume and process problem rather than a high-value-order problem, and it is measurable today as a count-based KPI at platform and product level.

**The dashboard lets stakeholders monitor exactly that**, alongside the two distinctions the analysis showed are easy to get wrong: that platform sales leadership is a volume statement rather than a quality one, and that the top-revenue product list and the top-units product list identify different products. Sales figures never appear on the dashboard without a completion-rate figure beside them.

**The logical next step is a platform-by-order-status cross-tabulation.** It is the smallest piece of additional analysis that would convert a flat, business-wide 31.4% non-completion rate into a targeted operational priority — and it requires no data the project does not already hold.
