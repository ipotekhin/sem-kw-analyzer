---
name: sem-kw-analyzer
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

Calculate **weighted averages** per campaign (and per cluster if needed):

```
Avg CTR  = SUM(Clicks) / SUM(Impressions)
Avg CPC  = SUM(Cost) / SUM(Clicks)
Avg CR   = SUM(Conversions) / SUM(Clicks)
Avg CPA  = SUM(Cost) / SUM(Conversions)
Avg ROAS = SUM(Revenue) / SUM(Cost)
```

Never use simple AVERAGE() across rows — it distorts results when row volumes differ.

**Clustering:** If ad groups within one campaign cover fundamentally different products (e.g., phones vs refrigerators), calculate benchmarks per cluster, not per campaign. Check if StdDev(CR across groups) / Mean(CR) > 0.5 — if so, cluster. See reference file for details.

### Step 4: Classify keywords

Read `references/decision-algorithms.md` for the complete decision trees per campaign type.

Apply the appropriate algorithm (e-commerce / conversion / traffic) to every keyword. Assign one of these categories:

| Symbol | Category | Meaning |
|---|---|---|
| ★ | SCALE | Outstanding performance — increase bids, expand |
| ● | KEEP | Average performance — maintain, monitor |
| ▲ | OPTIMIZE | Below average — lower bids, check ads/landing pages |
| ⚠ | WATCH | Insufficient data or borderline — monitor closely |
| ✖ | PAUSE | Poor performance with sufficient data — stop |

The core logic for all types: compare each KW's metrics to campaign benchmarks using ±30% thresholds. Also check data sufficiency (min 10 clicks for traffic metrics, min 30 clicks + 5 conversions for conversion metrics). For KWs with zero conversions, compare Cost to Avg CPA to decide severity.

### Step 5: Classify search terms

Search terms are analyzed with the same algorithm as keywords (Step 4), with two critical differences:

1. **You cannot pause a search term** — if it's ineffective, you must add it as a negative keyword.
2. **All search terms are analyzed**, including those already "Added" as keywords.

For each search term:
- Run the same performance classification (★/●/▲/⚠/✖) as for keywords.
- If classified ✖ PAUSE: check relevance against the client's website.
  - **Irrelevant** → identify the trigger word and add as Broad or Phrase negative.
  - **Relevant but ineffective** → add the entire search term as an Exact negative.
- If classified ★ SCALE and not yet added as KW → recommend adding (only if results are outstanding — see reference file for thresholds).

### Step 6: Build negative keyword list

Read `references/negative-keywords.md` for match type mechanics and best practices.

Key rules:
- Extract the specific **trigger word** that makes a query irrelevant — never negate the whole phrase.
- Negative match types work differently from regular KW match types (no synonyms, no close variants, no stemming). Add all word forms manually.
- Choose Broad negative for universally irrelevant words, Phrase for order-dependent phrases, Exact for specific ineffective search terms.
- Choose the right level: account (shared list) for universal negatives, campaign for type-specific, ad group for group-specific.
- Always verify: will this negative block any useful queries in other groups/campaigns?

### Step 7: Generate the output file

Read `references/output-format.md` for the complete file specification.

Create an xlsx file with these sheets (in this order):

**Sheet 1 — "RECAP"** (opens first, most important):
- Summary block at top: total KWs/STs analyzed, campaign types, count of actions, estimated savings.
- Table A: Negative keywords to add (word, match type, level, campaign, reason, affected queries, priority).
- Table B: Search terms to add as KW (term, match type, target campaign/group, justification, stats).
- Table C: Keywords to pause/optimize (KW, campaign, action, cost, reason).

**Sheet 2 — "Keywords — Analysis"**:
- All original data columns preserved.
- Added columns: Avg CTR, Avg CPC, Avg CR, Avg CPA, Avg ROAS, Category (★/●/▲/⚠/✖), Action, Comment.
- Conditional formatting: green (#C6EFCE) for >30% better, orange (#FDE9D9) for 15-30% worse, red (#FFC7CE) for >30% worse.

**Sheet 3 — "Search Terms — Analysis"**:
- Same structure + Relevance column (✅/❌/❓), Negative word, Negative match type.

**Sheet 4 — "Benchmarks"** (optional, for complex accounts):
- Weighted averages per campaign/cluster with totals.

Formatting: Arial 10pt, headers white-on-navy (#1F4E79), freeze top row + first 3-4 columns, auto-filters on all data sheets, proper number formats (%, currency, multiplier).

### Step 8: Deliver the result

After building the file:

1. Check if Google Drive integration is available (search for Google Drive tools).
2. If available, offer choice: "I can save this as an Excel file for download, or upload it to your Google Drive. Which do you prefer?"
3. If not available, save the xlsx to `/mnt/user-data/outputs/` and present it.

File naming: `[ClientName]_KW_Analysis_[YYYY-MM-DD].xlsx`

## Important Rules

- **Branded vs Non Branded vs Competitor campaigns are NEVER compared to each other.** Each has its own benchmarks.
- **DSA campaigns have no keywords** — analyze only their search terms, with extra scrutiny on relevance.
- **Fractional conversions are normal** (attribution models). Do not round. Treat 0.3 conversions as insufficient data.
- **Seasonality caveat:** if data covers < 3 months, note that seasonal KWs may look artificially bad/good.
- **When in doubt about relevance, flag for manual review** rather than auto-negating. The cost of wrongly negating a good query is higher than letting a bad one run a bit longer.
