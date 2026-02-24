---
name: voice-of-customer
description: "Mine real customer language from communities, reviews, and forums — build a Customer Language Bank of pain expressions, desire phrases, comparison language, and purchase triggers. Use when user mentions: voice of customer, customer language, customer words, what customers say, customer feedback, review mining, community research, pain expressions, desire expressions, trigger phrases, customer quotes, language bank, VOC, customer voice, real language, how customers talk, customer sentiment, review analysis, community mining, Reddit research, forum mining"
user-invocable: true
---

# Voice of Customer

> Mine real customer language from communities, reviews, and forums.
> Uses Perplexity for community research. Output feeds copywriting, email, SEO, and every execution skill.

---

## Purpose

Voice of Customer answers the question **"What words do our customers actually use?"** It produces a **Customer Language Bank** with four categories:

- **Pain Expressions** — Complaints, frustrations, problem descriptions
- **Desire Expressions** — Wishes, aspirations, ideal outcomes
- **Comparison Language** — How customers evaluate and compare alternatives
- **Trigger Phrases** — What finally pushes them to buy, subscribe, or switch

The output — `research-memory/customer-language.md` — is the **language foundation** that copywriting (06), SEO (07), newsletter (08), email sequences (09), and content atomizer (10) reference to write in customers' own words.

> "Every customer photo is an ad. Every review is copy. Every unboxing video is content. You're not creating marketing — you're curating what customers already give you."

**Relationship to Audience Profiler**: Audience Profiler maps *who the customer is*. Voice of Customer captures *how the customer talks*.

---

## Modes

| Mode | When to Use | Behavior |
|------|-------------|----------|
| **Full Mining** | No `customer-language.md` exists, or it's an empty scaffold | Run all 5 steps from scratch |
| **Refresh** | `customer-language.md` already has data | Check `research-log.md` for last mining date → add new expressions or strengthen weak categories |

---

## Auto-Load Protocol

On every invocation, BEFORE any research:

1. **Check `research-memory/` directory**
2. If files exist → Read ALL `.md` files (except README.md)
3. Use loaded context to:
   - **From `customer-insight.md`**: Pull segment names, pain points, and community list → use as search keyword seeds (Step 1)
   - **From `competitive-intel.md`**: Pull competitor names → generate comparison search keywords ("[competitor] review", "[competitor] alternative")
   - Build on existing findings (deepen, not repeat)
4. **Check `brand-memory/`** (read-only) → If exists, extract product name and category from `voice-profile.md` → use in search queries
5. If `customer-language.md` has data AND `research-log.md` shows a previous mining run → **suggest Refresh mode**

---

## Input Gathering

Collect from the user conversationally. Do NOT dump a form — ask naturally.

| Field | Required | Description |
|-------|----------|-------------|
| Product / Service category | YES | What market are we mining language for? |
| Competitor names | Optional | For "[competitor] review" and "[competitor] vs" searches |
| Known communities | Optional | Reddit subs, Discord servers, forums the user already knows |
| Collection focus | Optional | Specific category to prioritize (Pain only, Triggers only, etc.) |
| Language / Region | Optional | English / Korean / other — determines search language |

**If customer-insight.md exists**, pre-fill category, segments, pain points, and communities — ask user to confirm or add.

**If competitive-intel.md exists**, extract competitor names automatically — show user and ask if any should be excluded.

**If this is a Refresh**, show current Language Bank summary (count per category) and ask: "Which category needs more expressions? Any new communities to mine?"

---

## Process

### Step 1: Load Context & Generate Search Keywords

**Goal**: Build a keyword matrix for targeted community mining.

**Actions**:
- Load all research-memory/ files
- Extract from customer-insight.md (if available):
  - Segment names → search audience context
  - Pain points → Pain Expression seed keywords
  - Community list → search scope
- Extract from competitive-intel.md (if available):
  - Competitor names → Comparison and Review keywords
- Generate the **Search Keyword Matrix**:

```
PAIN keywords:
  "[category] problems", "[category] frustrating", "[category] sucks"
  "[category] disappointed", "hate [category]", "[category] complaint"

DESIRE keywords:
  "best [category]", "[category] recommendation", "wish [category] could"
  "ideal [category]", "perfect [category] would", "[category] dream feature"

COMPARISON keywords:
  "[competitor A] vs [competitor B]", "[product] alternative"
  "switched from [competitor]", "[competitor] review honest"

TRIGGER keywords:
  "finally bought [category]", "worth it [category]"
  "what convinced me [category]", "just signed up for"
```

- If customer-language.md exists with data → switch to Refresh mode

**Output**: Keyword matrix ready for Steps 2-4.

---

### Step 2: Mine Pain Expressions

**Goal**: Collect 10-15 real phrases expressing frustration, complaints, and problems.

**Tool**: `perplexity_ask`

**Query pattern**:
```
What are the most common complaints and frustrations people express about [category] in online communities (Reddit, review sites, forums, social media)?

For each expression: quote the actual language, note the context, identify the source platform, rate intensity (Mild/Strong/Extreme).

Find 10-15 distinct pain expressions from [primary segment] perspective.
Include complaints about: [competitor list]
```

**Parameters**: `search_context_size: "high"`, `search_recency_filter: "year"`

---

### Step 3: Mine Desire Expressions & Comparison Language

**Goal**: Collect 8-10 desire expressions + 5-8 comparison expressions in one pass.

**Why combined**: In communities, wishes and comparisons naturally co-occur in the same threads ("I wish X could do what Y does").

**Tool**: `perplexity_ask`

**Query pattern**:
```
What do people say when discussing what they WANT from [category] and how they COMPARE options?

DESIRE EXPRESSIONS: "I wish [product] could...", "The perfect [category] would...", ideal outcomes, feature requests.
COMPARISON LANGUAGE: "[A] vs [B]", "I switched from X to Y because...", evaluation criteria mentioned.

For each: quote actual language, note context + source, tag as Desire or Comparison.
For comparisons: note competitors mentioned and criteria used.

Find 8-10 desire + 5-8 comparison expressions. Include: [competitor list]
```

**Parameters**: `search_context_size: "high"`, `search_recency_filter: "year"`

---

### Step 4: Mine Trigger Phrases

**Goal**: Collect 8-12 phrases describing the moment of purchase decision.

**Tool**: `perplexity_ask`

**Query pattern**:
```
What makes people finally decide to purchase or subscribe to [category]?

Search for phrases describing the MOMENT of decision: "I finally decided because...", "What convinced me was...", "I was on the fence until..."

Classify each by trigger type: Social Proof, Urgency, Value Realization, Risk Removal, Authority, or Scarcity.

For each: quote the expression, classify type, note source platform.
Find 8-12 trigger phrases across 3+ trigger types.
```

**Parameters**: `search_context_size: "high"`, `search_recency_filter: "year"`

---

### Step 5: Save & Log

**Goal**: Write all findings to `research-memory/customer-language.md` and log execution.

#### 5a. Write customer-language.md

Use the **exact schema** in `references/customer-language-schema.md`. Key rules:
- 5 sections: Pain Expressions, Desire Expressions, Comparison Language, Trigger Phrases, Community Sources
- Every expression in quotation marks with source attribution
- For Refresh: append new rows to existing tables, do NOT overwrite

#### 5b. Update research-log.md

Append one row:

```
| [YYYY-MM-DD] | voice-of-customer | Full Mining / Refresh | [X pain + Y desire + Z comparison + W trigger collected, key sources] | Perplexity |
```

---

## Perplexity MCP Tool Guide

| Tool | When to Use | This Skill |
|------|-------------|------------|
| `perplexity_ask` | Community language collection | Step 2 (pain), Step 3 (desire + comparison), Step 4 (triggers) |
| `perplexity_search` | Find specific communities/threads | Step 1 only — if known communities need URL discovery |

> This skill does NOT use `perplexity_reason`. The focus is **collection**, not classification or reasoning. `perplexity_ask` handles fact-based community mining best.

**Common parameters**:
- `search_context_size`: Always `"high"` — community data needs broad context to find real quotes
- `search_recency_filter`: `"year"` — recent expressions are most relevant for current copy

**Query best practices**:
- Always include the product category in every query
- Ask for ACTUAL phrases, not summaries — emphasize "quote the real language"
- Include competitor names to surface comparison discussions
- One category per query (pain, desire+comparison, triggers) — don't combine all in one call
- Request source attribution for every expression

---

## Quality Checklist

Before saving, verify:

- [ ] At least 10 pain expressions collected (with intensity ratings)
- [ ] At least 8 desire expressions collected
- [ ] At least 5 comparison expressions with competitor names and criteria
- [ ] At least 8 trigger phrases across 3+ trigger types
- [ ] Every expression includes source platform/community
- [ ] Community Sources table lists all mined sources with URLs where available
- [ ] Expressions read like real human language (not AI-summarized)
- [ ] All Perplexity responses include source citations
- [ ] research-log.md updated with execution record
- [ ] If Refresh: existing expressions preserved, new ones appended

---

## Example (Abbreviated)

**Input**: "AI marketing education / skill packs for solo marketers"

> **Pain Expressions**:
> - "I've installed 10 AI tools and ended up only using ChatGPT" (Reddit r/marketing, Strong)
> - "Everything AI writes sounds the same — that generic LinkedIn tone" (Twitter, Strong)
> - "Spent more time writing prompts than actually writing the copy" (HN, Mild)
>
> **Desire Expressions**:
> - "I just want copy-paste prompts that actually work for my niche" (Reddit)
> - "Wish there was a system, not just random tips scattered across YouTube" (Twitter)
>
> **Comparison Language**:
> - "DigitalMarketer's content was made pre-AI so it feels outdated now"
> - "Demand Curve is great for growth but weak on content marketing side"
>
> **Trigger Phrases**:
> - "$199 is less than one month of agency fees and I keep it forever" (Value Realization)
> - "Three people in my Twitter feed recommended it in one week" (Social Proof)

---

## What This Skill Does NOT Do

- **Customer profiling / segmentation** → Use `audience-profiler` (maps who they are)
- **Competitor identification** → Use `competitor-finder` (identifies the players)
- **Website scraping for competitor copy** → Use `competitor-analyzer` (Firecrawl)
- **Strategic recommendations** → Use `research-synthesizer` (reads this output)
- **Market sizing / trends** → Use `market-scanner`

Voice of Customer stays focused on **capturing real language** — the exact words, phrases, and expressions customers use when they complain, wish, compare, and decide.
