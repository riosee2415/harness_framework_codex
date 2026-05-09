# Architecture Decision Records

> 이 문서는 **왜 그렇게 설계했는가**의 근거를 누적한다. 시간이 지나도 결정의 맥락이 사라지지 않도록 한다.
> ADR을 추가해야 하는 시점:
> - 되돌리기 비싼 결정 (DB 선택, 인증 방식, 핵심 라이브러리)
> - 자명하지 않은 트레이드오프가 있는 결정
> - 상식과 다른 선택 (관행을 거스를 때는 반드시 기록)
>
> 작성하지 않아도 되는 시점: 라이브러리 마이너 버전 업, 변수명 같은 코드 컨벤션.

---

## 철학
{프로젝트의 핵심 가치관 3~5개. 각 ADR이 이 가치관과 충돌하지 않는지 검증한다.

예:
1. **MVP 속도 최우선** — 작동하는 최소 구현을 선택. 미래 확장은 ADR로 명시되기 전엔 가정하지 않는다.
2. **외부 의존성 최소화** — 1주 안에 직접 만들 수 있는 것은 라이브러리로 도입하지 않는다.
3. **명시적 > 마법** — 자동 추론보다 명시적 선언을 선호한다.
4. **되돌리기 비용 의식** — 결정의 비가역성을 측정하고 비가역적 결정에 더 많은 시간을 쓴다.
}

---

## 작성 형식
각 ADR은 아래 5개 필드를 채운다.

- **Status**: `Proposed` | `Accepted` | `Deprecated` | `Superseded by ADR-NNN`
- **Date**: YYYY-MM-DD
- **Context**: 어떤 상황·제약 때문에 결정이 필요한가
- **Decision**: 무엇을 선택했는가 (능동태로 명확히)
- **Consequences**: 좋은 점 / 포기한 점 / 새로 생긴 위험

상태가 바뀐 ADR은 삭제하지 말고 `Status` 필드만 업데이트한다 — 결정 이력이 유지되어야 한다.

---

### ADR-001: {결정 사항 한 줄 제목}
- **Status**: Accepted
- **Date**: {YYYY-MM-DD}
- **Context**:
  {왜 이 결정을 해야 했는가. 어떤 제약·요구사항이 있었는가. 한 문단.}
- **Decision**:
  {무엇을 선택했는가. 한두 문장으로 능동태.}
- **Considered alternatives**:
  - {대안 A} — {왜 채택하지 않았는가}
  - {대안 B} — {왜 채택하지 않았는가}
- **Consequences**:
  - ✓ {얻는 것}
  - ✗ {포기하는 것 / 새로 생긴 제약}
  - ⚠ {나중에 재검토할 트리거. 예: "사용자 100만 도달 시 분산 캐시 재검토"}

### ADR-002: {결정 사항}
- **Status**: Accepted
- **Date**: {YYYY-MM-DD}
- **Context**: …
- **Decision**: …
- **Considered alternatives**: …
- **Consequences**: …

### ADR-003: {결정 사항}
- **Status**: Proposed
- **Date**: {YYYY-MM-DD}
- **Context**: …
- **Decision**: …
- **Considered alternatives**: …
- **Consequences**: …

---

## 기술 스택 요약
ADR의 결정들이 모인 결과. 새 코드를 작성할 때 여기서 벗어나지 마라.

| 영역 | 선택 | 출처 ADR |
|------|------|----------|
| 언어 | {예: TypeScript strict} | ADR-001 |
| 프레임워크 | {예: Next.js 15 App Router} | ADR-001 |
| DB | {예: SQLite + Drizzle} | ADR-002 |
| 테스트 | {예: Vitest + Playwright} | ADR-003 |
| 배포 | {예: Vercel} | ADR-00? |

> **CRITICAL**: 위 표에 없는 기술을 도입하려면 먼저 새 ADR을 작성하고 사용자 승인을 받아라.
