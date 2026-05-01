# Output File Format Specification

## File Structure

The output xlsx file contains 3-4 sheets in this order:

1. **RECAP — Actions** (always first — this is what the user opens)
2. **Keywords — Analysis** (if Keywords data was in the input)
3. **Search Terms — Analysis** (if Search Terms data was in the input)
4. **Benchmarks** (optional — include when there are multiple campaigns or clusters)

## Sheet 1: RECAP — Actions

### Layout

**Top section (rows 1-8): Summary block**
- Merged cells, Arial 14pt Bold for title, 11pt for data
- Background: light gray (#F2F2F2)
- Content:
  - Row 1: "RECAP — ANALYSIS RESULTS" (title)
  - Row 2: "Client: [name]" (extracted from campaign names or file name)
  - Row 3: "Period: [from input file name or data if available]"
  - Row 4: "Analyzed: X keywords, Y search terms"
  - Row 5: "Campaign types: Branded (N), Non Branded (N), DSA (N)"
  - Row 6: "Negative keywords to add: N | KWs to pause: N"
  - Row 7: "Search terms to add as KW: N | Estimated savings: $X"
  - Row 8: empty (spacer)

**Block A (starting ~row 10): Negative Keywords to Add**

Header row: dark navy background (#1F4E79), white text, Bold

| Column | Header | Width | Description |
|---|---|---|---|
| A | Negative Keyword | 25 | The word or phrase to negate |
| B | Match Type | 15 | Broad / Phrase / Exact |
| C | Level | 15 | Account / Campaign / Ad Group |
| D | Campaign | 30 | Target campaign (if level = campaign) |
| E | Ad Group | 25 | Target ad group (if level = ad group) |
| F | Reason | 35 | Brief explanation |
| G | Affected Queries | 40 | Which search terms this will block |
| H | Priority | 12 | 🔴 High / 🟠 Medium / 🟡 Low |

Sort: by Priority (🔴 first), then by Level (Account → Campaign → Ad Group).

**Block B (after Block A + 2 empty rows): Search Terms to Add as KW**

| Column | Header | Width | Description |
|---|---|---|---|
| A | Search Term | 35 | The query to add |
| B | Recommended Match Type | 20 | Exact / Phrase |
| C | Target Campaign | 30 | Where to add |
| D | Target Ad Group | 25 | Existing group or "create new" |
| E | Justification | 40 | Why recommended (metrics cited) |
| F | Current Stats | 30 | Key figures: Clicks/Conv/CPA or ROAS |

Sort: by performance (best first).

**Block C (after Block B + 2 empty rows): Keywords to Pause / Optimize**

| Column | Header | Width | Description |
|---|---|---|---|
| A | Keyword | 30 | The keyword |
| B | Campaign | 30 | Campaign name |
| C | Ad Group | 25 | Ad group name |
| D | Action | 18 | ✖ PAUSE / ▲ OPTIMIZE / ⚠ WATCH |
| E | Cost | 12 | Total spend |
| F | Conversions | 12 | Total conversions |
| G | CPA or ROAS | 12 | Relevant metric |
| H | Reason | 40 | Brief explanation |

Sort: by Action (✖ PAUSE first), then by Cost (highest first).

Each block has a section title row (merged cells, Arial 12pt Bold, colored left border).

## Sheet 2: Keywords — Analysis

### Structure

**Original columns (preserved exactly as in input file):**
All columns from the uploaded file, in their original order. No modifications to original data.

**Added columns (appended to the right, separated by an empty column):**

| Column | Header | Format | Description |
|---|---|---|---|
| — | (empty separator) | — | Visual separator between original and analysis |
| +1 | Avg CTR | 0.0% | Campaign/cluster weighted average CTR |
| +2 | Avg CPC | $#,##0.00 | Campaign/cluster weighted average CPC |
| +3 | Avg CR | 0.0% | Campaign/cluster weighted average CR |
| +4 | Avg CPA | $#,##0.00 | Campaign/cluster weighted average CPA |
| +5 | Avg ROAS | 0.00 | Campaign/cluster weighted average ROAS |
| +6 | Category | Text | ★ SCALE / ● KEEP / ▲ OPTIMIZE / ⚠ WATCH / ✖ PAUSE |
| +7 | Action | Text | Specific recommendation |
| +8 | Comment | Text (wrap) | Detailed explanation of why this category was assigned |

Only include Avg columns for metrics that exist in the input data.

### Conditional Formatting

Apply cell background color to EACH metric cell (CTR, CPC, CR, CPA, ROAS, Cost) based on comparison to the corresponding Avg column:

- **Green fill (#C6EFCE):** metric is >30% better than average
- **Orange fill (#FDE9D9):** metric is 15-30% worse than average
- **Red fill (#FFC7CE):** metric is >30% worse than average
- **No fill:** within ±15% of average

For "Category" column:
- ★ SCALE rows: green text (#006100)
- ✖ PAUSE rows: red text (#9C0006)
- ▲ OPTIMIZE rows: orange text (#9C6500)

### Formatting

- Freeze row 1 (headers) and first 3 columns (Campaign, AdGroup, Keyword)
- Enable auto-filters on header row
- Number formats:
  - CTR, CR: `0.0%`
  - CPC, CPA, Cost, Revenue: `$#,##0.00`
  - ROAS: `0.00`
  - Impressions, Clicks: `#,##0`
  - Conversions: `#,##0.0`

## Sheet 3: Search Terms — Analysis

Same structure as Keywords sheet, plus these additional columns:

| Column | Header | Description |
|---|---|---|
| +9 | Relevance | ✅ Relevant / ❌ Irrelevant / ❓ Needs Review |
| +10 | Negative Word | The specific word to negate (if applicable) |
| +11 | Neg. Match Type | Broad / Phrase / Exact (if applicable) |

## Sheet 4: Benchmarks (optional)

Include when there are ≥ 2 campaigns or clusters.

| Column | Header | Description |
|---|---|---|
| A | Campaign | Campaign name |
| B | Cluster / Ad Group | If clustering was applied |
| C | Type | Branded / Non Branded / DSA / Competitor |
| D | Focus | E-commerce / Conversion / Traffic |
| E | KW Count | Number of keywords |
| F | Total Impressions | SUM |
| G | Total Clicks | SUM |
| H | Total Cost | SUM |
| I | Total Conversions | SUM |
| J | Total Revenue | SUM |
| K | Avg CTR | Weighted |
| L | Avg CPC | Weighted |
| M | Avg CR | Weighted |
| N | Avg CPA | Weighted |
| O | Avg ROAS | Weighted |

## Global Formatting Rules

- **Font:** Arial, 10pt for data; 10pt Bold for headers
- **Header row:** background #1F4E79, text white, Bold
- **Section titles on RECAP:** Arial 12pt Bold
- **Column width:** auto-fit to content, min 8 chars, max 50 chars (with text wrap)
- **Borders:** thin light gray borders (#D9D9D9) on all data cells
- **Alignment:** numbers right-aligned, text left-aligned, headers center-aligned

## File Naming

Format: `[ClientName]_KW_Analysis_[YYYY-MM-DD].xlsx`

Extract client name from:
1. Campaign name (first meaningful segment, e.g., "Berner International" from "Interactive_Berner International_99_...")
2. File name of the uploaded file
3. Fallback: "Campaign_Analysis"

## Google Drive Upload

If the user chooses Google Drive delivery:
1. Create the xlsx file locally (identical to download version).
2. Upload via Google Drive MCP integration (create_file tool).
3. Report the file link to the user.
4. Recommend opening as Google Sheets for full functionality.
