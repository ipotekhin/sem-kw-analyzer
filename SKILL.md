---
name: sem-kw-analyzer
version: v1.1
author: ipotekhin
description: "Analyze SEM keyword and search term performance data from uploaded spreadsheets (xlsx, csv). Use this skill whenever the user uploads a file containing campaign statistics from Google Ads, Microsoft Ads (Bing), or any other SEM platform — including keyword reports, search term reports, or search query reports. Trigger when the user asks to analyze keyword performance, find negative keywords, identify wasteful spend, optimize search terms, review PPC/SEM campaign data, or mentions 'search terms', 'negative keywords', 'keyword analysis', 'SQR' (search query report), 'CPA optimization', 'ROAS analysis', or any request to evaluate ad campaign keywords. This skill handles the full workflow: reading the file, calculating benchmarks, classifying every keyword/search term, generating negative keyword lists, and producing a formatted Excel report."
---

# SEM Keyword & Search Term Analyzer

## Overview

This skill analyzes keyword and search term statistics from SEM platforms (Google Ads, Microsoft Ads / Bing, and others), classifies each entry by performance, and produces an actionable Excel report with negative keywords to add, keywords to pause, and search terms to promote.

## Prerequisites — BLOCKING

**Before ANY analysis, the user MUST provide a link to the client's website or landing page.**

If the user has not provided a URL, ask for it immediately:
> "To analyze your keywords accurately, I need to see the client's website first — it's essential for determining which search terms are relevant to the business. Could you share the URL?"

Once received, fetch the website and study: what products/services are offered, B2B vs B2C, commercial vs residential, geographic coverage, languages. Keep this context for all relevance decisions throughout the analysis.

## Workflow

### Step 1: Read the file and detect structure

Read `references/data-detection.md` for detailed field-mapping rules.

1. Open the uploaded file (xlsx or csv). List all sheet names.
2. For each sheet, read column headers and classify as **Keywords** or **Search Terms**:
   - Keywords sheet: contains a column named "Keyword" (no "Search term" column, no "Added/Excluded" column).
   - Search Terms sheet: contains a column named "Search term" or "Search query", and usually "Added/Excluded".
3. Identify which metric columns are present (Impressions, Clicks, CTR, CPC, Cost, Conversions, CR, CPA, Revenue, ROAS, etc.).
4. Validate data: remove total/summary rows, check CTR/CR ranges (if > 1, divide by 100), verify no negative costs.

### Step 2: Determine campaign type

For **each campaign** separately:

| Condition | Campaign type | Primary metrics | Secondary metrics |
|---|---|---|---|
| Revenue/Conv. Value column exists AND values > 0 | **E-commerce** | ROAS, Revenue, CPA | CR, CTR, CPC |
| Conversions column exists AND values > 0 (at least some) | **Conversion** | CPA, CR | CTR, CPC |
| Only base metrics (no conversions, no revenue) | **Traffic** | CTR, CPC | Impressions, Imp. Share |

Also classify each campaign as Branded / Non Branded / Competitor / DSA based on campaign name patterns. Never compare metrics across these types.

### Step 3: Calculate benchmarks

Read `references/benchmarks-and-thresholds.md` for formulas and clustering rules.

Calculate **weighted averages** at the **campaign level** (this is the PRIMARY and DEFAULT benchmark):

```
Avg CTR  = SUM(Clicks) / SUM(Impressions)
Avg CPC  = SUM(Cost) / SUM(Clicks)
Avg CR   = SUM(Conversions) / SUM(Clicks)
Avg CPA  = SUM(Cost) / SUM(Conversions)
Avg ROAS = SUM(Revenue) / SUM(Cost)
```

Never use simple AVERAGE() across rows — it distorts results when row volumes differ.

**CRITICAL: Benchmarks are calculated per CAMPAIGN, not per ad group.** All keywords within a campaign are compared to the same campaign-level benchmark. This ensures that expensive keywords in one ad group are correctly flagged when cheaper keywords in other ad groups bring conversions at lower cost.

**Clustering exception:** Only apply clustering when one campaign contains fundamentally different PRODUCT CATEGORIES (e.g., "Cars" and "Car Accessories" in the same campaign — these have inherently different CPA/CR). In this case, cluster by product category, NOT by ad group. Most campaigns do NOT need clustering — use campaign-level benchmarks by default.

### Step 4: Evaluate ad groups (campaign-level insight)

**Before classifying individual keywords**, aggregate statistics per ad group and compare ad groups to each other within each campaign. This provides campaign-level insights:

For each ad group, calculate: total Clicks, Cost, Conversions, CPA (or ROAS), CTR. Then compare to the campaign benchmark.

| Ad group performance | Recommendation |
|---|---|
| All metrics significantly better than campaign avg (>30%) | ★ Ad group performing well — consider scaling (increase budget/bids) |
| Metrics within ±30% of campaign avg | ● Ad group is average — maintain |
| All metrics significantly worse AND sufficient data | ✖ Consider pausing the ENTIRE ad group |
| Most keywords in ad group are ⚠ WATCH (insufficient data) | Needs more time — do not pause |
| One keyword drags the whole group down | Pause that specific keyword, not the group |

This ad group analysis is reported on the RECAP sheet as an additional insight block, giving the marketer a full picture: should they pause the whole group, specific keywords, or specific search terms.

### Step 5: Classify keywords

Read `references/decision-algorithms.md` for the complete decision trees per campaign type.

Apply the appropriate algorithm (e-commerce / conversion / traffic) to every keyword. Assign one of these categories:

| Symbol | Category | Meaning |
|---|---|---|
| ★ | SCALE | Outstanding performance — increase bids, expand |
| ● | KEEP | Average performance — maintain, monitor |
| ▲ | OPTIMIZE | Below average — lower bids, check ads/landing pages |
| ⚠ | WATCH | Insufficient data or borderline — monitor closely |
| ✖ | PAUSE | Poor performance with sufficient data — stop |

The core logic for all types: compare each KW's metrics to **campaign-level benchmarks** (or cluster benchmarks if clustering is applied) using ±30% thresholds. Also check data sufficiency (min 10 clicks for traffic metrics, min 30 clicks + 5 conversions for conversion metrics). For KWs with zero conversions, compare Cost to Avg CPA to decide severity.

**Best-case test (mandatory for < 5 conversions with extreme deviation):** Before assigning ▲ OPTIMIZE to a KW with few conversions and poor performance, run the appropriate best-case test:
- **Conversion campaigns (CPA test):** `best_case_cpa = (Cost + (5 - Conv) × Avg CPA) / 5`. If result > Avg CPA × 1.5 → ✖ PAUSE.
- **E-commerce campaigns (ROAS test):** `best_case_roas = (Revenue + (5 - Conv) × Avg Rev/Conv) / (Cost + (5 - Conv) × Avg CPA)`. If result < Avg ROAS × 0.7 → ✖ PAUSE.

See `references/decision-algorithms.md` for full formulas, examples, and decision thresholds.

**Zero-conversion comment rule:** When Conv = 0, never output a computed or fallback CPA value. Use cost-based phrasing only: "0 conversions, spent $X (= N× avg CPA)".

**CRITICAL: Keywords are NEVER recommended for negation.** Keywords were intentionally added to the campaign by the advertiser. If a keyword is underperforming, the recommendation is to PAUSE it (disable), OPTIMIZE it (lower bid, improve ad/landing page), or WATCH it (gather more data). Negative keywords are ONLY generated from search terms (Step 6).

### Step 6: Classify search terms

Search terms are analyzed with the same algorithm as keywords (Step 5), with two critical differences:

1. **You cannot pause a search term** — if it's ineffective, you must add it as a negative keyword.
2. **All search terms are analyzed**, including those already "Added" as keywords.

For each search term:
- Run the same performance classification (★/●/▲/⚠/✖) as for keywords.
- If classified ✖ PAUSE: check relevance against the client's website.
  - **Irrelevant** → identify the trigger word and add as Broad or Phrase negative.
  - **Relevant but ineffective** → **first check if the ST matches an existing KW in the same campaign** (exact match or close variant). If it matches → do NOT negate; instead ensure the corresponding KW is flagged for pause/optimize on the Keywords sheet. If it does NOT match any KW → add the entire search term as an Exact negative.
- If classified ★ SCALE and not yet added as KW → recommend adding (only if results are outstanding — see reference file for thresholds).

**IMPORTANT: If the file contains ONLY keywords (no search terms sheet), do NOT generate negative keyword recommendations.** Negative keywords are derived exclusively from search term analysis. Without search terms data, the RECAP sheet should contain only keyword pause/optimize recommendations and ad group insights.

### Step 7: Build negative keyword list

**This step is ONLY executed when search terms data is present in the file.**

Read `references/negative-keywords.md` for match type mechanics and best practices.

Key rules:
- **Before adding ANY search term as a negative, verify it does not match an existing keyword in the same campaign.** If it matches (exact or close variant) → do not negate, redirect the recommendation to the KW (pause/optimize).
- Extract the specific **trigger word** that makes a query irrelevant — never negate the whole phrase.
- Negative match types work differently from regular KW match types (no synonyms, no close variants, no stemming). Add all word forms manually.
- Choose Broad negative for universally irrelevant words, Phrase for order-dependent phrases, Exact for specific ineffective search terms that do not match any KW.
- Choose the right level: account (shared list) for universal negatives, campaign for type-specific, ad group for group-specific.
- Always verify: will this negative block any useful queries in other groups/campaigns?

### Step 8: Generate the output file

Read `references/output-format.md` for the complete file specification.

Create an xlsx file with these sheets (in this order):

**Sheet 1 — "RECAP"** (opens first, most important):
- Summary block at top: total KWs/STs analyzed, campaign types, count of actions, estimated savings.
- Block A: Ad group performance comparison (group, campaign, aggregated metrics, category, recommendation). This gives the marketer a top-level view before diving into individual keywords.
- Block B: Keywords to pause/optimize (KW, campaign, ad group, action, cost, reason).
- Block C: Negative keywords to add — **ONLY if search terms data is present** (word, match type, level, campaign, reason, affected queries, priority).
- Block D: Search terms to add as KW — **ONLY if search terms data is present** (term, match type, target campaign/group, justification, stats).

**Sheet 2 — "Keywords — Analysis"**:
- All original data columns preserved.
- Added columns: Avg CTR, Avg CPC, Avg CR, Avg CPA, Avg ROAS, Category (★/●/▲/⚠/✖), Action, Comment.
- Conditional formatting: green (#C6EFCE) for >30% better, orange (#FDE9D9) for 15-30% worse, red (#FFC7CE) for >30% worse.

**Sheet 3 — "Search Terms — Analysis"** (only if search terms data is present):
- Same structure + Relevance column (✅/❌/❓), Negative word, Negative match type.

**Sheet 4 — "Benchmarks"** (optional, for complex accounts):
- Weighted averages per campaign/cluster with totals.
- Ad group aggregated performance within each campaign.

Formatting: Arial 10pt, headers white-on-navy (#1F4E79), freeze top row + first 3-4 columns, auto-filters on all data sheets, proper number formats (%, currency, multiplier).

### Step 9: Deliver the result

After building the file:

1. Check if Google Drive integration is available (search for Google Drive tools).
2. If available, offer choice: "I can save this as an Excel file for download, or upload it to your Google Drive. Which do you prefer?"
3. If not available, save the xlsx to `/mnt/user-data/outputs/` and present it.

File naming: `[ClientName]_KW_Analysis_[YYYY-MM-DD].xlsx`

## Important Rules

- **Keywords are NEVER negated.** Keywords were added to the campaign intentionally. If underperforming → pause or optimize. Negative keywords are generated ONLY from search term data. If the file contains only keywords, there are NO negative keyword recommendations.
- **Benchmarks are calculated at the CAMPAIGN level by default.** All keywords within a campaign share the same benchmark. Do NOT calculate separate benchmarks per ad group — this masks underperformance. Clustering into sub-benchmarks is ONLY justified when a campaign mixes fundamentally different product categories.
- **Ad group performance is evaluated as a separate insight**, comparing groups to each other within the campaign. This goes on the RECAP sheet to help the marketer decide: pause the whole group, specific keywords, or specific search terms.
- **Branded vs Non Branded vs Competitor campaigns are NEVER compared to each other.** Each has its own benchmarks.
- **DSA campaigns have no keywords** — analyze only their search terms, with extra scrutiny on relevance.
- **Fractional conversions are normal** (attribution models). Do not round. Treat 0.3 conversions as insufficient data.
- **Seasonality caveat:** if data covers < 3 months, note that seasonal KWs may look artificially bad/good.
- **When in doubt about relevance, flag for manual review** rather than auto-negating. The cost of wrongly negating a good query is higher than letting a bad one run a bit longer.
