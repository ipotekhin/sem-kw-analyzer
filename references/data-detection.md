# Data Detection and Structure Identification

## Sheet Classification

When opening the uploaded file, inspect each sheet:

### Identifying a Keywords sheet
- Contains column "Keyword" (or localized: "Ключевое слово", "Mot clé", etc.)
- Does NOT contain "Search term" / "Search query"
- Does NOT contain "Added/Excluded"
- "Match type" column shows KW match types: Broad, Phrase, Exact

### Identifying a Search Terms sheet
- Contains column "Search term" or "Search query"
- May contain "Added/Excluded" column (values: "Added", "Excluded", or empty/NaN)
- May contain "Keyword" column alongside "Search term" (shows which KW triggered the query)
- "Match type" column shows trigger types: exact, phrase, broad match, close variant, exact (close variant), phrase (close variant)

### Non-standard sheets
If sheet names don't clearly indicate content, classify by columns present. If a sheet has both "Keyword" and "Search term" columns — treat as Search Terms (it shows the parent KW for each search term).

## Metric Detection

### Base metrics (present in almost all exports)
| Possible column names | Standardized name | Type |
|---|---|---|
| Impressions, Impr., Показы | Impressions | Integer |
| Clicks, Клики | Clicks | Integer |
| CTR, Click-through rate, CTR (%) | CTR | Decimal (0-1) or percentage |
| CPC, Avg. CPC, Cost/click | CPC | Currency |
| Cost, Spend, Расход | Cost | Currency |

### Conversion metrics
| Possible column names | Standardized name | Type |
|---|---|---|
| Conversions, Conv., Конверсии | Conversions | Decimal (fractional allowed) |
| Conv. rate, CR, Conversion rate | CR | Decimal or percentage |
| Cost/conv., CPA, Cost per conversion | CPA | Currency |

### E-commerce metrics
| Possible column names | Standardized name | Type |
|---|---|---|
| Conv. value, Revenue, Доход | Revenue | Currency |
| Conv. value/cost, ROAS, Return on ad spend | ROAS | Multiplier or percentage |

## Data Validation Checklist

1. **Remove summary rows:** Filter out rows where Campaign = "Total", "Summary", or where all numeric fields are aggregated.
2. **CTR/CR format:** If max(CTR) > 1 → values are percentages, divide by 100. Same for CR.
3. **ROAS format:** If max(ROAS) > 50 → likely percentage (320% = 3.2×), divide by 100.
4. **Negative values:** CPC, CPA, Cost must be ≥ 0. Flag any negatives as data errors.
5. **Empty/NaN handling:** Treat NaN in numeric columns as 0 for aggregation, but mark as "no data" for per-row analysis.
6. **Fractional conversions:** Normal in Google Ads (data-driven attribution). Do not round.

## Campaign Type Detection from Names

Scan campaign names for these patterns:

| Pattern in name | Campaign classification |
|---|---|
| "brand", "branded", "бренд" | Branded |
| "competitor", "конкурент", "comp" | Competitor |
| "DSA", "Dynamic Search Ads", "dynamic" | DSA |
| "non brand", "non-brand", "nonbrand", "generic", "не бренд" | Non Branded |
| None of the above | Non Branded (default) |

Also check: if all keywords in a campaign contain the company name → likely Branded even if not labeled.
