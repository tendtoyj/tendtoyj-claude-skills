---
name: card-news-copy-writer
description: "Write copy for Instagram card-news (1080x1350). Plans card structure, writes all placeholders with strict character limits, and outputs structured markdown for evaluation. Use when user mentions: 카드뉴스 카피, card news copy, 카드뉴스 글, 카드뉴스 문구, write card news, 카드뉴스 텍스트, 카피 작성, card copy, 카드뉴스 초안"
user-invocable: true
---

# Card-News Copy Writer

> Plan card structure + write every placeholder's copy with strict character limits.
> No tools — pure copywriting. Outputs structured markdown for copy-evaluator.

---

## Purpose

Copy Writer handles the creative core of card-news production:
1. Decide the card structure (how many cards, what type each card is)
2. Write all placeholder text with strict character limits
3. Produce structured markdown that downstream skills consume

---

## Memory Auto-Load Protocol

```
1. Check card-news-memory/ exists → create from card-news-memory-template/ if missing
   → ⚠ If creation fails, SKIP memory loading and proceed with defaults
2. Load card-news-memory/series-config.md
   → Brand header name, account handle, default tags, color theme
   → If missing: use placeholder values (header="Brand", handle="@account")
3. Load card-news-memory/copy-bank.md
   → Past approved copy for few-shot reference (tone, structure patterns)
   → If missing: skip — write copy without few-shot reference
4. Load brand-memory/voice-profile.md (read-only)
   → Brand personality, tone descriptors, vocabulary preferences
   → If missing: skip — use neutral professional Korean tone
5. Optional: Load creative-memory/storytelling-frameworks.md (read-only)
   → Narrative patterns and hooks
6. Optional: Load research-memory/ files (read-only)
   → Topic-specific facts, data, quotes for content sourcing
```

**Error handling:** 필수 파일(series-config, copy-bank) 로드 실패 시, 사용자에게 경고 메시지를 출력하고 기본값으로 진행한다. 파일 로드 실패가 전체 프로세스를 중단시켜서는 안 된다.

**Read the following BEFORE writing any copy:**
1. `skills/card-news-maker/references/TEMPLATE-GUIDE.md` — placeholder별 상세 설명, 글자수 제한, 예시 (source of truth)
2. `references/placeholder-constraints.md` — copy-writer용 추가 규칙

---

## Input

| Input | Required | Source |
|-------|----------|--------|
| Topic / Theme | Yes | User or orchestrator |
| Card count | Yes | Default 4, range 2~10 |
| Language | No | Default: 한국어 |
| Specific angle / hook | No | User preference |
| Target audience | No | series-config or user |

---

## Process

### Step 1: Structure Planning

Design the card sequence based on topic and card count.

**Available card types:**

| Type | Position | Purpose |
|------|----------|---------|
| `cover` | Always first | Brand header + headline + tags |
| `content-image` | Middle | Image + title + body text |
| `content-features` | Middle | 3 feature cards with icons |
| `outro` | Always last | Closing message + account handle |

**Structure rules:**
- Card 1 = `cover` (mandatory)
- Last card = `outro` (mandatory)
- Middle cards = any combination of `content-image` and `content-features`
- For 2 cards: cover + outro only
- For 3 cards: cover + 1 content + outro
- For 4 cards (default): cover + 2 content + outro
- For 5+ cards: cover + 3+ content + outro

**Present the proposed structure to the user for confirmation before writing copy.**

- 사용자가 구조를 수정 요청하면 반영 후 다시 제안한다.
- **사용자가 별다른 의견 없이 진행을 요청하거나, 주제만 제시한 경우에는 기본 구조(4장: cover → content-image → content-features → outro)로 바로 진행한다.**
- 구조 확인에 최대 1회 재제안까지만 허용. 2회 이상 반복되면 기본 구조로 확정하고 카피 작성을 시작한다.

Example for 4-card set:
```
Proposed Structure (4 cards):
1. cover       — Hook + headline
2. content-image   — Key visual + explanation
3. content-features — 3 key points with icons
4. outro       — Closing + CTA
```

---

### Step 2: Write Copy

Write all placeholders for each card, strictly following character limits from `references/placeholder-constraints.md`.

#### Cover Card Copy

| Field | Max | Write |
|-------|-----|-------|
| accent-text | 6 | Short exclamation or hook in handwriting style |
| highlight-keyword | 6 | Core topic keyword with yellow highlight |
| cover-title | 6 | Continuation of headline |
| cover-subtitle | 20 | One-line context sentence |
| tag-1 ~ tag-N | 8 each | 2~4 hashtag pills |

**Cover writing tips:**
- `highlight-keyword` + `cover-title` form one visual headline — ensure they read naturally together
- `accent-text` is handwriting font — use casual, energetic tone (e.g., "핵심만 쏙", "알아볼까?")
- Tags should categorize the topic, not repeat the headline

#### Content-Image Card Copy

| Field | Max | Write |
|-------|-----|-------|
| category | 8 | Category pill (e.g., #경제) |
| highlight-keyword | 6 | Key term highlighted in yellow |
| title-line-1 | 12 (incl. keyword) | First title line (keyword is inline) |
| title-line-2 | 12 | Second title line |
| body-line-1~3 | 20 each | Body text, 2~3 lines |

**Content-image tips:**
- `highlight-keyword` appears inline within `title-line-1` — budget character count accordingly
- Body text should add context or explanation, not repeat the title
- Write `image-alt` text describing the ideal image (this feeds into contents-manager)

#### Content-Features Card Copy

| Field | Max | Write |
|-------|-----|-------|
| category | 8 | Category pill |
| highlight-keyword | 6 | Key term |
| title-line-1 | 12 (incl. keyword) | First title line |
| title-line-2 | 12 | Second title line |
| feature-N-title (×3) | 12 each | Feature heading |
| feature-N-description (×3) | 25 each | Feature explanation |
| body-line-1~3 | 20 each | Summary text |

**Content-features tips:**
- 3 features should be parallel in structure (similar length, same grammatical pattern)
- Feature descriptions should be self-contained — no "as mentioned above" references
- Include `icon-hint` for each feature (a descriptive word like "heart", "clock", "shield") — contents-manager uses this to select Lucide icons

#### Outro Card Copy

| Field | Max | Write |
|-------|-----|-------|
| outro-line-1~3 | 12 each | Closing message, center-aligned |
| account-handle | 15 | From series-config.md |

**Outro tips:**
- 2~3 lines that wrap up the topic with warmth
- Include a soft CTA ("팔로우", "저장해두세요", etc.) or emotional close
- Account handle comes from series-config — just reference it

---

### Step 3: Output

Produce structured markdown in this exact format:

```markdown
# Card-News Copy: [Topic]

> Date: [YYYY-MM-DD]
> Cards: [N]
> Language: [language]
> Structure: cover → [types] → outro

---

## Card 1 — Cover

- **accent-text**: [text] ([N]자)
- **highlight-keyword**: [text] ([N]자)
- **cover-title**: [text] ([N]자)
- **cover-subtitle**: [text] ([N]자)
- **tags**: [tag-1], [tag-2], [tag-3]

## Card 2 — Content-Image

- **category**: [text] ([N]자)
- **highlight-keyword**: [text] ([N]자)
- **title-line-1**: [text] ([N]자, keyword 포함)
- **title-line-2**: [text] ([N]자)
- **image-alt**: [ideal image description]
- **icon-hint**: [not applicable for content-image]
- **body-line-1**: [text] ([N]자)
- **body-line-2**: [text] ([N]자)
- **body-line-3**: [text] ([N]자)

## Card 3 — Content-Features

- **category**: [text]
- **highlight-keyword**: [text] ([N]자)
- **title-line-1**: [text] ([N]자)
- **title-line-2**: [text] ([N]자)
- **feature-1-title**: [text] ([N]자)
- **feature-1-description**: [text] ([N]자)
- **feature-1-icon-hint**: [descriptive word]
- **feature-2-title**: [text] ([N]자)
- **feature-2-description**: [text] ([N]자)
- **feature-2-icon-hint**: [descriptive word]
- **feature-3-title**: [text] ([N]자)
- **feature-3-description**: [text] ([N]자)
- **feature-3-icon-hint**: [descriptive word]
- **body-line-1**: [text] ([N]자)
- **body-line-2**: [text] ([N]자)
- **body-line-3**: [text] ([N]자)

## Card N — Outro

- **outro-line-1**: [text] ([N]자)
- **outro-line-2**: [text] ([N]자)
- **outro-line-3**: [text] ([N]자)
- **account-handle**: [from series-config]
```

**Character count notation:** Include the actual character count next to each text field in parentheses. This enables copy-evaluator to quickly verify limits.

---

## After PASS: Copy Bank Update

When copy-evaluator returns **PASS** or **PASS WITH NOTES**, append the approved copy to `card-news-memory/copy-bank.md` following the format defined in that file.

---

## What This Skill Does NOT Do

- **Evaluate copy quality** → copy-evaluator
- **Select icons** → contents-manager
- **Generate images** → image-generator
- **Render HTML/PNG** → card-news-maker
- **Route pipeline** → orchestrator

Copy Writer stays focused: **topic in → structured copy out**.
