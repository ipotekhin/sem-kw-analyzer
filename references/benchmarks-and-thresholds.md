# Benchmarks, Thresholds, and Clustering

## Benchmark Calculation

All averages are **weighted** — never use simple AVERAGE() across rows.

```
Avg CTR  = SUM(Clicks) / SUM(Impressions)
Avg CPC  = SUM(Cost) / SUM(Clicks)
Avg CR   = SUM(Conversions) / SUM(Clicks)
Avg CPA  = SUM(Cost) / SUM(Conversions)       # only if SUM(Conversions) > 0
Avg ROAS = SUM(Revenue) / SUM(Cost)            # only if SUM(Cost) > 0
```

Calculate at these levels:
1. **Campaign level** (always) — this is the PRIMARY benchmark used for all keyword/search term classification. All keywords within a campaign are compared to these numbers.
2. **Cluster level** (rare exception) — ONLY when a campaign mixes fundamentally different product categories. See clustering rules below.
3. **Ad group level** (for group-level insight only) — aggregated ad group stats are compared to the campaign benchmark to identify strong/weak groups. These are NOT used as per-keyword benchmarks.

## Metric Direction

| Metric | Better = | Worse = |
|---|---|---|
| CTR | Higher | Lower |
| CR | Higher | Lower |
| ROAS | Higher | Lower |
| Revenue | Higher | Lower |
| CPC | Lower | Higher |
| CPA | Lower | Higher |
| Cost (no conversions) | Lower | Higher |

## Deviation Thresholds

| Zone | Threshold | Color | Action |
|---|---|---|---|
| Excellent | >30% better than avg | Green (#C6EFCE) | Scale/maintain |
| Good | 15-30% better | No highlight | Maintain |
| Normal | ±15% of avg | No highlight | Maintain |
| Attention | 15-30% worse | Orange (#FDE9D9) | Monitor/optimize |
| Critical | >30% worse | Red (#FFC7CE) | Optimize/pause |

Formulas:
- For "higher = better" metrics (CTR, CR, ROAS): Green if value > avg × 1.3; Red if value < avg × 0.7
- For "lower = better" metrics (CPC, CPA): Green if value < avg × 0.7; Red if value > avg × 1.3

## Data Sufficiency Thresholds

| What to evaluate | Minimum threshold | Reasoning |
|---|---|---|
| CTR | ≥ 100 impressions | Below 100 — random noise |
| CPC | ≥ 10 clicks | Few clicks → CPC depends on individual auctions |
| CR | ≥ 30 clicks | Below 30 — conversion rate statistically unreliable |
| CPA | ≥ 5 conversions | Few conversions → CPA is random |
| ROAS | ≥ 5 conversions with Revenue > 0 | Same as CPA |

### Data volume classification

```
NO DATA:
  Impressions = 0 or row missing
  → Skip, do not analyze

MINIMAL DATA:
  Impressions > 0 but Clicks < 10
  → Can assess: demand exists (impressions present)
  → Cannot assess: CTR, CPC, CR, CPA
  → Action: WATCH, do not act

SUFFICIENT FOR TRAFFIC:
  Clicks ≥ 10, Impressions ≥ 100
  → Can assess: CTR, CPC
  → Cannot assess: CR, CPA (if conversions < 5)

SUFFICIENT FOR CONVERSIONS:
  Clicks ≥ 30 AND Conversions ≥ 5
  → Can assess: all metrics
  → Action: full analysis

SUFFICIENT BUT FEW CONVERSIONS:
  Clicks ≥ 30, Conversions > 0 but < 5
  → Can assess: CTR, CPC, CR direction (unreliable)
  → Action: analyze traffic + cautious conversion assessment

SUFFICIENT WITH ZERO CONVERSIONS:
  Clicks ≥ 30, Conversions = 0
  → Signal: KW gets traffic but doesn't convert
  → Action: compare Cost to Avg CPA (see decision algorithms)
```

## Clustering Rules

### Default: NO clustering

**By default, use campaign-level benchmarks for all keywords.** This is the correct approach for 90%+ of campaigns. Even if ad groups have different performance levels, that's the point — comparing all keywords to the same benchmark reveals which ad groups and keywords are pulling their weight and which are not.

**Do NOT cluster just because ad groups show performance variance.** Variance between groups is expected and informative — it's how you identify weak groups.

### When to cluster (rare exception)

Clustering is justified ONLY when a campaign contains fundamentally different **product categories** that inherently have different economics. This means different products with structurally different price points, conversion cycles, or customer intent.

**Example where clustering IS justified:**
```
Campaign "All Products"
├── Groups about "Cars"        — CPA = $500 (normal for cars)
├── Groups about "Accessories" — CPA = $25 (normal for accessories)

These are structurally different products. A $100 CPA is terrible
for accessories but great for cars. Cluster by product category.
```

**Example where clustering is NOT justified:**
```
Campaign "Air Curtains" (Branded)
├── Group "Brand Terms"    — CR = 4.0%
├── Group "Air Curtains"   — CR = 1.8%
├── Group "Models"         — CR = 1.5%

These are all the same product (air curtains) targeted with different
keyword strategies. Do NOT cluster — use one campaign benchmark.
The difference in CR tells you which keyword strategies work better.
```

**When clustering IS applied:**
- Cluster by product category (NOT by ad group)
- Multiple ad groups about the same product = same cluster
- Calculate separate benchmarks per cluster
- Report between-cluster comparison as a budget allocation insight

## Ad Group Performance Comparison

This is a separate analysis that runs AFTER keyword classification. It does NOT affect benchmarks — it provides additional insight.

**Process:**
1. For each ad group within a campaign, aggregate: total Clicks, Cost, Conversions, CPA (or ROAS), CTR.
2. Compare each ad group's aggregated metrics to the campaign-level benchmark.
3. Classify each ad group:

| Ad group performance vs campaign benchmark | Category | Recommendation |
|---|---|---|
| CPA ≤ Avg CPA × 0.7 (or ROAS ≥ Avg × 1.3) | ★ Strong group | Scale — increase bids/budget for this group |
| CPA within Avg × 0.7 … Avg × 1.3 | ● Average group | Maintain |
| CPA > Avg CPA × 1.3, sufficient data | ▲ Weak group | Check if one KW drags it down (pause that KW) or if the whole group underperforms (consider pausing group) |
| CPA > Avg CPA × 2, sufficient data | ✖ Poor group | Consider pausing the entire group |
| Insufficient data (< 10 clicks or < 1 conv) | ⚠ Needs data | Do not act yet |

**Reporting:** Ad group comparison is shown on the RECAP sheet as an additional insight block before individual keyword recommendations.

## Typical Benchmark Ranges by Campaign Type

These are orientation only — always use calculated benchmarks from the actual data.

| Campaign type | Typical CTR | Typical CR | Typical CPC |
|---|---|---|---|
| Branded | 15–40% | 5–15% | Low |
| Non Branded | 2–10% | 1–5% | Medium–High |
| Competitor | 1–5% | 0.5–3% | High |
