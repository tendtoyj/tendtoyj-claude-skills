---
name: card-news-copy-evaluator
description: "Quality gate for card-news copy. Validates character limits, scores quality across 7 dimensions, and issues PASS / REVISION verdicts. Use when user mentions: 카피 평가, copy evaluate, 카드뉴스 검수, card news review, 카피 검토, copy check, 품질 체크, quality gate, 카피 채점"
user-invocable: true
---

# Card-News Copy Evaluator

> Quality gate for card-news copy. Format validation + multi-dimension scoring.
> No tools — pure evaluation. Returns PASS, PASS WITH NOTES, or REVISION with feedback.

---

## Purpose

Copy Evaluator is the quality checkpoint between copy-writer and card-news-maker. It:
1. Validates every placeholder against character limits (hard gate)
2. Scores copy quality across 7 weighted dimensions
3. Issues a verdict: PASS / PASS WITH NOTES / REVISION
4. On REVISION, provides specific, actionable feedback for copy-writer

**No copy passes to rendering without going through this gate.**

---

## Memory Auto-Load Protocol

```
1. Load the copy-writer output (structured markdown — provided as input)
2. Load card-news-memory/copy-bank.md
   → Compare against past approved copy for quality baseline
3. Load brand-memory/voice-profile.md (read-only)
   → Brand voice reference for scoring Brand Voice dimension
4. Load references/scoring-rubric.md
   → Detailed 1/3/5 point examples for each scoring dimension
```

---

## Input

| Input | Required | Source |
|-------|----------|--------|
| Copy-writer output | Yes | Structured markdown from copy-writer |
| Brand voice profile | Recommended | brand-memory/voice-profile.md |
| Past approved copy | Optional | copy-bank.md for baseline comparison |

---

## Process

### Step 1: Format Validation (Hard Gate)

Check every placeholder against the character limits defined in `copy-writer/references/placeholder-constraints.md`.

**Character limit table:**

| Field | Max |
|-------|-----|
| accent-text | 6 |
| highlight-keyword | 6 |
| cover-title | 6 |
| cover-subtitle | 20 |
| tag-N | 8 |
| category | 8 |
| title-line-1 (incl. keyword) | 12 |
| title-line-2 | 12 |
| body-line-N | 20 |
| feature-N-title | 12 |
| feature-N-description | 25 |
| outro-line-N | 12 |
| account-handle | 15 |

**Validation rules:**
1. Count characters for every placeholder
2. Flag any violation with: `[OVER] field: "text" (N/MAX chars)`
3. **If ANY character limit is exceeded → immediate REVISION verdict**
4. No further scoring needed when format fails

**Output format for violations:**
```
FORMAT VALIDATION: FAIL

Violations:
- [OVER] title-line-2: "소비심리가 크게 주춤하는 상황" (14/12 chars)
- [OVER] feature-2-description: "혈압과 혈당 그리고 스트레스 지수까지 실시간 측정 가능" (27/25 chars)

→ Verdict: REVISION — Fix character overflows before quality scoring.
```

---

### Step 2: Quality Scoring

Score the copy across 7 dimensions. **Read `references/scoring-rubric.md` for detailed 1/3/5 point criteria.**

| Dimension | Weight | What to Evaluate |
|-----------|--------|-----------------|
| **Hook Power** | 25% | Does the cover grab attention in 1 second? Accent + keyword + title impact |
| **Narrative Flow** | 20% | Does the story progress logically across cards? Cover→content→outro arc |
| **Information Density** | 15% | Is each card dense with value, not fluff? Body text adds real information |
| **Brand Voice** | 15% | Does the tone match voice-profile.md? Consistent personality throughout |
| **Highlight Relevance** | 10% | Are the highlighted keywords truly the most important terms? |
| **CTA Strength** | 10% | Does the outro drive action? Save, follow, share motivation |
| **Korean Naturalness** | 5% | Does it read like native Korean? No 번역체, natural particles/endings |

**Scoring scale per dimension: 1 (poor) / 2 / 3 (adequate) / 4 / 5 (excellent)**

**Weighted score calculation:**
```
Total = (Hook × 0.25) + (Narrative × 0.20) + (Info × 0.15) +
        (Voice × 0.15) + (Highlight × 0.10) + (CTA × 0.10) + (Korean × 0.05)
```

---

### Step 3: Verdict

| Score | Verdict | Action |
|-------|---------|--------|
| 4.0 ~ 5.0 | **PASS** | Proceed to contents-manager + card-news-maker |
| 3.0 ~ 3.9 | **PASS WITH NOTES** | Proceed, but include improvement suggestions |
| Below 3.0 | **REVISION** | Return to copy-writer with specific feedback |

---

### Step 4: Output

#### PASS Output

```markdown
# Copy Evaluation: [Topic]

> Date: [YYYY-MM-DD]
> Verdict: ✅ PASS
> Score: [X.X] / 5.0

## Format Validation: PASS
All [N] placeholders within character limits.

## Quality Scores

| Dimension | Score | Notes |
|-----------|-------|-------|
| Hook Power (25%) | [X] | [brief note] |
| Narrative Flow (20%) | [X] | [brief note] |
| Information Density (15%) | [X] | [brief note] |
| Brand Voice (15%) | [X] | [brief note] |
| Highlight Relevance (10%) | [X] | [brief note] |
| CTA Strength (10%) | [X] | [brief note] |
| Korean Naturalness (5%) | [X] | [brief note] |

**Weighted Total: [X.X] / 5.0**

## Strengths
- [top 2-3 specific strengths]

## Approved for Production
This copy is ready for contents-manager and card-news-maker.
```

#### PASS WITH NOTES Output

Same as PASS, plus:
```markdown
## Improvement Notes (Optional)
- [specific suggestions for future iterations]
- [these do NOT block production]
```

#### REVISION Output

```markdown
# Copy Evaluation: [Topic]

> Date: [YYYY-MM-DD]
> Verdict: ❌ REVISION
> Score: [X.X] / 5.0

## Format Validation: [PASS / FAIL]
[If FAIL, list violations]

## Quality Scores
[Same table as PASS]

## Required Changes
1. **[Dimension]**: [Specific, actionable feedback]
   - Current: "[problematic text]"
   - Issue: [what's wrong]
   - Suggestion: [concrete improvement direction]

2. **[Dimension]**: [...]

## Revision Priority
Focus on these changes in order:
1. [Most impactful fix]
2. [Second priority]
3. [Third priority]
```

---

## Feedback Loop

When REVISION is issued:
1. copy-writer receives the evaluation output
2. copy-writer revises based on **Required Changes** and **Revision Priority**
3. copy-writer resubmits revised copy
4. copy-evaluator re-evaluates (full process, not just changed parts)
5. Maximum 3 revision cycles — if still failing after 3, escalate to user

---

## Quality Checklist

- [ ] Every single placeholder character-counted (no exceptions)
- [ ] Format validation completed before quality scoring
- [ ] All 7 dimensions scored with brief justification notes
- [ ] Weighted total calculated correctly
- [ ] Verdict matches the score threshold
- [ ] REVISION feedback is specific and actionable (not vague)
- [ ] Strengths highlighted even in REVISION verdicts
- [ ] Output format matches the templates above

---

## What This Skill Does NOT Do

- **Write or rewrite copy** → copy-writer handles revisions
- **Select visual assets** → contents-manager
- **Render cards** → card-news-maker
- **Score visual quality** → out of scope

Copy Evaluator stays focused: **copy in → scored verdict out**.
