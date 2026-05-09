# UI 디자인 가이드

> 이 문서는 **컴포넌트가 어떻게 보여야 하는가**를 정의한다. 새 화면/컴포넌트를 만들 때 여기서 벗어나면 안 된다.
> 일관성이 디자인의 핵심이다. 한 번 정한 토큰(색·간격·둥글기)을 새 화면마다 다르게 쓰지 마라.

---

## 1. 디자인 원칙
1. {원칙 1 — 예: "도구처럼 보여야 한다. 마케팅 페이지가 아니라 매일 쓰는 대시보드."}
2. {원칙 2 — 예: "정보 밀도를 우선한다. 여백은 가독성에 필요한 만큼만."}
3. {원칙 3 — 예: "장식 < 기능. 시각 효과는 동작과 직접 연결될 때만."}

각 원칙은 컴포넌트 리뷰 시 체크리스트로 사용된다.

## 2. AI 슬롭 안티패턴 — 절대 금지
| 금지 사항 | 이유 |
|-----------|------|
| `backdrop-filter: blur()` | glass morphism은 AI 템플릿의 가장 흔한 징후 |
| 그라데이션 텍스트 (`bg-clip-text`) | AI가 만든 SaaS 랜딩의 1번 특징 |
| "Powered by AI" 배지 | 기능이 아니라 장식. 사용자에게 가치 없음 |
| `box-shadow` 글로우 애니메이션 | 네온 글로우 = AI 슬롭 |
| 보라/인디고 브랜드 색상 | "AI = 보라색" 클리셰 |
| 모든 카드에 동일한 `rounded-2xl` | 균일한 큰 둥근 모서리 = 템플릿 느낌 |
| 배경 gradient orb (`blur-3xl` 원형) | 모든 AI 랜딩 페이지의 장식 |
| 무의미한 이모지 헤딩 | 진지한 도구로 보이지 않음 |

## 3. 디자인 토큰
모든 색·간격·타이포는 토큰으로 정의하고 컴포넌트는 토큰만 참조한다. 화면별 ad-hoc 값 금지.

### 3.1 색상
**배경**
| 토큰 | 값 | 용도 |
|------|----|----|
| `bg-page` | {예: #0a0a0a} | 페이지 |
| `bg-surface` | {예: #141414} | 카드, 패널 |
| `bg-elevated` | {예: #1c1c1c} | 호버, 모달 |

**텍스트**
| 토큰 | 값 | 용도 |
|------|----|----|
| `text-primary` | {예: #fafafa} | 제목, 강조 |
| `text-body` | {예: #d4d4d4} | 본문 |
| `text-muted` | {예: #a3a3a3} | 보조 정보 |
| `text-disabled` | {예: #737373} | 비활성 |

**시맨틱** — 의미가 다르면 색을 다르게.
| 토큰 | 값 | 용도 |
|------|----|----|
| `accent-success` | {예: #22c55e} | 성공/정상 |
| `accent-error` | {예: #ef4444} | 에러/위험 |
| `accent-warn` | {예: #eab308} | 경고/대기 |
| `accent-info` | {예: #3b82f6} | 정보/링크 |

### 3.2 간격
4px 베이스. 토큰만 사용 (Tailwind: `1=4px, 2=8px, 3=12px, 4=16px, 6=24px, 8=32px`).

| 용도 | 값 |
|------|----|
| 컴포넌트 내부 패딩 | {예: p-4 또는 p-6} |
| 컴포넌트 사이 | {예: gap-3 ~ gap-4} |
| 섹션 사이 | {예: space-y-8} |

### 3.3 둥근 모서리
| 용도 | 값 |
|------|----|
| 입력/버튼 | {예: rounded-md (6px)} |
| 카드/패널 | {예: rounded-lg (8px)} |
| 모달/큰 영역 | {예: rounded-xl (12px)} |

> 동일한 컴포넌트군은 동일한 둥글기를 쓴다. 한 화면에서 둥글기를 섞지 마라.

### 3.4 타이포그래피
| 용도 | 스타일 |
|------|--------|
| 페이지 제목 (H1) | {예: text-3xl font-semibold tracking-tight} |
| 섹션 제목 (H2) | {예: text-xl font-semibold} |
| 카드 제목 | {예: text-sm font-medium uppercase tracking-wide text-muted} |
| 본문 | {예: text-sm leading-relaxed text-body} |
| 메타/캡션 | {예: text-xs text-muted} |
| 코드 | {예: font-mono text-xs} |

## 4. 컴포넌트 규격
모든 컴포넌트는 토큰만 사용. raw 16진수 컬러 사용 금지.

**카드**
```
rounded-lg bg-surface border border-neutral-800 p-6
```

**버튼**
```
Primary:   rounded-md bg-white text-black hover:bg-neutral-200 px-4 py-2 text-sm font-medium
Secondary: rounded-md border border-neutral-800 text-primary hover:bg-elevated px-4 py-2 text-sm
Text:      text-muted hover:text-primary text-sm
Danger:    rounded-md bg-accent-error/10 text-accent-error hover:bg-accent-error/20 px-4 py-2 text-sm
```

**입력 필드**
```
rounded-md bg-neutral-900 border border-neutral-800 px-3 py-2 text-sm
focus: border-neutral-600 outline-none
error: border-accent-error
```

**상태 표시 (status pill)**
```
Success: text-accent-success bg-accent-success/10 px-2 py-0.5 rounded text-xs
Error:   text-accent-error  bg-accent-error/10  px-2 py-0.5 rounded text-xs
Pending: text-muted         bg-neutral-800       px-2 py-0.5 rounded text-xs
```

## 5. 레이아웃
- 최대 너비: {예: max-w-5xl mx-auto}
- 정렬: 좌측 정렬 기본. 중앙 정렬은 빈 상태 화면에만.
- 그리드: {예: 데스크톱 3열, 태블릿 2열, 모바일 1열}
- 반응형 브레이크포인트: 모바일 우선 (Tailwind: sm/md/lg/xl)

## 6. 애니메이션
**허용**
- `fade-in` 0.2~0.4s — 마운트 시
- `slide-up` 0.3s — 모달/토스트 진입
- 호버 색상 전환 0.15s

**금지**
- 무한 루프 애니메이션 (스피너 제외)
- bounce, wobble 등 장식적 모션
- 페이지 진입 시 모든 요소가 stagger 애니메이션

## 7. 아이콘
- {예: Lucide icons, strokeWidth 1.5}
- 크기: 16/20/24px만 사용
- 컬러: `text-*` 토큰 상속 — `currentColor`
- 아이콘 컨테이너(둥근 배경 박스)로 감싸지 마라

## 8. 접근성 (필수)
모든 컴포넌트는 아래를 만족해야 한다.

- [ ] 텍스트 대비 WCAG AA 이상 (`text-body` on `bg-page` ≥ 4.5:1)
- [ ] 키보드만으로 모든 인터랙션 가능 (Tab/Enter/Esc)
- [ ] 포커스 표시 명확 (`focus-visible:ring-2`)
- [ ] 폼 입력에 `<label>` 또는 `aria-label`
- [ ] 아이콘 단독 버튼에 `aria-label`
- [ ] 색상 단독으로 의미 전달 금지 (텍스트/아이콘 병기)
- [ ] 동적 콘텐츠 변경 시 `aria-live` 적용 (토스트, 로딩)

## 9. 빈 상태 / 에러 / 로딩
화면 모든 데이터 컴포넌트는 4가지 상태를 모두 정의한다.

| 상태 | 표현 |
|------|------|
| 로딩 | {예: 스켈레톤 — 실제 레이아웃과 동일한 공간 점유} |
| 빈 상태 | {예: 중앙 정렬 텍스트 + 다음 행동 CTA} |
| 에러 | {예: accent-error 텍스트 + 재시도 버튼} |
| 정상 | {기본} |

> 빈 상태에서 "데이터 없음"만 표시하지 마라. 사용자가 다음에 무엇을 할 수 있는지 알려줘라.

## 10. 일관성 검증 체크리스트
새 컴포넌트 머지 전 확인:
- [ ] 색·간격·둥글기를 모두 토큰으로 표현했는가
- [ ] 안티패턴(섹션 2)에 해당하는 요소가 없는가
- [ ] 4가지 상태(로딩/빈/에러/정상) 모두 처리했는가
- [ ] 접근성 체크리스트(섹션 8) 통과
- [ ] 다크/라이트 모드를 모두 지원하는가 (해당 시)
- [ ] 기존 동급 컴포넌트와 시각적으로 한 가족처럼 보이는가
