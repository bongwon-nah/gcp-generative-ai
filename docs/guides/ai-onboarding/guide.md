# AI Assistant 프로젝트 온보딩 가이드

> 최종수정: 2026-04-11
> 용도: Claude Code(및 호환 AI Assistant)가 새 프로젝트에 투입될 때, `docs/guides/` 아래의 가이드 체계를 **읽기에 그치지 않고 즉시 일관된 방식으로 부트스트랩까지 수행**하도록 강제한다.
> 적용 시점:
> - 프로젝트 최초 투입 → **풀 온보딩 (§3)** 1회 수행
> - 이후 매 세션 시작 → **Preflight (§2)** 만 수행
> 분량 제약: 없음. 이 가이드는 CLAUDE.md가 아니다. `docs/guides/claude-md-guide/guide.md` §7.1의 200줄 제약은 여기에 적용되지 않는다.

---

## 0. 이 가이드가 존재하는 이유

과거 한 프로젝트의 초기 세션에서 AI Assistant는 "`docs/guides/` 아래 하위 문서를 읽고 실행하라"는 사용자 지시를 받고도 다음 실수를 저질렀다:

1. 일부 가이드만 훑고 전수 독해는 하지 않았다.
2. 읽은 가이드가 전제하는 산출물(`CLAUDE.md`, `.claude/rules/`, `tasks/lessons.md`, `docs/pdca/` 등)이 실제로 존재하는지 **검증하지 않았다**.
3. 가이드에 명시된 rule을 따르는 듯 행동했지만, 전제 파일이 없었기 때문에 `세션 연속성 확인`, `lessons 리뷰`, `PDCA 캡처` 같은 절차는 실질적으로 **silent no-op** 상태였다.
4. 사용자가 세션 말미에 "가이드를 준수했느냐"고 물어본 뒤에야 실토했고, 재발 방지 대책을 그때서야 제출했다.

이 문서는 **동일한 실수를 구조적으로 불가능하게 만들기 위한 결정론적 체크리스트**다. 서술형 권고가 아니라, 건너뛸 수 없는 순서·검증·리포트 단계로 이루어진다.

---

## 1. 핵심 원칙 (Non-Negotiable)

1. **"읽기"와 "적용"은 분리되지 않는다.** 가이드를 읽었다면, 그 가이드가 전제하는 모든 파일/디렉토리의 존재 여부를 즉시 검증하고, 누락분을 부트스트랩 대상으로 잡아야 한다. "읽었음"과 "적용 완료"는 같은 턴에 끝낸다.
2. **Preflight 없이 작업 시작 금지.** 모든 세션의 첫 도구 사용은 §2 Preflight다. 예외 없다.
3. **부트스트랩 실행 전 Plan 승인.** `.claude/`, `CLAUDE.md`, `tasks/`, `docs/pdca/` 등 프로젝트 구조를 만드는 것은 아키텍처 결정이다. `.claude/rules/sdd-workflow.md`의 "Plan 필수" 조항이 그대로 적용된다. 사용자 승인 없이 파일을 생성하지 않는다.
4. **전수 독해, 건너뛰기 금지.** `docs/guides/` 하위의 모든 `.md` 파일을 읽는다. 제목이나 길이를 이유로 스킵하지 않는다.
5. **자가 검증 리포트 없이 온보딩 종료 선언 금지.** §3 Step 5의 체크리스트를 돌리고 결과를 사용자에게 보고해야 온보딩이 끝난다.

---

## 2. Preflight — 매 세션 시작 시 반드시 수행

세션의 첫 Bash/도구 호출로 다음을 수행한다. 사용자 질문이 무엇이든 관계없다.

### 2.1 단계 A — 가이드 기반 산출물 존재 확인

```bash
ls CLAUDE.md tasks/lessons.md \
   .claude/rules .claude/agents .claude/commands \
   docs/pdca 2>&1
```

### 2.2 단계 B — 가이드 본체 존재 확인

```bash
ls docs/guides/README.md \
   docs/guides/claude-md-guide/guide.md \
   docs/guides/claude-rules/guide.md \
   docs/guides/harness-patterns/guide.md \
   docs/guides/superclaude-workflow/guide.md \
   docs/guides/ai-onboarding/guide.md 2>&1
```

### 2.3 판정 분기

- **단계 A의 모든 산출물이 존재** → 섹션 2.4로 진행.
- **단계 A 산출물이 하나라도 누락** → 즉시 §3 **풀 온보딩** 절차로 진입.
- **단계 B의 가이드 본체가 누락** → 사용자에게 "가이드 체계가 불완전합니다"를 보고하고 중단. 가이드 없이 rule을 지어내지 않는다.

### 2.4 세션 Preflight에서 반드시 읽을 문서

Preflight가 PASS(단계 A/B 모두 존재)인 경우, 다음 파일을 Read tool로 명시적으로 읽는다:

1. `tasks/lessons.md` — 이전 세션에서 축적된 교훈을 현 세션 행동에 반영.
2. `CLAUDE.md`의 "Current Work" 섹션 — 진행 중 작업 존재 시 사용자에게 재개 여부 확인.
3. `.claude/rules/sdd-workflow.md` — 현 세션이 SDD 모드로 운영됨을 재확인.

이 세 파일을 읽기 전에 본 작업을 시작하지 않는다.

---

## 3. 풀 온보딩 절차 — 최초 1회

이 절차는 순차적이다. 이전 Step이 끝나지 않은 상태에서 다음 Step으로 점프하지 않는다.

### Step 1 — `docs/guides/` 전수 독해

1. `docs/guides/` 하위의 모든 `.md` 파일을 Glob으로 열거한다:
   ```
   docs/guides/**/*.md
   ```
2. 열거된 모든 파일을 Read tool로 읽는다. 파일 수와 읽은 파일 수가 일치해야 한다.
3. 읽은 파일 경로를 내부적으로 기록해 둔다. Step 5 리포트에서 사용한다.

**금지**: "관련성이 낮아 보인다"는 이유로 특정 파일을 건너뛰는 것. 전수 독해다.

### Step 2 — 가이드가 전제하는 산출물 매핑

각 가이드가 "존재해야 한다"고 전제하는 파일/디렉토리를 추출한다. 기본 매핑은 아래와 같다 (프로젝트가 가이드를 추가/수정했다면 그 항목도 포함한다):

| 출처 가이드 | 전제 산출물 | 필수 여부 |
|---|---|---|
| `claude-md-guide/guide.md` §7.1 | `CLAUDE.md` (프로젝트 루트, 영문 본문, <200줄) | 필수 |
| `claude-rules/guide.md` §7.2 | `.claude/rules/sdd-workflow.md` | 필수 |
| `claude-rules/guide.md` §7.2 | `.claude/rules/code-conventions.md` | 필수 (스택 결정 후) |
| `claude-rules/guide.md` §7.3 | `.claude/agents/spec-writer.md` | 필수 |
| `claude-rules/guide.md` §7.3 | `.claude/agents/service-builder.md` | 필수 |
| `claude-rules/guide.md` §7.3 | `.claude/agents/test-validator.md` | 필수 |
| `claude-rules/guide.md` §7.4 | `.claude/commands/sdd.md` | 필수 |
| `claude-rules/guide.md` §7.5 | `.claude/skills/` | 선택 (빈 디렉토리 OK) |
| `harness-patterns/guide.md` §9.2 | `tasks/lessons.md` | 필수 |
| `harness-patterns/guide.md` §9.3 | `CLAUDE.md`의 "Current Work" 섹션 | 필수 (CLAUDE.md 내부) |
| `superclaude-workflow/guide.md` §7, §8 | `docs/pdca/` | 필수 |
| `superclaude-workflow/guide.md` §8 | `specs/`, `tasks/` 분류 체계 | 필수 (초기엔 빈 디렉토리 + README) |
| `ai-onboarding/guide.md` (본 문서) | Preflight가 PASS 가능한 상태 | 필수 |

각 항목에 대해 현재 존재 여부를 `ls`로 체크하고, 존재/누락을 분리해 기록한다.

### Step 3 — 부트스트랩 Plan 작성 및 사용자 승인 게이트

다음 형식으로 Plan을 사용자에게 제시한다:

```
## 온보딩 부트스트랩 Plan

### 읽은 가이드 (N개)
- docs/guides/README.md
- docs/guides/claude-md-guide/guide.md
- docs/guides/claude-rules/guide.md
- docs/guides/harness-patterns/guide.md
- docs/guides/harness-patterns/claude-code-session-management-guide.md
- docs/guides/superclaude-workflow/guide.md
- docs/guides/ai-onboarding/guide.md
- (기타 발견된 가이드 전부)

### 현 상태
- 존재 (OK): [경로 목록]
- 누락 (TO BE CREATED): [경로 목록]

### 생성/수정 예정 산출물
1. CLAUDE.md — 신규 작성
2. .claude/rules/sdd-workflow.md
3. .claude/rules/code-conventions.md
4. .claude/agents/spec-writer.md
5. .claude/agents/service-builder.md
6. .claude/agents/test-validator.md
7. .claude/commands/sdd.md
8. tasks/lessons.md
9. docs/pdca/README.md (또는 .gitkeep)
10. specs/README.md (또는 .gitkeep)

### 사용자 결정 필요 항목 (부트스트랩 전 확인)
- 프로젝트 목적 및 현 단계 (CLAUDE.md Project Purpose / Current Stage 섹션)
- 프로젝트 언어/프레임워크 (code-conventions.md 작성 전 필수)
- 사용할 모델 조합 (Opus/Sonnet/Haiku 역할 분담)
- 서브 에이전트 확장 여부 (기본 3개 외 추가 필요성)
- `.claude/settings.local.json` 생성 여부 및 권한 모드
```

**사용자의 명시적 승인("승인", "진행", "go") 이전에는 어떤 파일도 생성하지 않는다.** 이 게이트는 건너뛸 수 없다.

### Step 4 — 부트스트랩 실행

승인을 받은 뒤 Plan에 명시된 산출물을 순서대로 생성한다. 생성 시 각 가이드의 제약을 엄격히 지킨다:

- **CLAUDE.md**
  - 본문 영어, 200줄 미만 (`claude-md-guide/guide.md` §7.1)
  - 포함: Bash 명령, 기본값과 다른 코드 스타일, 테스트 명령, 프로젝트 고유 아키텍처 결정
  - 제외: 코드에서 유추 가능한 내용, 자명한 지침, 전체 API 문서
  - 권장 섹션 순서: Project Purpose / Current Stage / Key References / Target Tech Stack / Workflow Rules / Sub-Agent Roster / Slash Commands / Skills / Key Commands / Current Work / Known Unknowns / Changelog
- **`.claude/rules/*.md`**
  - YAML frontmatter 없이 단순 Markdown OK (rules는 자동 로드)
  - `sdd-workflow.md`는 SPEC→Plan→Implement→Test→Commit 순서, 2-strike rule, 금지 사항, 각 금지에 대한 대안을 반드시 포함
- **`.claude/agents/*.md`**
  - YAML frontmatter 필수: `name`, `description`, `tools`, `model`
  - 각 agent의 역할·입력·출력·제한을 본문에 명시
- **`.claude/commands/sdd.md`**
  - `/sdd <target>` 진입점 정의
  - Pre-flight → SPEC → SPEC review → Plan → Plan review → Implement → Validate → Commit → Postmortem 단계 포함
  - 각 단계의 산출물 경로 명시
- **`tasks/lessons.md`**
  - 상단에 목적과 형식 설명
  - "Date — Title → Context → Mistake/Insight → Rule going forward" 형식
  - 최초 entry로 "온보딩 수행 완료 + 참조 가이드 목록" 기록
- **`docs/pdca/`**
  - 빈 디렉토리여도 `README.md` 또는 `.gitkeep`으로 존재를 가시화
- **`specs/`, `tasks/`**
  - 빈 디렉토리 + README.md로 역할 명시

### Step 5 — 자가 검증 리포트

다음 체크리스트를 돌리고 사용자에게 결과를 제출한다. 체크리스트를 돌리지 않고 온보딩 종료를 선언하지 않는다.

```
[ ] docs/guides/ 하위의 모든 .md 파일을 Read tool로 읽었다 (파일 수 일치)
[ ] Step 2 매핑표의 모든 "필수" 산출물을 생성했다
[ ] CLAUDE.md가 claude-md-guide §7.1 제약을 만족한다 (영문 본문, 200줄 미만)
[ ] .claude/rules/, .claude/agents/, .claude/commands/가 claude-rules §7.2-7.4와 일치한다
[ ] tasks/lessons.md가 형식 템플릿과 최초 entry를 포함한다
[ ] docs/pdca/가 존재한다
[ ] 다음 세션의 §2 Preflight가 단계 A/B 모두 PASS할 상태다
[ ] CLAUDE.md "Changelog" 섹션에 "온보딩 부트스트랩 완료" 일자가 기록되었다
[ ] 생성된 파일 중 어느 것도 가이드의 "제외" 조항을 위반하지 않는다
```

리포트 형식:

```
## 온보딩 완료 리포트

### 읽은 가이드 (N개)
- [경로 목록]

### 생성한 산출물 (M개)
- [경로 목록]

### 건너뛴 항목
- [있다면 항목과 사유]

### 자가 검증 결과
- PASS / CONDITIONAL (조건부) / FAIL
- [각 체크박스 결과]

### 다음 액션 (사용자에게)
- [사용자 결정이 필요한 질문 or 첫 작업 제안]
```

리포트 제출 후에만 "온보딩 완료"를 선언할 수 있다.

---

## 4. 안티 패턴 (금지 행동)

1. **"가이드 읽었습니다"라고 말한 뒤 부트스트랩으로 넘어가지 않기** — 읽기와 적용은 같은 턴에 끝낸다.
2. **존재하지 않는 파일을 전제로 rule을 따르는 척** — Preflight 없이 작업 시작하는 것. silent no-op의 원인.
3. **사용자 승인 없이 `.claude/` 구조나 CLAUDE.md 생성** — 아키텍처 결정이므로 Plan 게이트 필수.
4. **`docs/guides/` 일부 가이드만 읽고 "관련성 낮음"을 이유로 스킵** — 전수 독해다.
5. **Preflight를 건너뛰고 첫 도구 호출로 본 작업 시작** — 매 세션의 첫 도구는 Preflight.
6. **자가 검증 리포트 없이 온보딩 종료 선언** — PASS/FAIL을 명시적으로 사용자에게 전달해야 완료다.
7. **가이드에 없는 산출물을 즉흥적으로 추가** — 가이드가 요구하지 않은 파일/구조는 별도 Plan으로 제안하되, 온보딩 단계에서는 추가하지 않는다.
8. **사용자가 "빠르게 해줘"라고 하더라도 Preflight 생략** — 속도 요청은 절차 생략의 정당화가 되지 않는다. 절차 자체가 속도를 보장하는 장치다.

---

## 5. 매 세션 적용 요약 (TL;DR)

| 시점 | 수행 내용 |
|---|---|
| 프로젝트 최초 투입 | §3 풀 온보딩 (Step 1-5) → 리포트 제출 → 사용자 승인 후 작업 개시 |
| 이후 매 세션 시작 | §2 Preflight → `lessons.md`/`Current Work`/`sdd-workflow.md` 독해 → 작업 시작 |
| 가이드가 추가/변경된 경우 | 해당 세션 Preflight에서 diff 확인 → 필요 시 §3 Step 2-5 재수행 |
| 산출물 누락이 발견된 경우 | 작업을 멈추고 즉시 §3 풀 온보딩으로 재진입 |

---

## 6. 부록 — 가이드별 최소 요건 참조

각 산출물의 최소 요건을 한눈에 본다. 실제 작성 시엔 해당 가이드 원문을 함께 읽을 것.

### CLAUDE.md — `claude-md-guide/guide.md` §7.1
- 본문: 영어
- 분량: 200줄 미만
- 포함: Bash 명령, 기본값과 다른 코드 스타일, 테스트 명령, 프로젝트 고유 아키텍처 결정
- 제외: 코드에서 유추 가능한 것, 자명한 지침, 전체 API 문서
- 모든 금지에는 대안 병기

### `.claude/rules/sdd-workflow.md` — `claude-rules/guide.md` §7.2
- SPEC → Plan → Implement → Test → Commit 순서 명시
- SPEC 없는 구현, 시간 추정, `--no-verify`, 강제 푸시, 목업 DB 통합 테스트 금지
- 2-strike rule: 같은 접근으로 2회 실패 시 `/clear` 후 재계획
- 금지에는 대안 병기

### `.claude/rules/code-conventions.md` — `claude-rules/guide.md` §7.2
- 언어, 프레임워크, 서비스 구조, 테스트 규칙, Docker, 문서 규칙
- 스택 결정 전이면 placeholder + TODO 명시

### `.claude/agents/*.md` — `claude-rules/guide.md` §7.3
- 최소 3개: spec-writer / service-builder / test-validator
- YAML frontmatter: name, description, tools, model
- 스윗 스팟 3-4개; 5개 이상은 정당화 필요
- 같은 파일을 여러 에이전트가 편집하지 않도록 역할 분리

### `.claude/commands/sdd.md` — `claude-rules/guide.md` §7.4
- `/sdd <target>` 진입점
- Pre-flight / SPEC / SPEC review / Plan / Plan review / Implement / Validate / Commit / Postmortem 단계
- 각 단계 산출물 경로 명시

### `tasks/lessons.md` — `harness-patterns/guide.md` §9.2
- 형식: Date — Title → Context → Mistake/Insight → Rule going forward
- 매 세션 시작 시 읽기
- 사용자 correction 또는 non-obvious success 발생 시 즉시 append

### `docs/pdca/` — `superclaude-workflow/guide.md` §7
- feature별 디렉토리: `plan.md` / `do.md` / `check.md` / `act.md`
- 초기엔 빈 상위 디렉토리 + placeholder README OK

---

## 7. 이 가이드의 변경 관리

이 온보딩 가이드 본체가 변경되면:

1. 파일 상단의 "최종수정" 일자를 갱신한다.
2. 변경 항목을 본 파일 하단 Changelog에 한 줄로 기록한다.
3. 이미 온보딩을 마친 프로젝트의 AI Assistant는 다음 세션 Preflight 단계에서 이 파일의 diff를 확인하고, 필요 시 §3 Step 2-5를 재수행해 새 요건을 충족시킨다.

---

## Changelog

- **2026-04-11** — 최초 작성. 과거 세션에서 발생한 "가이드 읽기 → 미적용 → 사용자 지적 후 실토" 실수의 재발 방지를 위해 결정론적 체크리스트 + 자가 검증 리포트 형태로 문서화.
