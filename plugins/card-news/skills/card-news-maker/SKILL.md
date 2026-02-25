---
name: card-news-maker
description: "Assemble and render card-news HTML into final 1080x1350 PNG files using Playwright MCP. Combines approved copy, icons, and images into modular HTML, then captures each card as a pixel-perfect screenshot. Use when user mentions: 카드뉴스 렌더링, render cards, 카드뉴스 만들기, make cards, 카드뉴스 출력, card news render, 카드 이미지 만들기, PNG 출력, 최종 렌더, final render, 카드뉴스 조립"
user-invocable: true
---

# Card-News Maker

> Assemble modular HTML from card type components + render each card to 1080×1350 PNG.
> Tools: Playwright MCP. Final step in the card-news pipeline.

---

## Purpose

Card-News Maker is the assembler and renderer. It:
1. Collects all upstream outputs (copy, icons, images)
2. Builds a single HTML file from modular card type components
3. Renders each card as an isolated 1080×1350 PNG via Playwright
4. Saves output files and logs production

---

## Memory Auto-Load Protocol

```
1. Load approved copy (from copy-writer, after copy-evaluator PASS)
   → All placeholder values per card
2. Load contents-manager output (Visual Asset Plan)
   → Icon SVG paths, image file paths
3. Load card-news-memory/series-config.md
   → Brand header name, account handle, color overrides, font overrides
4. Read references/card-news-template.html ★ CRITICAL ★
   → 원본 HTML 템플릿 — 이 파일이 source of truth
   → 모든 CSS, 4개 카드 타입, placeholder가 이미 포함됨
5. Read references/TEMPLATE-GUIDE.md
   → Placeholder 목록, 글자수 제한, 사용 팁
6. Load references/card-type-components.md
   → 가변 카드(2~10장) 조립 가이드, color override 방법
7. Load references/playwright-pipeline.md ★ CRITICAL ★
   → Step-by-step rendering instructions
```

### Access Rules

| Memory | Permission |
|--------|-----------|
| `brand-memory/` | Read-only |
| `creative-memory/` | Read-only |
| `card-news-memory/` | Read & Write (production-log.md) |

---

## Input

| Input | Required | Source |
|-------|----------|--------|
| Approved copy | Yes | copy-writer (after PASS) |
| Visual Asset Plan | Yes | contents-manager |
| Generated images | Yes (for content-image cards) | image-generator |
| Series config | Yes | series-config.md |

---

## Process

### Step 1: HTML Assembly

원본 `card-news-template.html`을 기반으로 최종 `cards.html`을 조립한다.

**Assembly sequence:**

1. **Read 원본 HTML**: `references/card-news-template.html` 전체를 읽는다
2. **카드 블록 식별**: `<body>` 안의 4개 `<div class="card">` 블록을 각각 식별
   - Card 1 (Cover), Card 2 (Content-Image), Card 3 (Content-Features), Card 4 (Outro)
   - 원본의 HTML 주석(`<!-- CARD N — ... -->`)과 `.label` div로 구분
3. **카드 수 조정**: `references/card-type-components.md`의 가변 카드 조립 규칙에 따라
   - 4장 (기본): 원본 그대로 사용
   - 4장 미만: 불필요한 중간 카드 블록 제거
   - 4장 초과: 중간 카드 블록(Content-Image / Content-Features)을 복제하여 삽입
4. **각 카드 블록에 `data-card-index` 추가**: Playwright 렌더링용
   ```html
   <div class="card" data-card-index="1">
   ```
5. **Placeholder 치환**: 모든 `{{...}}`를 실제 값으로 교체
   - Copy placeholder → copy-writer 출력값
   - `{{icon-N-path}}` → contents-manager의 Lucide SVG path
   - `{{image-url}}` / `{{image-alt}}` → image-generator 출력 경로
   - `{{account-handle}}` → series-config.md
   - Content-Image의 `<img>` 주석 해제 → 실제 이미지 경로 삽입
6. **Brand name 교체**: `TODAY&rsquo;S PICKS` → series-config.md brand name
7. **Color override 적용**: `:root` CSS 변수를 series-config.md override 값으로 교체
8. **`.label` 텍스트 업데이트**: `N / TOTAL — Type` 형태로 갱신
9. **Save**: 최종 HTML을 `cards.html`로 저장

**핵심 원칙:**
- 원본 HTML이 source of truth — CSS를 다시 쓰거나 구조를 재구성하지 않는다
- 원본을 복사한 뒤 placeholder 치환 + 카드 블록 추가/제거만 수행
- 모든 CSS 값, 클래스명, 레이아웃은 원본 그대로 유지

---

### Step 2: Save HTML

Save the assembled HTML to:
```
[project]/card-news/[topic]-[YYYY-MM-DD]/cards.html
```

Copy any generated images to the same directory (if not already there).

Verify the file structure:
```
card-news/[topic]-[YYYY-MM-DD]/
├── cards.html
├── img-card-2.png (if content-image cards exist)
├── img-card-3.png (etc.)
└── outro-icon.png (if AI-generated)
```

---

### Step 3: Playwright Rendering

**Read `references/playwright-pipeline.md` for the detailed rendering procedure.**

High-level sequence:
1. Navigate to the HTML file via `file://` protocol
2. Wait for fonts to load (`document.fonts.ready`)
3. Wait for images to load
4. For each card (by `data-card-index`):
   a. Hide all other cards (`display: none`)
   b. Resize viewport to 1080×1350
   c. Take screenshot of the visible card
   d. Save as `card-[N].png`
5. Restore all cards visible (for preview)

**Output files:**
```
card-news/[topic]-[YYYY-MM-DD]/
├── cards.html
├── card-1.png    (cover)
├── card-2.png    (content)
├── ...
├── card-N.png    (outro)
├── img-card-*.png (source images)
└── outro-icon.png
```

---

### Step 4: Verification

After rendering, verify each PNG:
1. File exists and is non-empty
2. Dimensions are 1080×1350 (check via Playwright or file metadata)
3. Visual spot-check: fonts loaded, images rendered, no blank areas

**If any card fails:**
- Check font loading (most common issue)
- Check image paths (relative path errors)
- Retry rendering for the failed card only

---

### Step 5: Log Production

Append to `card-news-memory/production-log.md`:

```markdown
| [YYYY-MM-DD] | [Topic] | [N] cards | Complete | `card-news/[topic]-[date]/` | [notes] |
```

---

### Step 6: Output Summary

Present to the user:

```markdown
# Card-News Production Complete: [Topic]

> Date: [YYYY-MM-DD]
> Cards: [N] (cover + [N-2] content + outro)
> Output: `card-news/[topic]-[YYYY-MM-DD]/`

## Rendered Cards

| Card | Type | File | Status |
|------|------|------|--------|
| 1 | cover | card-1.png | ✅ |
| 2 | content-image | card-2.png | ✅ |
| 3 | content-features | card-3.png | ✅ |
| 4 | outro | card-4.png | ✅ |

## Files
- `cards.html` — Source HTML (editable for future modifications)
- `card-*.png` — Final 1080×1350 PNG files (ready for Instagram)
- `img-card-*.png` — Source content images

## Next Steps
- Upload `card-1.png` through `card-[N].png` to Instagram as a carousel
- Edit `cards.html` to modify copy or colors, then re-render
- Run image-generator with different prompts for alternative visuals
```

---

## Quality Checklist

- [ ] HTML assembled with all placeholders replaced (no `{{...}}` remaining)
- [ ] CSS variables overridden per series-config
- [ ] Font links included in `<head>`
- [ ] All image `src` paths resolve correctly
- [ ] Each card rendered as isolated 1080×1350 PNG
- [ ] Fonts loaded before screenshot (no fallback fonts visible)
- [ ] Images loaded before screenshot (no gray placeholders)
- [ ] production-log.md updated
- [ ] Output summary presented to user

---

## What This Skill Does NOT Do

- **Write copy** → copy-writer
- **Evaluate copy** → copy-evaluator
- **Plan visual assets** → contents-manager
- **Generate AI images** → image-generator
- **Design new card types** → Template extensions are manual

Card-News Maker stays focused: **assembled assets in → rendered PNG files out**.
