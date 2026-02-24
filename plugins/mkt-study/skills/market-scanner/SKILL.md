---
name: market-scanner
description: "Scan and map your market landscape — size, structure, trends, and seasonality using Perplexity. Use when user mentions: market research, market size, market structure, market trends, market analysis, market landscape, market scan, market mapping, TAM, total addressable market, market conditions, market demand, seasonality, market dynamics, market overview, market forces, macro trends, 시장 조사, 시장 분석, 시장 규모, 시장 트렌드, 시장 구조, 시장 스캔"
user-invocable: true
---

# Market Scanner

> Scan and map your market landscape — size, structure, trends, and seasonality.
> Uses Perplexity for deep market research. Output feeds every downstream research and marketing skill.

---

## Purpose

Market Scanner is the **starting point** of all research. It answers:
- What market are we actually in? (precise category, not vague industry)
- How big is it, and where is it headed?
- What macro forces are shaping it?
- How is the market structured?
- When does demand peak and trough?

The output — `research-memory/market-landscape.md` — becomes the **shared context** that every subsequent skill (competitor-finder, audience-profiler, brand-voice, SEO, copy, etc.) reads to stay grounded in market reality.

> "Doing marketing without research is like driving without a map." — The Boring Marketer

---

## Modes

| Mode | When to Use | Behavior |
|------|-------------|----------|
| **Full Scan** | No `market-landscape.md` exists, or it's an empty scaffold | Run all 5 steps from scratch |
| **Refresh** | `market-landscape.md` already has data | Check `research-log.md` for last scan date → update only changed sections |

---

## Auto-Load Protocol

On every invocation, BEFORE any research:

1. **Check `research-memory/` directory**
2. If files exist → Read ALL `.md` files (except README.md)
3. Use loaded context to:
   - Skip already-covered areas (avoid redundancy)
   - Build on existing findings (deepen, not repeat)
   - Maintain consistency with other research outputs
4. **Check `brand-memory/`** (read-only) → If exists, use business description, positioning, and audience info as input context
5. If `market-landscape.md` has data AND `research-log.md` shows a previous scan → **suggest Refresh mode**

---

## Input Gathering

Collect from the user conversationally. Do NOT dump a form — ask naturally.

| Field | Required | Description |
|-------|----------|-------------|
| Business / Product description | YES | What does the company/product do? |
| Industry / Category | YES | What market does it operate in? |
| Current status | Optional | Revenue range, growth stage, geography |
| Research focus | Optional | Specific area of interest (trends, size, structure, etc.) |
| Known competitors | Optional | Helps triangulate market boundaries |

**If brand-memory/ exists**, pre-fill from `voice-profile.md` and `positioning.md` — ask user to confirm or correct.

**If this is a Refresh**, show the current market-landscape.md summary and ask: "What has changed or what do you want to update?"

---

## Process

### Step 1: Define Market Category

**Goal**: Pin down the PRECISE market category — not "software" but "AI-powered marketing automation for SMBs."

**Tool**: `perplexity_reason` (requires analytical reasoning)

**Query pattern**:
```
What is the precise market category and sub-segment for a business that [user's business description]?

Define:
- The specific market segment (not broad industry)
- Adjacent segments that partially overlap
- Where the boundaries are (what's IN vs OUT of this market)

Be specific: "DTC home textiles" not "home goods"
```

**Parameters**:
- `search_context_size`: "high"

**Output**: Category name, scope definition, boundary clarification, adjacent markets.

---

### Step 2: Market Size & Growth (TAM / SAM)

**Goal**: Quantify the market opportunity with credible numbers.

**Tool**: `perplexity_reason` (numerical reasoning required)

**Query pattern**:
```
What is the current market size for [market category from Step 1]?

Provide:
1. TAM (Total Addressable Market) — global value
2. SAM (Serviceable Addressable Market) — the realistic slice for [business type/geography]
3. CAGR projection for the next 3-5 years
4. Key regional breakdown (if relevant)

For each number: cite the source name and year of data.
If multiple sources conflict, note the range.
```

**Parameters**:
- `search_context_size`: "high"
- `search_recency_filter`: "year"

**Output**: TAM, SAM, CAGR, regional split — all with source citations.

---

### Step 3: Macro Trends + Opportunity/Threat Tagging

**Goal**: Identify 3-5 macro forces shaping this market, and tag each as opportunity or threat.

**Tool**: `perplexity_ask` (fact-based, current data)

**Query pattern**:
```
What are the top 3-5 macro trends shaping the [market category] market in [current year]?

For each trend, cover:
- Category: consumer behavior / technology / regulation / economic / cultural
- Description: what's happening and why it matters
- Impact on a [business type]: is this an OPPORTUNITY or THREAT?
- Timeframe: immediate (0-1yr), near-term (1-3yr), or long-term (3-5yr)
- Evidence: cite recent data points or examples
```

**Parameters**:
- `search_recency_filter`: "month"
- `search_context_size`: "high"

**Output**: 3-5 trends, each tagged with category, opportunity/threat, timeframe, and evidence.

---

### Step 4: Market Structure Map + Seasonality

**Goal**: Understand how the market is organized and when demand peaks/troughs.

**Tool**: `perplexity_ask`

**Query pattern**:
```
How is the [market category] market structured?

Map along these dimensions:
1. Price tiers: budget / mid-range / premium / luxury
2. Distribution channels: DTC, retail, marketplace, wholesale, SaaS, etc.
3. Customer segments: demographics, use cases, geography
4. Product sub-categories: major product/service lines

Also identify:
- Seasonal demand patterns (peak months, trough months)
- Event-driven demand spikes (holidays, industry events, budget cycles)
- Any cyclical patterns (quarterly, annual)
```

**Parameters**:
- `search_context_size`: "high"

**Output**: Market structure across 4 dimensions + seasonality calendar.

---

### Step 5: Save & Log

**Goal**: Write all findings to `research-memory/market-landscape.md` and log the execution.

#### 5a. Write market-landscape.md

Use the exact schema below. Fill every section with Step 1-4 findings.

```markdown
# Market Landscape
> Last updated: [YYYY-MM-DD]
> Source skill: market-scanner

## Market Definition
[Category name, scope, boundaries, adjacent markets]

## Market Size (TAM / SAM)
| Metric | Value | Source | Year |
|--------|-------|--------|------|
| TAM | $XXB | [source] | [year] |
| SAM | $XXB | [source] | [year] |
| Growth Rate (CAGR) | XX% | [source] | [year range] |

[Regional breakdown if relevant]

## Growth Trajectory
[Growth stage: early / growth / mature / declining]
[Key growth drivers]
[Headwinds / risks]

## Macro Trends
| # | Trend | Category | Type | Impact | Timeframe |
|---|-------|----------|------|--------|-----------|
| 1 | [trend] | [category] | Opportunity/Threat | [impact] | [timeframe] |
| 2 | [trend] | [category] | Opportunity/Threat | [impact] | [timeframe] |
| 3 | [trend] | [category] | Opportunity/Threat | [impact] | [timeframe] |

## Market Structure Map
### By Price Tier
[budget / mid / premium / luxury breakdown]

### By Channel
[DTC, retail, marketplace, etc.]

### By Customer Segment
[demographics, use cases, geography]

### By Sub-Category
[product/service lines]

## Seasonality & Demand Cycles
[Peak months, trough months, event-driven spikes]
[Budget cycles, quarterly patterns]
```

**For Refresh mode**: Do NOT overwrite the entire file. Update only the sections that changed. Append `> Updated: [date]` below the section header for each changed section.

#### 5b. Update research-log.md

Append one row to the log:

```
| [YYYY-MM-DD] | market-scanner | Full Scan / Refresh | [brief summary of key findings or changes] | Perplexity |
```

---

## Perplexity MCP Tool Guide

| Tool | When to Use | This Skill |
|------|-------------|------------|
| `perplexity_reason` | Analytical reasoning, number crunching | Step 1 (category definition), Step 2 (TAM/SAM) |
| `perplexity_ask` | Factual Q&A, current events | Step 3 (trends), Step 4 (structure/seasonality) |
| `perplexity_search` | Find specific URLs/reports | Only if Steps 1-4 need source verification |

**Common parameters**:
- `search_context_size`: Always `"high"` — market research needs comprehensive context
- `search_recency_filter`: `"month"` for trends (Step 3), `"year"` for size data (Step 2)

**Query best practices**:
- Be specific: Include the exact market category in every query
- Ask for sources: Always request citation of source name + data year
- Handle conflicts: When sources disagree, report the range, not a single number
- One topic per query: Don't combine market size + trends in one call

---

## Quality Checklist

Before saving, verify:

- [ ] Market category is PRECISE (not "tech" but "AI-powered marketing automation for SMBs")
- [ ] TAM/SAM numbers have source citations and data year
- [ ] At least 3 macro trends identified, each tagged opportunity/threat
- [ ] Market structure covers at least 2 of 4 dimensions (price, channel, segment, sub-category)
- [ ] Seasonality section is populated (even if "no strong seasonality detected")
- [ ] All Perplexity responses include source URLs in the output
- [ ] research-log.md updated with execution record

---

## Example (Abbreviated)

**Input**: "Marketing education business selling skill packs and courses to solo marketers."

> **Category**: Creator Economy — Marketing Education & Digital Skills
> **TAM**: $30B (global online education, marketing subset — HolonIQ 2024)
> **SAM**: $3B (English-speaking solo marketers — estimated 2024)
> **CAGR**: 12-15% (SignalFire, 2024-2029)
> **Trends**: AI democratizing execution (Opportunity), Creator economy growth (Opportunity), AI replacing traditional education (Threat)
> **Seasonality**: Q1 peak (New Year), Q4 trough (holidays), Sep mini-peak (back-to-work)

---

## What This Skill Does NOT Do

- **Competitor analysis** → Use `competitor-finder` (separate skill)
- **Customer profiling** → Use `audience-profiler` (separate skill)
- **Customer language mining** → Use `voice-of-customer` (separate skill)
- **Strategic recommendations** → Use `research-synthesizer` (reads this output)

Market Scanner stays focused on **the market itself** — size, shape, forces, timing.
