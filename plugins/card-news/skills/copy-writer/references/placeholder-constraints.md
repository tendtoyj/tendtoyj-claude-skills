# Placeholder Constraints

> **Source of truth**: `skills/card-news-maker/references/TEMPLATE-GUIDE.md`
>
> 이 파일은 TEMPLATE-GUIDE.md의 글자수 제한을 참조하며,
> copy-writer 스킬 실행 시 필요한 추가 규칙만 보충합니다.

---

## 글자수 제한 — Quick Reference

TEMPLATE-GUIDE.md에서 추출한 요약 (상세 내용은 원본 참조):

| Field | Max | Used In |
|---|---|---|
| accent-text | **6** | Cover |
| highlight-keyword | **6** | Cover, Content-Image, Content-Features |
| cover-title | **6** | Cover |
| cover-subtitle | **20** | Cover |
| tag-N | **8** | Cover |
| category | **8** | Content-Image, Content-Features |
| title-line-1 (keyword 포함) | **12** | Content-Image, Content-Features |
| title-line-2 | **12** | Content-Image, Content-Features |
| body-line-N | **20** | Content-Image, Content-Features |
| icon-N-path | — | Content-Features |
| feature-N-title | **12** | Content-Features |
| feature-N-description | **25** | Content-Features |
| outro-line-N | **12** | Outro |
| account-handle | **15** | Outro |

---

## Copy-Writer 추가 규칙

### 글자수 카운팅

1. 한글 1자 = 1, 공백 1자 = 1, 영문/숫자 1자 = 1, 특수문자 1자 = 1
2. `title-line-1`에서 `highlight-keyword`가 인라인으로 포함됨 — **keyword + 나머지 합산이 12자 이내**
3. 한계치는 **hard maximum** — 1자라도 초과하면 시각적 오버플로우 발생

### 시각 폭 보정

- 한글은 같은 font-size에서 영문보다 넓게 렌더링됨
- 한영 혼용 placeholder는 실질적으로 **15% 적게** 써야 안전
- `…`, `!`, `.`는 좁고, `ㅋ`, `ㅎ`은 전각 폭

---

## 원본 파일

- **`skills/card-news-maker/references/TEMPLATE-GUIDE.md`** — placeholder별 상세 설명, 예시, 사용 팁
- **`skills/card-news-maker/references/card-news-template.html`** — 실제 HTML에서 placeholder 위치 확인
