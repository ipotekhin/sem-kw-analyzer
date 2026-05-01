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
1. **Campaign level** (always) — primary benchmark.
2. **Cluster level** (when needed) — see clustering rules below.
3. **Ad group level** (optional) — only if a group has ≥ 5 KWs with clicks.

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

### When to cluster

If a single campaign contains ad groups for fundamentally different products, comparing all KWs to one campaign average is misleading.

**Detection:**
1. Calculate CR (or CPA, ROAS) per ad group within the campaign.
2. Compute coefficient of variation: CV = StdDev(metric) / Mean(metric)
3. If CV > 0.5 → clustering is needed.

**Example:**
```
Campaign "Appliances"
├── Group "Phones"       — CR = 5.2%, CPA = $12
├── Group "TVs"          — CR = 1.8%, CPA = $45
└── Group "Refrigerators"— CR = 0.9%, CPA = $120

Mean CR = 2.6%, StdDev = 2.2%, CV = 0.85 > 0.5 → cluster
```

### How to cluster

Group ad groups by product category (inferred from names) and/or by similar performance levels. Calculate separate benchmarks for each cluster. Compare KWs within their own cluster.

### Two-level comparison

When clusters exist, report both:
1. **Within cluster:** Is this KW performing well relative to similar products?
2. **Between clusters:** Which product categories deserve more/less budget? (this is for campaign-level recommendations, not per-KW decisions)

## Typical Benchmark Ranges by Campaign Type

These are orientation only — always use calculated benchmarks from the actual data.

| Campaign type | Typical CTR | Typical CR | Typical CPC |
|---|---|---|---|
| Branded | 15–40% | 5–15% | Low |
| Non Branded | 2–10% | 1–5% | Medium–High |
| Competitor | 1–5% | 0.5–3% | High |
