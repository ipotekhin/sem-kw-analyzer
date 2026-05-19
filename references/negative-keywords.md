# Negative Keywords: Match Types, Rules, and Best Practices

## CRITICAL: Negative keywords come from SEARCH TERMS only

**Never recommend negating a keyword that the advertiser added to the campaign.** Keywords in the campaign were added intentionally. If a keyword underperforms, the correct action is to PAUSE or OPTIMIZE it — not to negate it.

Negative keyword recommendations are generated EXCLUSIVELY from search term analysis. If the uploaded file contains only keywords (no search terms), do NOT produce any negative keyword recommendations.

## CRITICAL: Never negate a search term that matches an existing keyword

**Before adding ANY search term as a negative keyword, check whether it matches (or closely matches) a keyword already in the same campaign.**

Check for:
- Exact match: ST text = KW text
- Close variants: same root words in different order, ±prepositions/articles, singular/plural

**If the search term matches an existing KW:**
- Do NOT add it as a negative keyword (this would block the advertiser's own keyword by a back door).
- Instead, check that the corresponding keyword is already classified as ✖ PAUSE or ▲ OPTIMIZE on the Keywords sheet.
- If it's not yet flagged — add the KW to the pause/optimize list with a note: "Identified via search term analysis: ST '[term]' matches this KW but shows poor performance."

**Exact negatives on search terms are ONLY valid when:**
1. The ST is **irrelevant** to the product AND does not match any existing KW → use trigger-word Broad/Phrase negative (not Exact on the full ST).
2. The ST is **relevant but ineffective** AND **differs from all existing KWs** in the campaign → Exact negative is appropriate.

## Core Rule

**Negate the specific trigger WORD, not the entire search query phrase.**

Process:
1. Take the irrelevant search term.
2. Compare it to the keyword that triggered it.
3. Identify the word(s) that make the search term irrelevant.
4. That word is the negative keyword candidate.

**Example:**
- KW: "buy air conditioner"
- Search term: "buy air conditioner for truck"
- Client sells residential AC, not automotive
- Negative keyword: `truck` (NOT "buy air conditioner for truck")
- Also consider: `car`, `vehicle`, `automotive` — preventive negatives following the same logic

## Match Types for Negative Keywords — DIFFERENT FROM REGULAR KW!

Negative keywords do NOT use close variants, synonyms, stemming, or spelling corrections. Only exact word matches are blocked.

### Negative Broad Match (default)

Ad will NOT show if the search contains **ALL** words of the negative keyword, in **any order**.
Ad WILL show if the search contains only SOME of the words.

**Example:** negative keyword `running shoes` (broad)

| Search query | Ad shows? | Why |
|---|---|---|
| blue tennis shoes | ✅ Yes | Contains only "shoes", not "running" |
| running shoe | ✅ Yes | "shoe" ≠ "shoes" — no stemming for negatives |
| blue running shoes | ❌ No | Contains both "running" and "shoes" |
| shoes running | ❌ No | Both words present (order doesn't matter) |
| running shoes | ❌ No | Exact match |

### Negative Phrase Match

Ad will NOT show if the search contains the negative keyword words **in the same order**. Extra words before/after are fine.
If a word has additional characters, the ad WILL still show ("shoe" ≠ "shoes").

**Example:** negative keyword `"running shoes"` (phrase)

| Search query | Ad shows? | Why |
|---|---|---|
| blue tennis shoes | ✅ Yes | Doesn't contain "running shoes" as a phrase |
| running shoe | ✅ Yes | "shoe" ≠ "shoes" |
| blue running shoes | ❌ No | Contains "running shoes" in correct order |
| shoes running | ✅ Yes | Word order is different |
| running shoes | ❌ No | Exact phrase match |

### Negative Exact Match

Ad will NOT show if the search contains **exactly the same words, in the same order, with no extra words**.

**Example:** negative keyword `[running shoes]` (exact)

| Search query | Ad shows? | Why |
|---|---|---|
| blue tennis shoes | ✅ Yes | Different words |
| running shoe | ✅ Yes | "shoe" ≠ "shoes" |
| blue running shoes | ✅ Yes | Extra word "blue" present |
| shoes running | ✅ Yes | Different word order |
| running shoes | ❌ No | Exact match |

### Key Differences from Regular Keywords

| Behavior | Regular KW | Negative KW |
|---|---|---|
| Close variants (synonyms) | ✅ Used | ❌ NOT used |
| Word forms (shoe/shoes) | ✅ Used | ❌ NOT used |
| Typos | ✅ Used | ❌ NOT used |
| Related words / stemming | ✅ Used | ❌ NOT used |

**Practical consequence:** when negating, you MUST manually add all word forms. If negating "truck", also add "trucks". For languages with complex inflection (Russian, German, etc.), add all relevant cases/forms.

## When to Use Which Match Type

| Situation | Match type | Example |
|---|---|---|
| One irrelevant word that should never appear | Broad negative | `truck` — blocks any query containing "truck" |
| Irrelevant multi-word phrase in specific order | Phrase negative | `"for trucks"` — blocks "AC for trucks" but not "trucks for AC" |
| Specific ineffective search term to block surgically | Exact negative | `[buy cheap air conditioner free shipping]` — only this exact query |
| Relevant-but-ineffective search term (from Section 5.2 of main algorithm) | Exact negative | Always Exact — to avoid collateral damage to useful queries |

## Negative Keyword Levels

| Level | When to use |
|---|---|
| Account (shared negative list) | Universally irrelevant words: jobs, career, free, DIY, wiki, salary |
| Campaign | Words irrelevant to a specific campaign type (e.g., "residential" in a B2B campaign) |
| Ad group | Words irrelevant to one group but relevant in other groups of the same campaign |

## Common Negative Keyword Categories

| Category | Examples | Match type | Level |
|---|---|---|---|
| Wrong language | Words in unexpected language | Broad | Campaign/Account |
| Wrong product/segment | "residential" for B2B company | Broad | Campaign |
| Wrong geography | "canada" if not served | Phrase | Campaign |
| Informational intent | "photos", "reviews", "wiki", "what is" | Broad | Account |
| DIY / self-repair | "DIY", "how to make", "homemade" | Broad | Account |
| Jobs/careers | "jobs", "career", "salary", "hiring" | Broad | Account |
| Free/cheap | "free", "download", "torrent" | Broad | Account |
| Competitors (if not targeted) | Competitor brand names | Phrase/Exact | Campaign |
| B2C vs B2B mismatch | "for home" if B2B-only product | Broad | Campaign |

## Pre-Negation Checklist

1. ✅ Identified the specific trigger word (not the whole phrase).
2. ✅ Verified it won't block useful queries in other groups/campaigns.
3. ✅ Chose the right match type (Broad is powerful but risky — can over-block).
4. ✅ Chose the right level (account, campaign, or group).
5. ✅ If uncertain about relevance — flagged for manual review instead of auto-negating.
6. ✅ Checked the client's website: does the client sell this product/service type?
7. ✅ Added all necessary word forms (plurals, cases) since negatives don't stem.
