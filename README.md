# SEM Keyword & Search Term Analyzer

A Claude skill that analyzes keyword and search term performance data from SEM platforms (Google Ads, Microsoft Ads / Bing, and others), classifies every entry by effectiveness, generates negative keyword lists, and produces a formatted Excel report with ready-to-use action items.

## What It Does

Upload a keyword or search term report from any SEM platform → get back an Excel file with:

- **RECAP sheet** — copy-paste-ready tables: negative keywords to add, search terms to promote, keywords to pause. Sorted by priority, with estimated savings.
- **Keywords — Analysis** — your original data + benchmarks, category (★ Scale / ● Keep / ▲ Optimize / ⚠ Watch / ✖ Pause), action, and color-coded cells.
- **Search Terms — Analysis** — same treatment + relevance assessment, negative keyword recommendations with match types.
- **Benchmarks** — weighted averages per campaign and cluster.

## How It Works

1. **Asks for the client's website** — essential for relevance decisions. The skill won't proceed without it.
2. **Reads the uploaded file** (xlsx or csv) — detects Keywords vs Search Terms sheets, identifies available metrics, validates data.
3. **Determines campaign type** — E-commerce (ROAS-focused), Conversion (CPA-focused), or Traffic (CTR-focused) — per campaign, automatically.
4. **Calculates weighted benchmarks** — per campaign, with automatic clustering when ad groups cover different product categories.
5. **Classifies every keyword** — using decision trees with 5 scenarios per campaign type, data sufficiency checks, and ±30% deviation thresholds.
6. **Classifies every search term** — same algorithm as keywords, plus relevance check against the client's website.
7. **Builds negative keyword list** — extracts trigger words (not whole phrases), selects correct match type (Broad / Phrase / Exact), assigns level (Account / Campaign / Ad Group).
8. **Generates the Excel report** — formatted, color-coded, with frozen panes and auto-filters.
9. **Delivers** — download as xlsx or upload to Google Drive (if integration is connected).

## Key Features

- **Universal** — works with Google Ads, Microsoft Ads, and any SEM platform that exports standard KW/ST reports.
- **Negative keyword match types done right** — the skill understands that negative match types work differently from regular keywords (no synonyms, no stemming, no close variants) and accounts for this.
- **Smart clustering** — automatically detects when a single campaign contains fundamentally different product groups and calculates separate benchmarks.
- **Search terms analyzed for both relevance AND performance** — irrelevant terms get trigger-word negation; relevant-but-ineffective terms get Exact negation.
- **RECAP-first output** — the most actionable sheet opens first, designed for quick copy-paste into Google Ads Editor.

## Installation

### Option 1: Claude Project (recommended)

1. Download `sem-kw-analyzer.skill` from the [Releases](../../releases) page.
2. In Claude, go to your Project → Project Knowledge → Upload the `.skill` file.

### Option 2: Manual setup

1. Clone or download this repository.
2. Copy the `sem-kw-analyzer/` folder to your Claude skills directory.

## Usage

In a Claude conversation (with the skill installed):

> Here's our Google Ads keyword report for Q1. Can you analyze it and find negative keywords?

Attach your xlsx/csv file. The skill will ask for the client's website URL, then run the full analysis.

### Example prompts

- *"Analyze this search term report and find wasteful spend"*
- *"Review my Microsoft Ads keywords — which ones should I pause?"*
- *"Find negative keywords in this SQR export"*
- *"I uploaded our PPC keyword data — what's underperforming?"*

## Skill Structure

```
sem-kw-analyzer/
├── SKILL.md                                  # Core workflow (8 steps)
└── references/
    ├── data-detection.md                     # File structure identification rules
    ├── benchmarks-and-thresholds.md          # Formulas, deviation thresholds, clustering
    ├── decision-algorithms.md                # Decision trees for 3 campaign types
    ├── negative-keywords.md                  # Match type mechanics, categories, checklist
    └── output-format.md                      # Excel output specification (4 sheets)
```

The SKILL.md contains the main workflow and points to reference files as needed. Claude loads only the relevant reference file at each step, keeping context efficient.

## Supported Input Formats

| Format | Supported |
|---|---|
| xlsx (Excel) | ✅ |
| csv | ✅ |
| Single sheet (KW or ST) | ✅ |
| Multiple sheets (KW + ST) | ✅ |
| Non-English column names | ✅ (common variants detected) |

## Metrics Handled

| Metric | Used for |
|---|---|
| Impressions, Clicks, CTR | All campaign types |
| CPC, Cost | All campaign types |
| Conversions, CR, CPA | Conversion + E-commerce |
| Revenue, ROAS | E-commerce |
| Impression Share, Quality Score | Used if available |

## Decision Framework

Every keyword and search term is classified into one of five categories:

| Category | Symbol | Meaning | Action |
|---|---|---|---|
| Scale | ★ | Outstanding — significantly above benchmarks | Increase bids, expand |
| Keep | ● | Average — within normal range | Monitor, maintain |
| Optimize | ▲ | Below average — underperforming | Lower bids, test new ads |
| Watch | ⚠ | Insufficient data or borderline | Monitor, do not act yet |
| Pause | ✖ | Poor — significantly below benchmarks | Stop or negate |

## Contributing

Issues and pull requests are welcome. If you'd like to extend the skill (e.g., add support for a new SEM platform's export format), see the reference files for the patterns used.

## Author

**ipotekhin** — [GitHub](https://github.com/ipotekhin)

## License

MIT
