# Decision Algorithms by Campaign Type

These decision trees are applied to every keyword AND every search term. For search terms, replace "PAUSE" with "add as negative keyword" (Exact match for relevant-but-ineffective, trigger-word for irrelevant).

## E-commerce Campaigns

Primary metric: ROAS. Secondary: CPA, CR, CTR.

```
FOR EACH keyword/search term in an e-commerce campaign:
│
├── SCENARIO 1: Insufficient data
│   (Clicks < 10)
│   → Category: WATCH
│   → Do not act, mark as "insufficient data"
│
├── SCENARIO 2: Has clicks, conversions, AND revenue
│   (Clicks ≥ 10, Conversions ≥ 1, Revenue > 0)
│   │
│   ├── ROAS ≥ Avg ROAS × 1.3
│   │   → Category: ★ SCALE
│   │   → Actions: increase bid, expand match type,
│   │     check impression share — if < 80%, room to grow
│   │
│   ├── ROAS within Avg ROAS × 0.7 … Avg ROAS × 1.3
│   │   → Category: ● KEEP
│   │
│   ├── ROAS < Avg ROAS × 0.7 BUT ROAS > 1.0 (profitable)
│   │   → Category: ▲ OPTIMIZE
│   │   → Actions: check ads and landing page, lower bid
│   │
│   └── ROAS < 1.0 (unprofitable)
│       ├── Conversions ≥ 5 (reliable data)
│       │   → Category: ✖ PAUSE
│       │   → Actions: pause or drastically lower bid
│       └── Conversions < 5 (unreliable)
│           → Run BEST-CASE ROAS TEST (see below)
│           → If best_case_roas < Avg ROAS × 0.7 → ✖ PAUSE
│           → If best_case_roas ≥ Avg ROAS × 0.7 → ▲ OPTIMIZE WITH CAUTION
│           → Comment for PAUSE: "Even in best-case scenario,
│             blended ROAS = X (= Y× avg). Pause recommended."
│
├── SCENARIO 3: Has clicks and conversions BUT Revenue = 0
│   (Clicks ≥ 10, Conversions ≥ 1, Revenue = 0)
│   → Anomaly in e-commerce: conversion without revenue
│   → Possible causes: micro-conversion, tracking error, cancelled order
│   → Action: analyze as conversion campaign (by CPA/CR),
│     flag for tracking verification
│
├── SCENARIO 4: Has clicks, ZERO conversions
│   (Clicks ≥ 10, Conversions = 0)
│   │
│   │   COMMENT RULE: When Conv = 0, CPA cannot be calculated.
│   │   NEVER output a computed or fallback CPA number.
│   │   Use this template: "0 conversions, spent $X (= N× avg CPA)"
│   │
│   ├── Cost ≥ Avg CPA × 2
│   │   → Spent 2× a conversion's cost with zero results
│   │   → Category: ✖ PAUSE
│   │   → Comment: "0 conversions, spent $[Cost] (= [Cost/AvgCPA]× avg CPA). Pause."
│   │   → Check relevance of KW to product and landing page
│   │
│   ├── Cost ≥ Avg CPA × 1 but < Avg CPA × 2
│   │   → Category: ⚠ WATCH
│   │   → Comment: "0 conversions, spent $[Cost] (= [Cost/AvgCPA]× avg CPA). Monitor."
│   │   → Lower bid, check ad + landing page, allow 1-2 more weeks
│   │
│   ├── Cost < Avg CPA × 1
│   │   → Not enough spent to expect a conversion yet
│   │   → Category: WATCH (monitor)
│   │   → Comment: "0 conversions, spent $[Cost] — below avg CPA, allow more time"
│   │
│   └── Additional CTR check:
│       ├── CTR < Avg CTR × 0.5 → poor clickability, check search terms
│       └── CTR ≥ Avg CTR × 0.5 → clicks OK, problem is post-click
│
└── SCENARIO 5: Impressions but no clicks
    (Impressions > 0, Clicks = 0)
    ├── Impressions ≥ 100 → check ad copy and position
    └── Impressions < 100 → insufficient data, monitor
```

## Conversion Campaigns

Primary metrics: CPA, CR. Secondary: CTR, CPC.

```
FOR EACH keyword/search term in a conversion campaign:
│
├── SCENARIO 1: Insufficient data
│   (Clicks < 10)
│   → Category: WATCH
│
├── SCENARIO 2: Sufficient conversions (≥ 5)
│   (Clicks ≥ 10, Conversions ≥ 5)
│   │
│   ├── CPA ≤ Avg CPA × 0.7 (30%+ cheaper than average)
│   │   → Category: ★ SCALE
│   │   → Increase bid, expand match type
│   │
│   ├── CPA within Avg CPA × 0.7 … Avg CPA × 1.3
│   │   → Category: ● KEEP
│   │
│   └── CPA > Avg CPA × 1.3 (30%+ more expensive)
│       → Category: ▲ OPTIMIZE
│       → Lower bid, check ads + landing page
│       → If CPA > Avg CPA × 2 and Conversions ≥ 5: consider PAUSE
│
├── SCENARIO 3: Some conversions but < 5
│   (Clicks ≥ 10, 0 < Conversions < 5)
│   → Conversion data unreliable — apply BEST-CASE TEST for high CPA
│   │
│   ├── CPA ≤ Avg CPA × 1.3
│   │   → Good direction but insufficient data
│   │   → Category: ● KEEP, monitor
│   │
│   ├── CPA > Avg CPA × 1.3 but < Avg CPA × 2
│   │   → Category: ⚠ WATCH
│   │   → Lower bid, allow 2-4 more weeks
│   │
│   └── CPA > Avg CPA × 2
│       → Run BEST-CASE TEST (see below)
│       → If best_case_cpa > Avg CPA × 1.5 → ✖ PAUSE
│         Comment: "Even in best-case scenario, blended CPA = $X
│         (= Y× avg). Optimization cannot recover. Pause recommended."
│       → If best_case_cpa ≤ Avg CPA × 1.5 → ▲ OPTIMIZE
│         Comment: "CPA elevated but recoverable. Lower bid, monitor."
│
├── SCENARIO 4: Has clicks, ZERO conversions
│   (Clicks ≥ 10, Conversions = 0)
│   │
│   │   COMMENT RULE: When Conv = 0, CPA cannot be calculated.
│   │   NEVER output a computed or fallback CPA number.
│   │   Use this template: "0 conversions, spent $X (= N× avg CPA)"
│   │
│   ├── Cost ≥ Avg CPA × 3
│   │   → Spent 3× a conversion's cost with zero results
│   │   → Category: ✖ PAUSE
│   │   → Comment: "0 conversions, spent $[Cost] (= [Cost/AvgCPA]× avg CPA). Pause."
│   │   → Check relevance, check search terms for junk traffic
│   │
│   ├── Cost ≥ Avg CPA × 1.5 but < Avg CPA × 3
│   │   → Category: ⚠ WATCH → approaching PAUSE
│   │   → Comment: "0 conversions, spent $[Cost] (= [Cost/AvgCPA]× avg CPA). Monitor."
│   │   → Lower bid 30-50%, check ad + landing page
│   │   → If no conversions in 2 weeks → PAUSE
│   │
│   ├── Cost < Avg CPA × 1.5
│   │   → Not enough spent to expect a conversion
│   │   → Category: WATCH (monitor)
│   │   → Comment: "0 conversions, spent $[Cost] — below avg CPA, allow more time"
│   │
│   └── Additional CTR check:
│       ├── CTR < Avg CTR × 0.5 → weak clickability, check search terms
│       └── CTR ≥ Avg CTR → clicks OK, problem is landing page/offer
│
└── SCENARIO 5: Impressions but no clicks
    → Same as e-commerce Scenario 5
```

## Traffic Campaigns

Primary metrics: CTR, CPC. No conversion tracking.

```
FOR EACH keyword/search term in a traffic campaign:
│
├── SCENARIO 1: Insufficient data
│   (Impressions < 100 OR Clicks < 10)
│   → Category: WATCH
│
├── SCENARIO 2: Sufficient data
│   (Impressions ≥ 100, Clicks ≥ 10)
│   │
│   ├── CTR ≥ Avg CTR × 1.3 AND CPC ≤ Avg CPC × 1.3
│   │   → Category: ★ SCALE
│   │   → High clickability at reasonable cost
│   │
│   ├── CTR ≥ Avg CTR × 0.7 AND CPC ≤ Avg CPC × 1.3
│   │   → Category: ● KEEP
│   │
│   ├── CTR < Avg CTR × 0.7
│   │   → Category: ▲ OPTIMIZE or ✖ PAUSE
│   │   → If CTR < Avg CTR × 0.3 → consider PAUSE
│   │
│   └── CPC > Avg CPC × 1.3
│       → Expensive clicks
│       ├── CTR high → competitive query, lower bid
│       └── CTR low → irrelevant query, PAUSE
│
└── SCENARIO 3: Impressions but no clicks
    (Impressions ≥ 100, Clicks = 0)
    → Category: ✖ PAUSE
```

## Summary Decision Table

| Scenario | E-commerce | Conversion | Traffic |
|---|---|---|---|
| Insufficient data (Clicks < 10) | Watch | Watch | Watch |
| Excellent performance | ★ Scale (ROAS > Avg×1.3) | ★ Scale (CPA < Avg×0.7) | ★ Scale (CTR > Avg×1.3, CPC OK) |
| Average performance | ● Keep | ● Keep | ● Keep |
| Below average | ▲ Optimize (ROAS < Avg×0.7 but > 1.0) | ▲ Optimize (CPA > Avg×1.3) | ▲ Optimize (CTR < Avg×0.7) |
| Few conv (< 5), CPA > 2× avg | Best-case test → ✖ Pause or ▲ Optimize | Best-case test → ✖ Pause or ▲ Optimize | — |
| Unprofitable (ROAS < 1.0), Conv < 5 | Best-case test → ✖ Pause or ▲ Optimize | — | — |
| 0 conv, Cost ≥ 3× Avg CPA | ✖ Pause | ✖ Pause | — |
| 0 conv, Cost ≥ 1.5× Avg CPA | ⚠ → approaching Pause | ⚠ → approaching Pause | — |
| 0 conv, Cost < 1.5× Avg CPA | Watch | Watch | — |
| 0 clicks, ≥ 100 impressions | Check ad | Check ad | ✖ Pause |

## Best-Case Test (for < 5 conversions with extreme CPA)

This test determines whether a keyword/search term/ad group with few conversions and high CPA can mathematically recover to an acceptable level.

**When to apply:** Any row with 0 < Conversions < 5 AND CPA > Avg CPA × 2.

**Formula:**
```
best_case_cpa = (Current Cost + (5 - Current Conversions) × Avg CPA) / 5
```

**Logic:** We assume that all remaining conversions needed to reach the reliability threshold (5) will arrive at the ideal campaign-average cost. This is the most optimistic scenario possible.

**Decision:**
- If `best_case_cpa > Avg CPA × 1.5` → ✖ PAUSE
  - Even in the best-case scenario, blended CPA will remain >50% above average.
  - Optimization is mathematically hopeless. Accumulated spend is too high.
  - Comment: "Even best-case blended CPA = $[best_case_cpa] ([X]× avg). Cannot recover. Pause."
- If `best_case_cpa ≤ Avg CPA × 1.5` → ▲ OPTIMIZE
  - There's a mathematical chance to normalize if future conversions come at avg cost.
  - Comment: "CPA elevated but recoverable if future conversions come at avg cost. Lower bid, monitor."

**Example:**
```
KW: "premium air curtain"
Cost = $500, Conversions = 1, CPA = $500
Campaign Avg CPA = $100

best_case_cpa = ($500 + (5 - 1) × $100) / 5
             = ($500 + $400) / 5
             = $180

$180 > $100 × 1.5 ($150) → ✖ PAUSE
Comment: "Even best-case blended CPA = $180 (1.8× avg). Cannot recover. Pause."
```

**Another example (recoverable):**
```
KW: "commercial air door"
Cost = $200, Conversions = 2, CPA = $100
Campaign Avg CPA = $60

best_case_cpa = ($200 + (5 - 2) × $60) / 5
             = ($200 + $180) / 5
             = $76

$76 ≤ $60 × 1.5 ($90) → ▲ OPTIMIZE
Comment: "CPA elevated but recoverable. Lower bid, monitor."
```

## Best-Case ROAS Test (for E-commerce: < 5 conversions with ROAS < 1.0)

This test determines whether a keyword/search term in an e-commerce campaign with few conversions and low ROAS can mathematically recover to a profitable level.

**When to apply:** E-commerce campaigns only. Any row with 0 < Conversions < 5 AND ROAS < 1.0.

**Required campaign-level metrics:**
```
Avg CPA = SUM(Cost) / SUM(Conversions)                  — across the campaign
Avg Revenue per Conv = SUM(Revenue) / SUM(Conversions)   — across the campaign
```

**Formula:**
```
best_case_roas = (Current Revenue + (5 - Current Conv) × Avg Revenue per Conv)
                 ÷
                 (Current Cost + (5 - Current Conv) × Avg CPA)
```

**Logic:** We assume the remaining conversions (to reach reliability threshold of 5) will each:
- Cost exactly the campaign average CPA (optimistic)
- Bring exactly the campaign average revenue per conversion (optimistic)

This is the most optimistic scenario. If ROAS still doesn't recover — optimization is hopeless.

**Decision:**
- If `best_case_roas < Avg ROAS × 0.7` → ✖ PAUSE
  - Even in the best case, blended ROAS will remain >30% below campaign average.
  - The accumulated cost is too high to recover from.
  - Comment: "Even best-case blended ROAS = [best_case_roas] ([X]× avg). Cannot recover. Pause."
- If `best_case_roas ≥ Avg ROAS × 0.7` → ▲ OPTIMIZE
  - There's a mathematical chance to reach acceptable ROAS.
  - Comment: "ROAS below target but recoverable if future conversions match campaign average. Lower bid, monitor."

**Example (unrecoverable):**
```
KW: "premium air curtain"
Cost = $800, Revenue = $200, Conversions = 1
Campaign: Avg CPA = $100, Avg Revenue/Conv = $350, Avg ROAS = 3.5

best_case_roas = ($200 + (5 - 1) × $350) / ($800 + (5 - 1) × $100)
               = ($200 + $1400) / ($800 + $400)
               = $1600 / $1200
               = 1.33

1.33 < 3.5 × 0.7 (= 2.45) → ✖ PAUSE
Comment: "Even best-case blended ROAS = 1.33 (0.38× avg). Cannot recover. Pause."
```

**Example (recoverable):**
```
KW: "commercial heated air door"
Cost = $250, Revenue = $400, Conversions = 2
Campaign: Avg CPA = $100, Avg Revenue/Conv = $350, Avg ROAS = 3.5

best_case_roas = ($400 + (5 - 2) × $350) / ($250 + (5 - 2) × $100)
               = ($400 + $1050) / ($250 + $300)
               = $1450 / $550
               = 2.64

2.64 ≥ 3.5 × 0.7 (= 2.45) → ▲ OPTIMIZE
Comment: "ROAS below target but recoverable. Lower bid, monitor."
```

## Comment Rules for Zero-Conversion Rows

**CRITICAL: When Conversions = 0, CPA is undefined (division by zero).**

Rules for all output — Comments column, RECAP blocks, all sheets:
1. NEVER output a computed CPA value when Conversions = 0.
2. NEVER use a fallback/sentinel value (999, Inf, N/A) as if it were a real CPA.
3. ALWAYS use cost-based phrasing: "0 conversions, spent $[Cost] (= [Cost/AvgCPA]× avg CPA)"
4. In the CPA column on analysis sheets: leave blank or show "—" when Conv = 0.

## Search Term–Specific: Adding as KW

Recommend adding a search term as a separate keyword ONLY when results are outstanding:

| Campaign type | Required condition | Additional |
|---|---|---|
| E-commerce | ROAS ≥ Avg ROAS × 1.3 AND Conversions ≥ 2 | Revenue growing |
| Conversion | CPA ≤ Avg CPA × 0.7 AND Conversions ≥ 2 | CR well above average |
| Traffic | CTR ≥ Avg CTR × 1.5 AND Clicks ≥ 15 | CPC ≤ Avg CPC |

Do NOT add if: nearly identical to existing KW, fewer than 2 conversions, too long/low-volume to matter.

Recommended match type when adding: Exact.
