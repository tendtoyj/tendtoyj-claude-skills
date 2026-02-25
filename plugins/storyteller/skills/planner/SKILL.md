---
name: storyteller-planner
description: "콘텐츠(뉴스레터, 아티클, URL 등)를 받아 카드뉴스 전체 구성을 기획하는 스킬. 콘텐츠 분석 → 카드 타입 결정 → 카드별 카피 작성 → 이미지 컨셉 지정 → 유저 승인까지 수행한다. 사용 시점: (1) 유저가 콘텐츠를 주며 카드뉴스로 만들어달라고 할 때, (2) storyteller 플러그인의 기획 단계를 실행할 때, (3) 카드뉴스 기획, 플래닝, storyteller plan 등의 요청이 있을 때."
---

# Storyteller Planner

콘텐츠를 받아 카드뉴스 세트(표지 1장 + 내용 10~15장)를 기획하고, 유저 승인을 받는다.

## 워크플로우

### Step 1. 콘텐츠 분석

- 원본 콘텐츠의 핵심 메시지, 내러티브 흐름 파악
- 10~15개 카드로 나눌 수 있는 의미 단위로 분절
- URL이 주어지면 콘텐츠를 먼저 가져온다

### Step 2. 카드 구성 결정

- **표지**(1장): 무조건 **card-gradient**
- **내용**(10~15장): 한 세트는 **한 카드 타입으로 통일** — 아래 3가지 중 택 1
  - `card-white` — 텍스트만, 빠른 제작
  - `card-image-overlay` — 풀이미지 + 텍스트 오버레이, 비주얼 임팩트
  - `card-image-top` — 상단 이미지 + 하단 텍스트, 이미지와 설명 모두 필요할 때
- 콘텐츠 성격에 따라 내용 카드 타입을 추천하고, 카드 수도 자체 판단
- 각 필드의 글자수 제한은 [references/card-specs.md](references/card-specs.md) 참조

### Step 3. 카드별 카피 작성

- 카드 타입에 맞는 텍스트 필드 작성
- 줄바꿈은 `<br>` 태그 사용
- **글자수 제한을 반드시 준수** — 작성 후 각 필드가 줄당 글자수 × 최대 줄 수 이내인지 검증

핵심 글자수 요약:

| 카드 타입 | 필드 | 한 줄 최대 | 최대 줄 수 |
|-----------|------|-----------|-----------|
| card-gradient | top-label | 19자 | 1 |
| card-gradient | title | 9자 | 2~3 |
| card-gradient | subtitle | 19자 | 1 |
| card-white | title | 11자 | 1~2 |
| card-white | body-text | 18자 | 2~3 |
| card-image-overlay | title | 9자 | 2~3 |
| card-image-overlay | subtitle | 19자 | 1 |
| card-image-top | title | 11자 | 1~2 |
| card-image-top | body-text | 18자 | 2~3 |

### Step 4. 이미지 방향 지정

내용 카드 타입이 **card-white**이면 이 단계 스킵.

이미지가 필요한 카드(card-image-overlay, card-image-top)에 대해:
- 이미지 모드 선택: **A (시네마틱)** 또는 **B (컨셉추얼)**
- 카드별 장면/컨셉 한 줄 설명 (`image-concept` 필드)
- image-maker 스킬이 이 한 줄 설명을 구체적 프롬프트로 확장

이미지 모드 요약:
- **Mode A (시네마틱)**: 포토리얼, 사람 중심, 극적 자연광, 웜톤 빈티지
- **Mode B (컨셉추얼)**: 메타포 오브젝트, 클린 스튜디오, 미니어처/디오라마, Wes Anderson 감성

상세 스펙은 [references/card-specs.md](references/card-specs.md) 참조.

### Step 5. 유저에게 제시 → 승인/수정

아래 Output 포맷으로 기획서를 작성하여 유저에게 제시. 승인받을 때까지 수정 반복.

## Output 포맷

```markdown
# Storyteller Plan: [주제]

> Date: YYYY-MM-DD
> Cards: N장 (표지 1 + 내용 N)
> Content Card Type: [card-white / card-image-overlay / card-image-top]
> Image Mode: [A (시네마틱) / B (컨셉추얼) / N/A]
> Brand: [브랜드명]

---

## Card 1 — 표지 (card-gradient)

- **top-label**: [19자 이내]
- **title**: [9자×2~3줄, <br>로 줄바꿈]
- **subtitle**: [19자 이내]
- **brand**: [브랜드명]

## Card 2 — 내용 ([카드타입])

- **title**: [글자수 제한 내]
- **body-text**: [글자수 제한 내, <br>로 줄바꿈] (card-white/image-top만)
- **subtitle**: [19자 이내] (card-image-overlay만)
- **brand**: [브랜드명]
- **image-concept**: "[장면/컨셉 한 줄 설명]" (이미지 카드만)

...(반복)...

---

## 승인 요청

위 구성을 확인해주세요:
1. 카드 수: N장
2. 내용 카드 타입: [타입]
3. 이미지 모드: [모드]
4. 각 카드 카피 & 이미지 컨셉

수정할 부분이 있으면 말씀해주세요. 승인하시면 다음 단계로 넘어갑니다.
```

## 설정

- **brand**: 유저가 지정 가능 (기본값: `AI STORYTELLER`)
- 시리즈별로 변경될 수 있으므로 실행 시 확인

## 참조 파일

- [references/card-specs.md](references/card-specs.md) — 카드 타입별 텍스트 필드 상세 규격, 이미지 모드 상세, 카드 타입 선택 가이드
