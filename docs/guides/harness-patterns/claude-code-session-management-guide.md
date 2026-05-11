# Claude Code 세션 관리 최적화 가이드

> 기본 Claude Code 및 SuperClaude 환경에서의 세션 전환 워크플로우

## 1. 왜 세션을 나눠야 하는가?

세션이 길어지면 AI의 능력 자체가 저하되는 것이 아니라, **컨텍스트 윈도우 관리** 문제가 발생한다:

- 이전 대화가 자동 압축(auto-compaction)되면서 **초기 지시사항이나 맥락이 유실**
- CLAUDE.md에 기록된 내용은 압축에서 살아남지만, 대화로만 전달한 지시는 사라짐
- 오래된 컨텍스트가 토큰을 점유하여 새 응답에 쓸 수 있는 공간이 줄어듦
- **결론:** 작업 단위로 세션을 나누는 것이 품질 유지에 효과적

---

## 2. 기본 Claude Code 세션 관리

### 2.1 핵심 도구

| 도구 | 용도 |
|------|------|
| **CLAUDE.md** | 프로젝트 규칙/컨벤션 영구 저장 (모든 세션에서 자동 로드, 200줄 이하 권장) |
| **Auto Memory** | Claude가 학습한 내용 자동 저장 (`/memory`로 확인) |
| **`.claude/rules/`** | 파일 경로별 조건부 규칙 로드 (주제별 모듈화) |
| **`/rename`** | 세션에 이름 부여 → 나중에 `/resume`으로 복귀 |
| **`/clear`** | 컨텍스트 초기화 (새 대화 시작) |
| **`/resume`** | 이전 세션으로 복귀 |
| **`/compact [focus]`** | 세션 초기화 없이 컨텍스트만 압축 |
| **`/context`** | 현재 컨텍스트 사용량 시각화 |
| **`/copy`** | 대화 내보내기 |
| **`/export`** | 플레인텍스트로 내보내기 |

### 2.2 세션 전환 워크플로우

```
┌─────────────────────────────────────────────┐
│           세션 종료                            │
│                                             │
│  1. 테스트 실행, 변경사항 커밋                   │
│     git add ... && git commit -m "..."      │
│                                             │
│  2. /rename "인증 기능 구현 완료"               │
│     → 나중에 /resume으로 복귀 가능              │
│                                             │
│  3. 중요 맥락을 CLAUDE.md 또는 Memory에 저장    │
│     → "이것을 기억해줘" 로 요청                  │
│                                             │
│  4. /clear                                  │
│     → 세션 초기화                              │
└─────────────────────────────────────────────┘
                     ↓
┌─────────────────────────────────────────────┐
│           새 세션 시작                         │
│                                             │
│  • CLAUDE.md + Auto Memory 자동 로드됨        │
│                                             │
│  • 추가 맥락이 필요하면 수동으로 전달:           │
│    "이전 세션에서 인증 모듈을 구현했고,          │
│     다음은 권한 관리 기능을 추가할 차례입니다.    │
│     관련 파일: src/auth/*, src/middleware/*"  │
│                                             │
│  • 이전 세션 참조가 필요하면: /resume           │
└─────────────────────────────────────────────┘
```

### 2.3 컨텍스트 영구 저장 3가지 방법

**A. CLAUDE.md (수동, 영구 지시사항)**
- `./CLAUDE.md` — 프로젝트 레벨 (git 공유)
- `~/.claude/CLAUDE.md` — 사용자 레벨 (모든 프로젝트)
- `./CLAUDE.local.md` — 개인용 (gitignore)
- `@path/to/file` 문법으로 다른 파일 참조 가능

**B. Auto Memory (Claude 자동 학습)**
- 위치: `~/.claude/projects/<project>/memory/`
- MEMORY.md 인덱스 파일의 처음 200줄이 세션 시작 시 자동 로드
- `/memory` 명령으로 확인/편집/삭제

**C. .claude/rules/ (모듈형 규칙)**
- 위치: `./.claude/rules/*.md`
- YAML 프론트매터로 특정 파일 경로에만 적용되도록 스코핑 가능

### 2.4 세션 중간 팁

- **`/compact focus:API변경사항`** — 초기화 없이 특정 주제 중심으로 컨텍스트 압축
- **서브에이전트 위임** — 무거운 작업(테스트, 로그 분석)을 서브에이전트에 맡겨 메인 컨텍스트 보호
- **`/context`** — 컨텍스트 사용량 확인하여 적절한 시점에 세션 전환 판단

---

## 3. SuperClaude 환경 세션 관리

### 3.1 핵심 차이점

| | 기본 Claude Code | SuperClaude |
|---|---|---|
| **컨텍스트 보존** | CLAUDE.md + Auto Memory (수동) | Serena MCP Memory (자동 + 구조화) |
| **세션 종료** | `/rename` → `/clear` | **`/sc:save`** → `/clear` |
| **세션 시작** | 수동으로 맥락 설명 | **`/sc:load`** → 자동 복원 |
| **작업 회고** | 없음 | **`/sc:reflect`** (PDCA Check) |
| **오케스트레이션** | 사용자가 직접 관리 | **PM Agent** 자동 관리 |
| **학습 축적** | 단순 메모리 파일 | PDCA 문서 체계 |

### 3.2 세션 전환 워크플로우

```
┌─────────────────────────────────────────────┐
│           세션 종료 (3단계)                    │
│                                             │
│  1. /sc:reflect --type session --validate   │
│     → 작업 회고 + 품질 검증                    │
│     → 목표 대비 달성도 평가                     │
│     → 남은 작업과 블로커 식별                   │
│                                             │
│  2. /sc:save --type all --checkpoint        │
│     → Serena MCP memory에 저장:             │
│       session/last (세션 요약)               │
│       session/checkpoint (상태 스냅샷)        │
│       session/context (PM Agent 전체 상태)   │
│       next_actions (다음 할 일)              │
│       learning/patterns/* (성공 패턴)        │
│       learning/mistakes/* (실패 원인)        │
│                                             │
│  3. /clear                                  │
│     → 세션 초기화                              │
└─────────────────────────────────────────────┘
                     ↓
┌─────────────────────────────────────────────┐
│           세션 시작 (2단계)                    │
│                                             │
│  1. /sc:load --type checkpoint --analyze    │
│     → 자동 수행:                              │
│       list_memories() → 저장된 상태 확인       │
│       read_memory("pm_context") → PM 복원    │
│       read_memory("current_plan") → 계획     │
│       read_memory("last_session") → 요약     │
│       read_memory("next_actions") → 할 일    │
│     → 자동 보고:                              │
│       "Previous: [이전 세션 요약]              │
│        Progress: [현재 진행 상태]              │
│        Next: [계획된 다음 작업]                │
│        Blockers: [이슈/블로커]"               │
│                                             │
│  2. 다음 작업 지시                             │
│     → 재설명 없이 바로 이어서 작업              │
└─────────────────────────────────────────────┘
```

### 3.3 PDCA 문서 체계

SuperClaude는 단순 메모리가 아닌 **구조화된 학습 시스템**을 운영한다:

```
docs/
├── pdca/[feature-name]/     # 기능별 PDCA 사이클
│   ├── plan.md              # 가설, 설계, 예상 결과
│   ├── do.md                # 실행 로그, 시행착오 기록
│   ├── check.md             # 결과 vs 예상 비교 평가
│   └── act.md               # 개선 조치, 패턴 공식화
│
├── patterns/                # 검증된 성공 패턴 (재사용)
│   └── supabase-auth.md
│
├── mistakes/                # 실패 분석 + 예방 체크리스트
│   └── jwt-config-2026-04-09.md
│
└── temp/                    # 임시 가설/실험 (세션 종료시 정리)
    ├── hypothesis-2026-04-09.md
    └── experiment-2026-04-09.md
```

### 3.4 Serena MCP Memory 키 구조

```yaml
session/:
  session/context        # PM Agent 전체 상태 스냅샷
  session/last           # 이전 세션 요약
  session/checkpoint     # 30분 간격 체크포인트

plan/:
  plan/[feature]/hypothesis     # 가설과 설계
  plan/[feature]/architecture   # 아키텍처 결정

execution/:
  execution/[feature]/do        # 실행 로그
  execution/[feature]/errors    # 에러 기록

evaluation/:
  evaluation/[feature]/check    # 평가 결과
  evaluation/[feature]/lessons  # 교훈

learning/:
  learning/patterns/[name]      # 재사용 가능한 성공 패턴
  learning/solutions/[error]    # 에러 해결 DB
  learning/mistakes/[timestamp] # 실패 분석 + 예방책

project/:
  project/context               # 프로젝트 이해
  project/architecture          # 시스템 아키텍처
  project/conventions           # 코드 스타일, 네이밍
```

### 3.5 PM Agent 세션 라이프사이클

PM Agent는 수동 호출 없이 **매 세션 시작 시 자동 활성화**되며, 다음을 수행한다:

**세션 시작 (자동):**
1. `list_memories()` → 기존 상태 확인
2. `read_memory("pm_context")` → 전체 컨텍스트 복원
3. `read_memory("current_plan")` → 진행 중인 계획
4. `read_memory("last_session")` → 이전 요약
5. `read_memory("next_actions")` → 다음 할 일
6. 사용자에게 상태 보고 → 즉시 작업 재개 가능

**작업 중 (PDCA 연속 실행):**
1. **Plan** — `write_memory("plan", goal)` + `docs/pdca/[feature]/plan.md`
2. **Do** — 30분마다 `write_memory("checkpoint", progress)` + 시행착오 기록
3. **Check** — `think_about_task_adherence()` 자기 평가
4. **Act** — 성공 → `docs/patterns/`, 실패 → `docs/mistakes/`

**세션 종료:**
1. `think_about_whether_you_are_done()` 완료 평가
2. `write_memory("last_session", summary)` 세션 요약 저장
3. `write_memory("next_actions", todos)` 다음 할 일 저장
4. `write_memory("pm_context", complete_state)` 전체 상태 보존

### 3.6 주요 /sc 명령어 참조

| 명령어 | 용도 | 사용 시점 |
|--------|------|----------|
| `/sc:save` | 세션 컨텍스트 저장 | 세션 종료 전 |
| `/sc:save --type all --checkpoint` | 전체 저장 + 체크포인트 | 중요 마일스톤 완료 시 |
| `/sc:save --type learnings` | 학습 내용만 저장 | 새 패턴 발견 시 |
| `/sc:save --summarize` | 세션 요약 생성 | 세션 중간 정리 |
| `/sc:load` | 프로젝트 컨텍스트 로드 | 새 세션 시작 시 |
| `/sc:load --type checkpoint` | 체크포인트 복원 | 이전 상태로 복귀 |
| `/sc:load --type project --analyze` | 프로젝트 분석 포함 로드 | 오랜만에 재개 시 |
| `/sc:reflect --type session` | 세션 회고 | 세션 종료 전 |
| `/sc:reflect --type task --analyze` | 작업 검증 | 작업 완료 후 |
| `/sc:reflect --type completion` | 완료 판정 | 기능 구현 완료 시 |
| `/sc:pm` | PM Agent 명시적 호출 | 보통 자동이므로 선택적 |
| `/sc:task` | 개별 작업 실행 | 명확한 작업 단위 실행 |

---

## 4. 실전 시나리오 비교

### 시나리오: 인증 기능 구현 중 세션 전환

**기본 Claude Code 방식:**
```
# 세션 1 종료
/rename "인증 기능 - JWT 미들웨어까지 완료"
/clear

# 세션 2 시작 (수동으로 맥락 전달 필요)
"이전에 인증 기능을 구현하다 멈췄어. 
 src/auth/middleware.ts에서 JWT 검증까지 했고,
 다음은 권한 관리(RBAC) 구현이야.
 Supabase Auth를 쓰고 있고..."
```

**SuperClaude 방식:**
```
# 세션 1 종료
/sc:reflect --type session        # 자동 회고
/sc:save --type all --checkpoint  # 전체 상태 저장
/clear

# 세션 2 시작 (자동 복원)
/sc:load
# → "Previous: JWT 미들웨어 구현 완료 (coverage 87%)
#    Progress: 인증 기능 60% 완료
#    Next: RBAC 권한 관리 구현
#    Blockers: 없음"

"다음 작업 진행해줘"  # 끝. 재설명 불필요.
```

---

## 5. 베스트 프랙티스 요약

### 공통 원칙
- **하나의 기능/버그 = 하나의 세션**이 이상적
- CLAUDE.md는 **200줄 이하**로 유지
- `/context`로 컨텍스트 사용량을 모니터링하여 적절한 시점에 세션 전환

### 기본 Claude Code 사용 시
- `/rename` → `/clear` 순서를 습관화
- 중요 패턴은 반드시 CLAUDE.md 또는 Auto Memory에 기록
- `/compact focus:[주제]`로 초기화 없이 공간 확보 가능
- 무거운 작업은 서브에이전트에 위임하여 메인 컨텍스트 보호

### SuperClaude 사용 시
- `/sc:reflect` → `/sc:save` → `/clear` → `/sc:load` 플로우 습관화
- PDCA 문서 체계(`docs/pdca/`)를 프로젝트에 유지하여 학습 축적
- PM Agent의 자동 복원을 신뢰하되, 중요 결정은 CLAUDE.md에도 기록
- `/sc:save --type learnings`로 세션 중간에도 학습 내용 수시 저장

---

*작성일: 2026-04-09*
