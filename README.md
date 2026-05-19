# 🔍 SEM Keyword & Search Term Analyzer

**Claude skill for deep analysis of Google Ads / Microsoft Ads keyword and search term performance.**

Upload your campaign stats → get back a formatted Excel report with every keyword and search term classified, negative keyword recommendations, and a ready-to-act RECAP sheet.

`v1.0` · by [ipotekhin](https://github.com/ipotekhin) · MIT License

---

## 📊 What It Does

You upload an xlsx or csv export from Google Ads, Microsoft Ads (Bing), or any other SEM platform. The skill reads the file, studies the client's website, calculates campaign benchmarks, and returns an Excel file with four sheets:

### Sheet 1 — 📋 RECAP (opens first)
The most important sheet. Everything is ready to copy-paste into Google Ads Editor or the platform UI:

- **Ad group performance overview** — which ad groups to scale, which to pause, which have one bad keyword dragging them down
- **Keywords to pause / optimize** — sorted by wasted spend, with the exact reason for each
- **Negative keywords to add** — with match type (Broad / Phrase / Exact), level (Account / Campaign / Ad Group), trigger word, and affected queries
- **Search terms to promote to keywords** — only truly outstanding performers
- **Estimated savings** — how much budget you'll recover

### Sheet 2 — 🔑 Keywords Analysis
Your original data preserved as-is, plus added columns to the right:

- Weighted average benchmarks per campaign (Avg CTR, Avg CPC, Avg CR, Avg CPA, Avg ROAS)
- Category for each keyword: ★ Scale · ● Keep · ▲ Optimize · ⚠ Watch · ✖ Pause
- Specific action recommendation and detailed comment
- Color-coded cells: 🟢 green = 30%+ better than avg, 🟠 orange = 15–30% worse, 🔴 red = 30%+ worse

### Sheet 3 — 🔎 Search Terms Analysis
Same structure as Keywords, plus:

- Relevance assessment (✅ Relevant / ❌ Irrelevant / ❓ Needs review) — based on the client's website
- Negative keyword recommendation with the specific trigger word and match type
- Protection: search terms that match existing keywords are never negated (the KW gets a pause recommendation instead)

### Sheet 4 — 📈 Benchmarks
Weighted averages per campaign (and per product cluster if applicable). Shows total impressions, clicks, cost, conversions, revenue, and all calculated averages.

---

## ⚙️ How It Works — Step by Step

| Step | What happens |
|---|---|
| 🌐 **Website check** | Asks for the client's website — won't proceed without it. Studies what the business sells, B2B/B2C, geography, languages |
| 📂 **File reading** | Opens xlsx/csv, detects Keywords vs Search Terms sheets, identifies available metrics (CTR, CPC, CPA, ROAS, etc.), validates data |
| 🏷️ **Campaign typing** | Determines each campaign's type — E-commerce (ROAS), Conversion (CPA), or Traffic (CTR) — automatically based on available metrics |
| 🏢 **Campaign classification** | Labels each campaign as Branded / Non Branded / Competitor / DSA. Never compares them to each other |
| 📐 **Benchmark calculation** | Calculates weighted averages at the campaign level (not per ad group). Clusters only when a campaign mixes fundamentally different product categories |
| 👥 **Ad group evaluation** | Aggregates stats per ad group, compares groups to each other within the campaign. Identifies strong groups to scale and weak groups to pause |
| 🔑 **Keyword classification** | Runs each keyword through a decision tree (5 scenarios per campaign type), checks data sufficiency, applies ±30% thresholds. Uses best-case test for borderline cases with few conversions |
| 🔎 **Search term classification** | Same algorithm as keywords + relevance check against the website. Verifies that negatives don't conflict with existing keywords |
| ⛔ **Negative keyword list** | Extracts trigger words (not whole phrases), picks the right match type (accounts for how negative match types actually work — no synonyms, no stemming), assigns the right level |
| 📊 **Excel generation** | Builds the formatted xlsx with all 4 sheets, color coding, frozen panes, auto-filters, proper number formats |
| 📤 **Delivery** | Download as xlsx, or upload to Google Drive if the integration is connected |

---

## 🚀 Installation

### Option A — Send the GitHub link to Claude (easiest)

1. Open a Claude Project where you want to use the skill.
2. Paste the repository URL into the chat:
   > Here's a skill I'd like to install: https://github.com/ipotekhin/sem-kw-analyzer
   > Please download and install it as a project skill.
3. Claude will fetch the files and set up the skill in your project.

### Option B — Download and upload manually

1. Click the green **Code** button on this repo → **Download ZIP**.
2. Open your Claude Project → go to **Project Knowledge**.
3. Upload the ZIP file (or the individual files from the `sem-kw-analyzer/` folder).
4. Tell Claude:
   > I've uploaded a skill. Please install it from the uploaded files.

### 💡 Important
The skill source of truth lives in this GitHub repo. There is no separate `.skill` release file — always grab the latest version from the repo.

---

## 💬 Usage

In a Claude conversation (with the skill installed), attach your xlsx/csv file and ask:

> Analyze this keyword report and tell me what to pause, what to scale, and what negative keywords to add.

The skill will ask for the client's website URL first, then run the full analysis.

### Example prompts

- *"Here's our Google Ads search term report for Q1. Find wasteful spend and negative keywords."*
- *"Review these Microsoft Ads keywords — which ones should I pause?"*
- *"Analyze this SQR export. I need a list of negatives and underperforming keywords."*
- *"I uploaded keyword stats from our Bing campaign. What's working and what's not?"*
- *"Find negative keywords in this file. Our website is example.com."*

---

## 🧠 Key Features

| Feature | Details |
|---|---|
| 🌍 **Universal** | Works with Google Ads, Microsoft Ads / Bing, and any SEM platform with standard KW/ST exports |
| 📐 **Campaign-level benchmarks** | Weighted averages calculated per campaign — not per ad group (prevents masking underperformers) |
| 🧮 **Best-case test** | For keywords with few conversions and high CPA — mathematically checks if optimization can recover them, or if pause is the only option |
| ⛔ **Negative match types done right** | Understands that negative Broad/Phrase/Exact work differently from regular keywords (no synonyms, no stemming, no close variants). Adds all word forms manually |
| 🔒 **KW protection** | Never negates a search term that matches an existing keyword — recommends pausing the KW instead |
| 🏷️ **Smart clustering** | Only clusters when a campaign mixes truly different product categories (cars vs accessories), not just because ad groups have different CR |
| 📋 **RECAP-first output** | The most actionable sheet opens first, designed for fast copy-paste into Google Ads Editor |
| 0️⃣ **Honest zero-conv handling** | Never shows fake CPA when conversions = 0. Uses cost-based phrasing: "0 conv, spent $X (= N× avg CPA)" |

---

## 📁 Skill Structure

```
sem-kw-analyzer/
├── SKILL.md                              # Main workflow (9 steps)
└── references/
    ├── data-detection.md                 # How to identify KW vs ST sheets, detect metrics
    ├── benchmarks-and-thresholds.md      # Weighted avg formulas, clustering rules, ad group comparison
    ├── decision-algorithms.md            # Decision trees for E-commerce / Conversion / Traffic
    ├── negative-keywords.md              # Negative match type mechanics, KW protection, checklist
    └── output-format.md                  # Excel file spec: 4 sheets, formatting, color codes
```

Claude reads `SKILL.md` as the main workflow and loads each reference file only at the step where it's needed — keeping context efficient.

---

## 📥 Supported Inputs

| Format | Status |
|---|---|
| xlsx (Excel) | ✅ Supported |
| csv | ✅ Supported |
| Single sheet (Keywords only) | ✅ Supported |
| Single sheet (Search Terms only) | ✅ Supported |
| Both sheets (KW + ST) | ✅ Supported |
| Non-English column names | ✅ Common variants auto-detected |

---

## 📏 Metrics Handled

| Metric | Campaign type |
|---|---|
| Impressions, Clicks, CTR | All |
| CPC, Cost | All |
| Conversions, CR, CPA | Conversion + E-commerce |
| Revenue, ROAS | E-commerce |
| Impression Share, Quality Score | Used when available |

---

## 🏷️ Decision Framework

Every keyword and search term is classified into one of five categories:

| Symbol | Category | What it means | What to do |
|---|---|---|---|
| ★ | **Scale** | 30%+ better than campaign average | Increase bids, expand match types |
| ● | **Keep** | Within ±15% of average | Monitor, maintain current settings |
| ▲ | **Optimize** | 15–30%+ worse than average | Lower bids, test new ads/landing pages |
| ⚠ | **Watch** | Not enough data to decide | Do nothing yet, wait for more clicks |
| ✖ | **Pause** | Significantly underperforming with enough data | Stop the keyword or negate the search term |

---

## 🤝 Contributing

Issues and pull requests are welcome. To extend the skill (new SEM platform formats, additional metrics, localization), check the `references/` folder for the patterns used.

---

## 👤 Author

**ipotekhin** — [GitHub](https://github.com/ipotekhin)

## 📄 License

MIT
