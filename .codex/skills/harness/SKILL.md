---
name: harness
description: Plan, create, review, and execute Harness phase workflows for Codex. Use when the user asks to break implementation into self-contained steps, create or update `phases/` files, run `scripts/execute.py`, recover failed or blocked Harness steps, or review changes against `AGENT.md`, `docs/ARCHITECTURE.md`, and `docs/ADR.md`.
---

# Harness

## Overview

Use this skill to run a Codex-native Harness workflow: read project guardrails, design small self-contained steps, write phase files, and execute them sequentially with `scripts/execute.py`.

## Workflow

1. Read `AGENT.md` and the relevant files under `docs/`, especially `PRD.md`, `ARCHITECTURE.md`, `ADR.md`, and `UI_GUIDE.md` when UI is involved.
2. Clarify only decisions that cannot be safely inferred from local context.
3. Draft small steps before creating files when the user asks for an implementation plan.
4. After approval or when the user asks to proceed, create or update the phase files under `phases/`.
5. Execute with `python scripts/execute.py <task-name>` when the user wants the Harness run performed.
6. Review failures by reading `phases/<task-name>/stepN-output.json` and `phases/<task-name>/index.json`, then reset only the failed or blocked step after the cause is fixed.

## Step Design Rules

- Keep scope narrow: one layer or module per step. Split steps when multiple modules must change.
- Make every `stepN.md` self-contained. Do not rely on prior chat context.
- List required reading files explicitly, including docs and files created or modified by earlier steps.
- Give interface-level guidance and hard invariants, not full implementations.
- Express acceptance criteria as runnable commands.
- Write concrete prohibitions as "Do not do X. Reason: Y."
- Use kebab-case step names such as `project-setup`, `api-layer`, or `auth-flow`.

## Phase Files

Create `phases/index.json` when it does not exist:

```json
{
  "phases": [
    {
      "dir": "0-mvp",
      "status": "pending"
    }
  ]
}
```

Create `phases/{task-name}/index.json`:

```json
{
  "project": "<project-name>",
  "phase": "<task-name>",
  "steps": [
    { "step": 0, "name": "project-setup", "status": "pending" },
    { "step": 1, "name": "core-types", "status": "pending" },
    { "step": 2, "name": "api-layer", "status": "pending" }
  ]
}
```

Rules:

- `project` comes from `AGENT.md`.
- `phase` must match the task directory.
- `steps[].step` starts at `0`.
- Initial `steps[].status` is always `"pending"`.
- Do not prefill timestamps; `scripts/execute.py` records `created_at`, `started_at`, `completed_at`, `failed_at`, and `blocked_at`.

## Step Template

Use this shape for each `phases/{task-name}/step{N}.md`:

```markdown
# Step {N}: {name}

## 읽어야 할 파일

먼저 아래 파일들을 읽고 프로젝트의 아키텍처와 설계 의도를 파악하라:

- /AGENT.md
- /docs/ARCHITECTURE.md
- /docs/ADR.md
- {이전 step에서 생성/수정된 파일 경로}

## 작업

{구체적인 구현 지시. 파일 경로, 클래스/함수 시그니처, 로직 설명, 반드시 지켜야 할 불변식을 포함한다.}

## Acceptance Criteria

```bash
npm run build
npm test
```

## 검증 절차

1. 위 AC 커맨드를 실행한다.
2. 아키텍처 체크리스트를 확인한다:
   - ARCHITECTURE.md 디렉토리 구조를 따르는가?
   - ADR 기술 스택을 벗어나지 않았는가?
   - AGENT.md CRITICAL 규칙을 위반하지 않았는가?
3. 결과에 따라 `phases/{task-name}/index.json`의 해당 step을 업데이트한다:
   - 성공: `"status": "completed"`, `"summary": "산출물 한 줄 요약"`
   - 수정 3회 시도 후 실패: `"status": "error"`, `"error_message": "구체적 에러 내용"`
   - 사용자 개입 필요: `"status": "blocked"`, `"blocked_reason": "구체적 사유"` 후 즉시 중단

## 금지사항

- {이 step에서 하지 말아야 할 것. "X를 하지 마라. 이유: Y" 형식}
- 기존 테스트를 깨뜨리지 마라.
```

## Execution

Run:

```bash
python scripts/execute.py <task-name>
python scripts/execute.py <task-name> --push
```

`scripts/execute.py` handles:

- `feat-<task-name>` branch creation or checkout.
- Guardrail injection from `AGENT.md` plus `docs/*.md`.
- Summary carryover from completed steps.
- Up to 3 retries when a step does not mark itself completed, blocked, or errored.
- Separate commits for implementation changes and Harness metadata.
- Timestamp updates in phase indexes.

Set `CODEX_HARNESS_CMD` to override the Codex CLI command used by the executor. The prompt is appended as the final argument. The default is `codex exec --json`.

## Review

When asked to review Harness output, read `AGENT.md`, `docs/ARCHITECTURE.md`, and `docs/ADR.md`; inspect changed files; then report whether the changes satisfy architecture, ADR technology choices, tests, critical rules, and buildability.
