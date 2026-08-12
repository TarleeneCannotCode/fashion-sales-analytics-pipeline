# Business Insights

**Project:** FASHIONIQ Sales Analytics
**Scope:** 5,000 validated orders · 26,453 units sold · Rp6,975,804,400 in recorded sales · AOV Rp1,395,161

---

## 1. Executive Summary

- **Order health is the largest operational issue:** 31.4% of orders (1,570) are non-completed, representing Rp2.18bn in recorded sales.
- **Sales are concentrated:** Toko Fashion App contributes 35.3% of total sales, while FashionKita leads on AOV (Rp1.41M) and completion rate (70.7%) despite a smaller order base.
- **Product value and volume tell different stories:** Gamis leads by sales value (Rp619M) while Kaos Pendek, Hijab, and Celana Pendek lead by units sold, with no overlap between the two rankings.
- **Returned orders are the largest non-completed category:** Rp979M, representing 44.9% of non-completed sales value.
- **Non-completion is broad-based by order value:** Completed and non-completed orders carry nearly identical average values (~Rp1.40M vs ~Rp1.39M), indicating the exposure is not concentrated in a specific value segment.

---

## 2. Platform Performance

### Insight: Sales concentration does not equal platform quality

**Finding**
Toko Fashion App generates the highest order volume (1,753 orders) and the highest sales value (Rp2,459M, 35.3% of total sales), roughly 1.6x the sales of the next-ranked platform, StyleMart (Rp1,550M, 22.2%). However, Toko Fashion App's AOV (Rp1.40M) and completion rate (68.6%) are close to the overall average and are both surpassed by FashionKita, a platform with far fewer orders (1,000).

**Interpretation**
Scale (orders and sales) and quality (AOV and completion rate) are tracking different dynamics across platforms. The largest platform by volume is not the platform with the best per-order economics or fulfillment reliability. This does not indicate that Toko Fashion App is underperforming — it indicates that its results are driven primarily by transaction count rather than transaction quality.

**Business Implication**
Because Toko Fashion App carries over a third of total revenue, any operational or completion-rate issue on this platform has an outsized effect on total business performance. Understanding what drives its volume (catalog breadth, order frequency, or customer base size) versus what drives FashionKita's higher completion rate would clarify whether the two platforms should be managed with different playbooks.

---

### Insight: FashionKita is the efficiency leader despite smaller scale

**Finding**
FashionKita ranks third in both orders (1,000) and sales (Rp1,414M, 20.3% share), yet it records the highest AOV of all platforms (Rp1.41M) and the highest completion rate (70.7%, versus a 68.6% overall average).

**Interpretation**
FashionKita converts a comparatively small order base into the strongest per-order value and the most reliable fulfillment outcome in the portfolio. The dashboard does not indicate why this occurs — whether from a different product mix, customer segment, or platform-specific operating process — but the pattern is consistent across both metrics.

**Business Implication**
FashionKita represents a template worth investigating: if the drivers behind its AOV and completion-rate advantage can be identified, they may be transferable to higher-volume platforms like Toko Fashion App or StyleMart, where similar gains would have a larger absolute revenue impact.

---

### Insight: StyleMart's sales are volume-driven, not value-driven

**Finding**
StyleMart holds the second-highest order count (1,134) and second-highest sales (Rp1,550M), but its AOV (Rp1.37M) is the lowest among all five platforms — below FashionHub (Rp1.38M), which has less than half StyleMart's order count.

**Interpretation**
StyleMart's sales ranking is supported more by the number of transactions it processes than by the value of each transaction. This is a different growth pattern from FashionKita's, where fewer, higher-value orders produce a comparable sales outcome.

**Business Implication**
If the business wants to grow StyleMart's contribution further, increasing order value (e.g., through bundling or premium product placement) may be a more direct lever than increasing order count, since order count is already relatively strong on this platform.

---

### Insight: Completion rate spread across platforms is narrow but consistent

**Finding**
Completion rates across the five platforms range from 66.1% (FashionHub) to 70.7% (FashionKita) — a 4.6 percentage-point spread. The ranking order (FashionKita > Toko Fashion App > StyleMart > OutfitNow > FashionHub) does not follow the sales or order-volume ranking (Toko Fashion App > StyleMart > FashionKita > OutfitNow > FashionHub).

**Interpretation**
Completion rate appears to be an independent dimension of platform performance rather than a byproduct of scale. A platform being large or generating high sales does not predict it will also have the highest share of completed orders.

**Business Implication**
Platform-level completion rate should be tracked and managed as its own KPI, separate from sales and order targets, since improving it on the two largest platforms (Toko Fashion App and StyleMart) would recover disproportionately more revenue than the same improvement on smaller platforms.

---

## 3. Product Performance

### Insight: Sales-value leaders and unit-volume leaders are almost entirely different products

**Finding**
The top 5 products by sales value (Gamis Rp619M, Jaket Denim Rp479M, Celana Jeans Rp478M, Dress Formal Rp427M, Jaket Bomber Rp407M) share no overlap with the top 5 products by units sold (Kaos Pendek 1,882, Hijab 1,873, Celana Pendek 1,857, Kemeja Casual 1,549, Kaos Panjang 1,547).

**Interpretation**
Two distinct product tiers appear to be operating side by side: a lower unit-count, higher price-per-unit tier (led by Gamis, Jaket Denim, Celana Jeans) that drives sales-value rankings, and a higher unit-count, lower price-per-unit tier (led by Kaos Pendek, Hijab, Celana Pendek) that drives volume rankings. Using the combined units-sold and sales table, Gamis converts 1,191 units into Rp619M (≈Rp520K per unit), while Kemeja Casual converts a similar or higher unit count (1,549) into a much lower Rp375M (≈Rp242K per unit) — confirming the price-per-unit gap between these tiers.

**Business Implication**
Sales-value ranking alone can understate which products are actually moving through the business at high frequency. Category and inventory planning should track both leaderboards together, since a high-volume, lower-value product (like Kaos Pendek or Hijab) may warrant different stocking and pricing decisions than a low-volume, high-value product (like Gamis).

---

### Insight: Dress Formal is a premium-priced outlier among top sellers

**Finding**
Dress Formal ranks #4 by sales value (Rp427.2M) but sold only 716 units — the lowest unit count of any product in the top 11 sales ranking. By comparison, Rok Midi (rank #6, Rp385.0M) sold 1,375 units. The implied price per unit for Dress Formal (≈Rp597K) is roughly double that of most other top-11 products, which mostly fall in the Rp240K–430K per-unit range.

**Interpretation**
Dress Formal reaches a top sales-value position primarily through a high unit price rather than high sales frequency, distinguishing it from every other product in the top 11.

**Business Implication**
Dress Formal's revenue contribution is more sensitive to price and fewer, larger transactions than volume-driven products. Its performance should be monitored separately from high-frequency items, and its pricing strategy is worth understanding in more detail since it behaves differently from the rest of the top-selling catalog.

---

### Insight: Material demand is moderately concentrated in two fabrics

**Finding**
Orders by material show Katun at 24.6% and Polyester at 20.9% of total orders — together representing 45.5% of order volume. The remaining six materials (Satin, Twill, Jersey, Cotton Combed, Linen, Chambray) each account for roughly 6.7%–10.7%, without any single material exceeding 11%.

**Interpretation**
Material demand is concentrated but not dominated by a single fabric — the top two materials combine for under half of orders, while the other six are fairly evenly distributed.

**Business Implication**
These two materials account for a disproportionate share of observed order volume and may therefore warrant closer monitoring in assortment and inventory planning. The dashboard reflects order distribution by material only — it does not include supplier, cost, or lead-time data, so sourcing or procurement risk cannot be assessed from this view.

---

## 4. Order Health

### Insight: Roughly one in three orders does not reach completion

**Finding**
Of 5,000 total orders, 3,430 (68.6%) are Completed and 1,570 (31.4%) fall into non-completed statuses: Returned (694, 13.9%), Cancelled (371, 7.4%), Shipped/in-transit (263, 5.3%), and Pending (242, 4.8%). In sales terms, Completed orders represent Rp4,794,773,200 (68.7% of total sales) and non-completed orders represent Rp2,181,031,200 (31.3%).

**Interpretation**
Just under a third of order volume and sales value is currently sitting outside a completed state. Shipped and Pending orders (5.3% and 4.8% of orders) may still convert to Completed as they progress through the pipeline, while Returned and Cancelled orders (13.9% and 7.4%) represent orders that have already exited the pipeline without completing.

**Business Implication**
The completed-order rate is a primary health metric for the business. Because Returned and Cancelled orders alone represent 21.3% of all orders and Rp1.47bn in sales value, understanding the drivers behind these two statuses specifically (as opposed to Shipped/Pending, which may simply be in progress) is the more urgent operational priority.

---

### Insight: Returned orders are the largest single source of non-completed revenue exposure

**Finding**
Among the four non-completed statuses, Returned orders carry the highest sales value (Rp979,337,300) and the highest order count (694) — larger than Cancelled (Rp488,457,500 / 371 orders), Shipped (Rp362,525,900 / 263 orders), and Pending (Rp350,710,500 / 242 orders) individually. Returned sales alone represent 44.9% of total non-completed sales value (Rp979,337,300 of Rp2,181,031,200).

**Interpretation**
Returns are not just the most frequent non-completed status by order count — they also carry a proportionally large share of the associated revenue exposure, roughly matching their share of non-completed order volume (44.2%).

**Business Implication**
Returns represent the largest single area for investigation within non-completed orders. Even a modest reduction in the return rate would recover more revenue than a proportional improvement in any other non-completed category, making it a priority area for root-cause investigation (e.g., product fit, quality, or delivery issues), to the extent such data becomes available in future analysis.

---

### Insight: Completed and non-completed orders carry similar average value

**Finding**
The implied AOV of Completed orders (Rp4,794,773,200 ÷ 3,430 = ≈Rp1.398M) is nearly identical to the implied AOV of non-completed orders (Rp2,181,031,200 ÷ 1,570 = ≈Rp1.389M), both close to the overall AOV of Rp1,395,161.

**Interpretation**
Non-completion does not appear to be concentrated among unusually high- or low-value orders. The revenue at risk from non-completion scales roughly proportionally with order count rather than being skewed toward large or small transactions.

**Business Implication**
Efforts to reduce non-completion do not need to be weighted toward specific order-value segments based on current evidence — the exposure is broad-based. This also means completion-rate improvements should be projected using the overall AOV, since there is no indication that recovering non-completed orders would yield disproportionately higher or lower revenue per order.

---

## 5. Cross-Dashboard Insights

### Insight: The largest platform and the most efficient platform are not the same platform

**Finding**
Toko Fashion App leads on orders (1,753) and sales (Rp2,459M, 35.3% share) — both Platform Performance metrics. FashionKita leads on AOV (Rp1.41M) and completion rate (70.7%) — both quality metrics that combine Platform Performance and Order Health data. These leadership positions do not belong to the same platform.

**Interpretation**
Viewed on its own, the Platform Performance page suggests Toko Fashion App is the standout platform. Only by cross-referencing completion rate from the Order Health page does it become clear that a smaller platform outperforms it on reliability and per-order value.

**Business Implication**
Platform prioritization decisions should not rely on sales or order-volume rankings alone. A platform-level scorecard combining sales share, AOV, and completion rate would give a more complete view than any single dashboard page in isolation.

---

### Insight: High-value product leadership and high-volume product leadership are two separate stories

**Finding**
Product Performance shows Gamis leading sales value (Rp619M) while ranking outside the top 5 in units sold; conversely, Kaos Pendek, Hijab, and Celana Pendek lead units sold without reaching the top sales-value tier. This pattern is only visible by comparing the two rankings on the Product Performance page side by side, since neither ranking alone reveals it.

**Interpretation**
The product catalog operates across at least two price/volume tiers, and no single "top products" list captures both dimensions of commercial importance.

**Business Implication**
Category management and marketing decisions (e.g., what to feature, restock, or promote) should reference both leaderboards. Relying on sales-value rankings alone would deprioritize high-frequency products that may be equally important for customer acquisition or repeat purchase behavior.

---

### Insight: Order-volume concentration does not translate into proportional revenue efficiency

**Finding**
StyleMart holds the second-highest order count (1,134, from Platform Performance) but the lowest AOV (Rp1.37M, from Order Health / Platform Performance). Meanwhile FashionKita, with less than the order count of StyleMart (1,000), achieves a higher AOV (Rp1.41M) and a meaningfully higher completion rate (70.7% vs. 68.3%).

**Interpretation**
This relationship — fewer orders but stronger AOV and completion rate — is only apparent when the Platform Performance and Order Health pages are compared directly; each page individually only tells part of the story.

**Business Implication**
Growth strategy for StyleMart and FashionKita should differ: StyleMart likely has more room to improve through order-value initiatives, while FashionKita's existing model appears more efficient and may be worth understanding for potential replication elsewhere.

---

## 6. Key Business Takeaways

1. Order health is the largest operational issue in the dataset: 31.4% of orders (1,570 / Rp2.18bn) are non-completed, and Returned orders alone account for 44.9% of that exposure (Rp979M) — the single highest-priority finding in this analysis.
2. Sales concentration and platform quality diverge: Toko Fashion App leads on sales (35.3%) and order volume, but FashionKita leads on both AOV (Rp1.41M) and completion rate (70.7%), and completion rate overall varies more by platform (66.1%–70.7%) than the sales ranking would suggest.
3. StyleMart's performance is volume-driven rather than value-driven, evidenced by its second-highest order count paired with the lowest AOV (Rp1.37M) of any platform.
4. Non-completion risk is broad-based by order value: Completed and non-completed orders carry nearly identical average values (~Rp1.39M–1.40M), so exposure scales with order count rather than a skew toward specific transaction sizes.
5. Product performance splits into distinct value and volume tiers: Gamis leads sales value while Kaos Pendek, Hijab, and Celana Pendek lead unit volume with no overlap between rankings, and Dress Formal stands out further as a premium-priced outlier (Rp427M in sales from only 716 units).
6. Material demand is moderately concentrated in Katun and Polyester (45.5% combined) — a pattern worth monitoring in assortment and inventory planning, though the dataset does not support conclusions about sourcing or supply-chain risk.

---

## 7. Potential Business Recommendations

**Platform strategy**
- Investigate the drivers behind Toko Fashion App's sales concentration by comparing order frequency, AOV, and completion rate against FashionKita, to determine whether FashionKita's efficiency advantages (higher AOV, higher completion rate) can be replicated on higher-volume platforms.
- StyleMart may warrant investigation into order-value levers such as bundling or premium product placement, given its strong order volume but lowest AOV among all platforms.
- Build a combined platform scorecard (sales share, AOV, completion rate) rather than relying on sales ranking alone, since the current pages show these metrics do not move together.

**Product strategy**
- Maintain separate visibility into sales-value and unit-volume product rankings when making assortment and promotion decisions, since the two leaderboards currently identify different products.
- Review pricing and demand data for Dress Formal specifically, given its unusually high implied price per unit relative to other top-11 products, to confirm whether this reflects intentional premium positioning.
- Monitor Katun and Polyester more closely in assortment and inventory planning given their combined 45.5% share of observed order volume, while treating the remaining six materials as a more evenly distributed secondary group. This reflects order-volume patterns only; sourcing, cost, or supply-chain conclusions would require additional data not present in this dashboard.

**Order / operational management**
- Treat return reduction as the top operational priority within order health, since Returned orders represent both the largest non-completed order count (694) and the largest non-completed revenue exposure (Rp979M, 44.9% of non-completed sales).
- Establish a routine review of Cancelled orders (371 orders, Rp488M) separately from Shipped/Pending orders, since Cancelled and Returned orders have already exited the pipeline while Shipped and Pending may still convert to Completed.
- Track completion rate by platform and by product as an ongoing KPI, given the meaningful spread already observed (66.1%–70.7% by platform; 69.6%–70.1% among the top completion-rate products).

**Further analysis**
- Obtain a platform-by-order-status cross-tabulation (orders and sales) to confirm which platform carries the largest absolute non-completed revenue exposure — the current dashboards only support this as an inference from combined figures, not a direct measurement.
- Obtain completion rate for all top sales-value products (particularly Gamis), since the current Top 5 by Completion Rate view does not include the top revenue product and cannot confirm or rule out a completion-rate risk there.
- If customer, cost, or return-reason data becomes available in a future iteration, layer it onto this analysis to move from "what is happening" (which this project establishes) toward "why it is happening."