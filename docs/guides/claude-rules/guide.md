# Claude Code 설정 가이드 (.claude/ 디렉토리)

> 최종수정: 2026-04-09
> 원본: project_standard_guideline.md §7.2-7.5

---

### 7.2 .claude/rules/ (자동 로드)

CLAUDE.md와 중복하지 않는 상세 규칙을 배치한다:

| 파일 | 내용 |
|------|------|
| `code-conventions.md` | 언어, 프레임워크, 서비스 구조, Docker, 문서 규칙 |
| `sdd-workflow.md` | SDD 필수 순서, 금지 사항 |

---

### 7.3 .claude/agents/ (서브에이전트)

| 에이전트 | 역할 | 모델 | 도구 |
|----------|------|------|------|
| `spec-writer` | SPEC.md 작성 | opus | Read, Grep, Glob, Bash, Write |
| `service-builder` | FastAPI 서비스 구현 (TDD) | opus | Read, Grep, Glob, Bash, Write, Edit |
| `test-validator` | 테스트 실행 및 SPEC 대비 검증 (읽기 전용) | sonnet | Read, Grep, Glob, Bash |

서브에이전트 운용:
- 스윗 스팟: 3-4개 에이전트
- 탐색/리서치는 서브에이전트에 위임하여 메인 컨텍스트 보호
- 같은 파일을 여러 에이전트가 편집하지 않음
- Git Worktree 격리(`--worktree`)로 독립 브랜치 작업 가능

---

### 7.4 .claude/commands/

| 명령어 | 용도 |
|--------|------|
| `/sdd {대상}` | SDD 워크플로우 시작 (SPEC 확인 → Plan → 구현 → 검증) |

---

### 7.5 .claude/skills/

| 스킬 | 용도 |
|------|------|
| `/docker-service {서비스} --port {포트}` | Docker Compose에 서비스 추가 |
| `/plan-phase {Phase번호}` | Phase별 구현 계획 수립 |
