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
│           → Category: ▲ OPTIMIZE WITH CAUTION
│           → Actions: lower bid, monitor 2-4 more weeks
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
│   ├── Cost ≥ Avg CPA × 2
│   │   → Spent 2× a conversion's cost with zero results
│   │   → Category: ✖ PAUSE
│   │   → Check relevance of KW to product and landing page
│   │
│   ├── Cost ≥ Avg CPA × 1 but < Avg CPA × 2
│   │   → Category: ⚠ WATCH
│   │   → Lower bid, check ad + landing page, allow 1-2 more weeks
│   │
│   ├── Cost < Avg CPA × 1
│   │   → Not enough spent to expect a conversion yet
│   │   → Category: WATCH (monitor)
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
│   → Conversion data unreliable
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
│       → Category: ▲ OPTIMIZE
│       → Lower bid, check ads; if no improvement in 2-4 weeks → PAUSE
│
├── SCENARIO 4: Has clicks, ZERO conversions
│   (Clicks ≥ 10, Conversions = 0)
│   │
│   ├── Cost ≥ Avg CPA × 3
│   │   → Spent 3× a conversion's cost with zero results
│   │   → Category: ✖ PAUSE
│   │   → Check relevance, check search terms for junk traffic
│   │
│   ├── Cost ≥ Avg CPA × 1.5 but < Avg CPA × 3
│   │   → Category: ⚠ WATCH → approaching PAUSE
│   │   → Lower bid 30-50%, check ad + landing page
│   │   → If no conversions in 2 weeks → PAUSE
│   │
│   ├── Cost < Avg CPA × 1.5
│   │   → Not enough spent to expect a conversion
│   │   → Category: WATCH (monitor)
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
| Few conversions (< 5) | ⚠ Watch | ⚠ Watch | — |
| Unprofitable (ROAS < 1.0) | ✖ Pause | — | — |
| 0 conv, Cost ≥ 3× Avg CPA | ✖ Pause | ✖ Pause | — |
| 0 conv, Cost ≥ 1.5× Avg CPA | ⚠ → approaching Pause | ⚠ → approaching Pause | — |
| 0 conv, Cost < 1.5× Avg CPA | Watch | Watch | — |
| 0 clicks, ≥ 100 impressions | Check ad | Check ad | ✖ Pause |

## Search Term–Specific: Adding as KW

Recommend adding a search term as a separate keyword ONLY when results are outstanding:

| Campaign type | Required condition | Additional |
|---|---|---|
| E-commerce | ROAS ≥ Avg ROAS × 1.3 AND Conversions ≥ 2 | Revenue growing |
| Conversion | CPA ≤ Avg CPA × 0.7 AND Conversions ≥ 2 | CR well above average |
| Traffic | CTR ≥ Avg CTR × 1.5 AND Clicks ≥ 15 | CPC ≤ Avg CPC |

Do NOT add if: nearly identical to existing KW, fewer than 2 conversions, too long/low-volume to matter.

Recommended match type when adding: Exact.
