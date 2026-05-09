# 아키텍처

> 이 문서는 **시스템이 어떻게 조립되어 있는가**를 정의한다. *왜* 이렇게 설계했는가는 ADR.md, *무엇을* 만드는가는 PRD.md를 참조한다.
> 모든 step은 이 문서를 참조해 디렉토리 위치, 레이어 책임, 데이터 흐름을 결정한다. 여기서 벗어나는 코드는 생성하지 마라.

---

## 1. 시스템 한눈에 보기
```
{ASCII/텍스트 다이어그램으로 주요 컴포넌트와 흐름을 그린다.

  예:
  ┌─────────┐    ┌──────────┐    ┌────────────┐
  │ Browser │───▶│ Next App │───▶│ External   │
  │         │◀───│ (SSR+API)│◀───│ API        │
  └─────────┘    └──────────┘    └────────────┘
                       │
                       ▼
                  ┌─────────┐
                  │ SQLite  │
                  └─────────┘
}
```

## 2. 디렉토리 구조와 책임
각 디렉토리는 **단 하나의 책임**을 갖는다. 책임이 모호한 코드는 잘못된 위치다.

```
src/
├── app/          # 페이지 + API 라우트. 라우팅과 요청/응답 직렬화만.
├── components/   # UI 컴포넌트. 비즈니스 로직 금지.
├── services/     # 외부 시스템 래퍼 (HTTP, DB, 큐). 도메인 로직 금지.
├── lib/          # 순수 유틸 (date, format, validation). 사이드 이펙트 금지.
├── types/        # TypeScript 타입 정의. 런타임 코드 금지.
└── {추가 폴더}/  # {책임}
```

**금지 패턴**:
- 컴포넌트가 직접 외부 API 호출 → `services/`를 거쳐라
- `lib/`에 fetch/DB 호출 → `services/`로 옮겨라
- `types/`에 함수 구현 → 타입 선언만 허용

## 3. 레이어 의존 방향
의존은 **단방향**으로 흐른다. 역방향 import는 금지.

```
app  →  components  →  services  →  lib
                              ↘
                                types  (모든 레이어가 참조 가능)
```

- {추가 규칙. 예: "components는 services를 직접 호출하지 않고 props로 주입받는다"}

## 4. 데이터 흐름 (대표 시나리오)
**시나리오 1: {예: 사용자 검색}**
```
1. User 입력 → ClientComponent (components/SearchBox)
2. ClientComponent → POST /api/search (app/api/search/route.ts)
3. Route handler → searchService.query() (services/search.ts)
4. searchService → External API (with retry/cache)
5. 결과 직렬화 → ClientComponent → 렌더
```

**시나리오 2: {예: 인증}**
```
{단계별 흐름}
```

## 5. 상태 관리
| 종류 | 위치 | 도구 |
|------|------|------|
| 서버 상태 | RSC / API 응답 | {예: React Server Components 직접 fetch} |
| URL 상태 | searchParams | {예: nuqs 또는 표준 useSearchParams} |
| 클라이언트 UI 상태 | 컴포넌트 로컬 | {예: useState/useReducer} |
| 영속 상태 | DB | {예: SQLite + Drizzle} |

> **금지**: 전역 클라이언트 상태 라이브러리(Redux/Zustand) 도입 — 필요해지면 ADR을 작성하고 추가한다.

## 6. 외부 의존성
| 시스템 | 용도 | 장애 시 폴백 |
|--------|------|--------------|
| {예: OpenAI API} | {임베딩} | {로컬 캐시 사용, UI에 degraded 모드 표시} |
| {예: GitHub API} | {이슈 fetch} | {백오프 + 사용자에게 rate limit 안내} |

각 외부 호출은 `services/` 안에서 wrapping되며, 다음을 갖춰야 한다:
- timeout (기본 10초)
- 재시도 정책 (idempotent 호출만)
- 구조화된 에러 (`ServiceError` 타입)

## 7. 에러 처리 정책
- **사용자 에러** (입력 검증 실패): 4xx + 사용자에게 명확한 메시지
- **시스템 에러** (외부 API 실패): 5xx + 로그 기록 + 사용자에게는 "일시적 문제" 안내
- **프로그래밍 에러** (assertion fail): throw하고 상위에서 잡지 마라 — 빠른 실패가 낫다

> **CRITICAL**: catch한 에러를 silent로 무시하지 마라. 최소한 로깅하라.

## 8. 보안 경계
- **신뢰 경계**: {예: 모든 입력은 API 라우트에서 zod로 검증}
- **시크릿**: 환경 변수만 사용. 코드/git에 절대 포함 금지.
- **인증**: {예: NextAuth / 세션 쿠키 (httpOnly + secure)}
- **권한**: {예: route handler 시작에서 권한 체크 → unauthorized면 401}

## 9. 성능 목표
| 지표 | 목표 |
|------|------|
| 초기 페이지 로드 | {예: TTI < 2s on 4G} |
| API 응답 P95 | {예: < 500ms} |
| 번들 크기 | {예: 초기 JS < 200KB gzipped} |

## 10. 확장 포인트와 비확장 포인트
- **나중에 추가하기 쉬운 것**: {예: 새 데이터 소스 — services/에 어댑터 추가}
- **추가 시 큰 리팩터링이 필요한 것**: {예: 멀티 테넌트 — DB 스키마/세션 전반 수정 필요}

이 정보는 PRD에서 새 기능 검토 시 비용 추정의 근거가 된다.
