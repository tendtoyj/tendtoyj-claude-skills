---
name: competitor-finder
description: "Identify your competitive set — direct and indirect competitors — and map their positioning, messaging, pricing, and channel activity. Use when user mentions: competitor research, competitive analysis, competitor finder, find competitors, who are my competitors, competitive set, competitive landscape, competitor mapping, competitor profiling, competitor positioning, competitive intel, competitive intelligence, market competitors, rival analysis, competition scan"
user-invocable: true
---

# Competitor Finder

> Identify your competitive set and map their positioning, messaging, pricing, and channel activity.
> Uses Perplexity for competitive intelligence. Output is the foundation that competitor-analyzer and competitor-visual enrich.

---

## Purpose

Competitor Finder is the **starting point of competitive intelligence**. It answers:
- Who are we actually competing with? (direct AND indirect)
- How does each competitor position themselves?
- What are their core value propositions and target audiences?
- What pricing models and ranges do they use?
- Which marketing channels are they active on?
- Where are the gaps and opportunities?

The output — `research-memory/competitive-intel.md` — is the **skeleton** that two downstream skills build upon:
- **competitor-analyzer** (Firecrawl) adds website messaging detail: headlines, CTAs, pricing pages, social proof
- **competitor-visual** (Playwright) adds design audit: screenshots, color palettes, layout patterns, visual tone

> "You can't differentiate if you don't know what you're differentiating FROM." — The Boring Marketer

---

## Modes

| Mode | When to Use | Behavior |
|------|-------------|----------|
| **Full Discovery** | No `competitive-intel.md` exists, or it's an empty scaffold | Run all 5 steps from scratch |
| **Refresh** | `competitive-intel.md` already has `[competitor-finder]` data | Check `research-log.md` for last scan date → add new competitors or update existing profiles |

---

## Auto-Load Protocol

On every invocation, BEFORE any research:

1. **Check `research-memory/` directory**
2. If files exist → Read ALL `.md` files (except README.md)
3. **Priority context from `market-landscape.md`**:
   - Market category definition → sets competitor search scope
   - Market structure (price tiers, channels) → provides classification frame
   - Market size → helps gauge competitive intensity
4. **Check `brand-memory/`** (read-only) → If exists, use business description, positioning, and target audience as self-reference context
5. If `competitive-intel.md` has `[competitor-finder]` data AND `research-log.md` shows a previous scan → **suggest Refresh mode**

**Enrichment preservation rule**: When loading existing `competitive-intel.md`, NEVER delete or overwrite sections tagged `[competitor-analyzer]` or `[competitor-visual]`. Only touch `[competitor-finder]` sections.

---

## Input Gathering

Collect from the user conversationally. Do NOT dump a form — ask naturally.

| Field | Required | Description |
|-------|----------|-------------|
| Business / Product description | YES | What does the company/product do? |
| Known competitors | Optional | Starting points for competitive mapping |
| Business model | Optional | SaaS, service, e-commerce, education, etc. — frames pricing comparison |
| Geography / Market scope | Optional | Global or specific region — bounds competitor search |
| Focus area | Optional | Pricing comparison, messaging differentiation, channel strategy, etc. |

**If brand-memory/ exists**, pre-fill business description, positioning, and target audience from `voice-profile.md` and `positioning.md` — ask user to confirm or correct.

**If market-landscape.md exists**, show the market category and structure as context: "Based on your market scan, I'll search for competitors in [category]. Does this look right?"

**If this is a Refresh**, show the current competitive set and ask: "Any new competitors to add? Or specific profiles to update?"

---

## Process

### Step 1: Load Context & Set Search Scope

**Goal**: Establish the competitive search frame using existing research.

**Actions**:
- Load all research-memory/ files
- Extract from market-landscape.md (if available):
  - Market category → primary search term
  - Price tiers → competitor classification frame
  - Channels → where to look for competitors
  - Adjacent markets → source of indirect competitors
- Extract from brand-memory/ (if available):
  - Own positioning → identify who occupies similar territory
  - Target audience → find who targets the same people
- If competitive-intel.md exists with data → switch to Refresh mode

**Output**: Clear search scope definition (category + constraints).

---

### Step 2: Identify Competitive Set (Direct + Indirect)

**Goal**: Build a comprehensive list of direct and indirect competitors with URLs.

**Tool**: `perplexity_reason` (classification reasoning required)

**Query pattern**:
```
Who are the main competitors for a [business description] in the [market category] space?

Identify:
1. Direct competitors (5-8): Same target audience, similar solution, same category
2. Indirect competitors (2-3): Different approach to same problem, OR adjacent category players expanding into this space

For EACH competitor, provide:
- Company name
- Website URL (homepage)
- One-line description of what they do
- Classification: direct or indirect, with reasoning
```

**Parameters**:
- `search_context_size`: "high"

**If user provided known competitors**: Include them in the query as starting points and ask Perplexity to validate + expand: "I already know about [names]. Who else competes in this space?"

**Output**: Competitive set table — names, URLs, descriptions, classifications.

---

### Step 3: Build Competitor Profiles (Positioning, Messaging, Pricing)

**Goal**: Map each competitor's basic positioning, value proposition, audience, and pricing.

**Tool**: `perplexity_ask` (factual data collection)

**Query pattern**:
```
For each of these competitors in the [market category]:
[list competitor names from Step 2]

Analyze each one:
1. Positioning: How do they describe themselves? (tagline, hero headline, or elevator pitch)
2. Core value propositions: Top 2-3 benefits they emphasize
3. Target audience: Who are they built for? (job titles, company sizes, demographics)
4. Pricing model: Free / Freemium / Subscription / One-time / Enterprise
5. Price range: Approximate price points or tiers

Cite their website or recent coverage as source for each data point.
```

**Parameters**:
- `search_context_size`: "high"

**If competitors are numerous (8+)**: Split into two queries to avoid shallow coverage.

**Output**: Positioning & Messaging Matrix for all competitors.

---

### Step 4: Channel Activity + Gap Analysis

**Goal**: Map competitors' marketing channel presence and identify whitespace opportunities.

**Tool**: `perplexity_ask`

**Query pattern**:
```
What marketing channels are these [market category] competitors most active on?
[list competitor names]

For each competitor, assess presence and activity level on:
- Content marketing (blog, YouTube, podcast)
- Social media (which platforms, approximate follower counts or engagement level)
- SEO / Paid advertising
- Email / Newsletter
- Community / Events

Rate each: Strong / Moderate / Weak / Absent

Then identify:
1. Underserved channels: Which channels do MOST competitors neglect?
2. Positioning whitespace: What positioning angles are unclaimed?
3. Messaging gaps: What customer needs are competitors NOT addressing in their messaging?
```

**Parameters**:
- `search_recency_filter`: "month"
- `search_context_size`: "high"

**Output**: Channel Activity Matrix + Gaps & Opportunities section.

---

### Step 5: Save & Log

**Goal**: Write all findings to `research-memory/competitive-intel.md` and log execution.

#### 5a. Write competitive-intel.md

Use the **exact schema** in `references/competitive-intel-schema.md`. Key rules:
- Tag every section you write with `[competitor-finder]`
- Leave `[competitor-analyzer]` and `[competitor-visual]` sections as empty scaffolds with HTML comments
- For Refresh: update only `[competitor-finder]` sections; NEVER touch other skills' sections

#### 5b. Update research-log.md

Append one row:
```
| [YYYY-MM-DD] | competitor-finder | Full Discovery / Refresh | [X direct + Y indirect identified, key gaps] | Perplexity |
```

---

## Perplexity MCP Tool Guide

| Tool | When to Use | This Skill |
|------|-------------|------------|
| `perplexity_reason` | Classification, reasoning | Step 2: Competitive set identification + direct/indirect classification |
| `perplexity_ask` | Factual Q&A, current data | Step 3: Competitor profiles, Step 4: Channel activity |
| `perplexity_search` | Find specific URLs/sources | Only if competitor website URLs need verification |

**Common parameters**:
- `search_context_size`: Always `"high"` — competitive research needs broad context
- `search_recency_filter`: `"month"` for Step 4 (latest channel activity), default for Steps 2-3

**Query best practices**:
- Include market category in every query (from market-landscape.md or user input)
- Always request website URLs — competitor-analyzer and competitor-visual depend on them
- Ask for source citations for positioning claims
- One topic per query: Don't combine competitor identification + profiling in one call
- For 8+ competitors, split profiling into two queries to ensure depth

---

## Quality Checklist

Before saving, verify:

- [ ] At least 5 direct competitors identified (with URLs)
- [ ] At least 2 indirect competitors identified (with URLs)
- [ ] Every competitor has: positioning, value props, target audience, pricing
- [ ] Channel Activity Matrix covers at least 4 of 5 channel types
- [ ] Gaps & Opportunities section has at least one finding per sub-section
- [ ] All sections tagged with `[competitor-finder]`
- [ ] Downstream sections (`[competitor-analyzer]`, `[competitor-visual]`) are empty scaffolds
- [ ] All Perplexity responses include source citations
- [ ] research-log.md updated with execution record
- [ ] Competitor URLs are valid homepage links (not 404s or redirects)

---

## Example (Abbreviated)

**Input**: "Marketing education business selling AI-powered skill packs to solo marketers and small teams."

> **Direct Competitors** (5):
> 1. Marketing Examined (Alex Garcia) — Newsletter-based marketing education
> 2. CopyBlogger — Content marketing & copywriting education platform
> 3. DigitalMarketer — Comprehensive digital marketing training + certifications
> 4. Demand Curve — Growth marketing education (YC-backed)
> 5. MarketingProfs — B2B marketing education + events
>
> **Indirect Competitors** (2):
> 1. ChatGPT / Claude — AI directly performing marketing tasks (substitutes education)
> 2. Canva — Design tool with built-in marketing education content
>
> **Key Gaps**:
> - No competitor offers "AI + marketing" skill packs as a combined product
> - Newsletter-based education is crowded; actionable skill/template bundles are rare
> - Most competitors weak on community-driven learning

---

## What This Skill Does NOT Do

- **Website scraping / messaging detail** → Use `competitor-analyzer` (Firecrawl)
- **Screenshots / design patterns** → Use `competitor-visual` (Playwright)
- **Customer profiling** → Use `audience-profiler`
- **Customer language mining** → Use `voice-of-customer`
- **Strategic recommendations** → Use `research-synthesizer` (reads this output)

Competitor Finder stays focused on **who the competitors are** and **how they position at a high level** — the foundation for deeper competitive analysis.
