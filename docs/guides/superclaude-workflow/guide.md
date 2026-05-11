# SuperClaude(/sc:) 표준 워크플로우 가이드

> 최종수정: 2026-04-09
> 용도: /sc: 명령 체계의 표준 사용 절차 및 상황별 활용법

---

## 1. 핵심 파이프라인 (기본 흐름)

```
/sc:brainstorm          요구사항 도출 (Socratic 대화)
      ↓                 Output: 요구사항 명세서
/sc:design              아키텍처 & 인터페이스 설계
      ↓                 Output: 설계 명세서
/sc:workflow            구현 계획 수립 (태스크 분해, 순서, 의존성)
      ↓                 Output: 워크플로우 계획
/sc:implement           실제 코드 구현
      ↓                 Output: 구현 코드
/sc:test                테스트 실행 & 커버리지
      ↓                 Output: 테스트 결과
/sc:improve             코드 품질 개선
      ↓                 Output: 리팩토링된 코드
/sc:git                 커밋 & 버전 관리
```

위는 가장 단순한 기본 흐름이다. 실제 프로젝트에서는 SDD(Spec-Driven Development) 가이드라인과 결합하여 Phase 기반으로 운영한다. (섹션 8 참조)

---

## 2. 각 단계의 역할과 경계

| 단계 | 명령 | 하는 것 | 하지 않는 것 |
|------|------|---------|-------------|
| 발견 | `/sc:brainstorm` | 요구사항 도출, 사용자 목표 명확화 | 코드 생성, 설계 결정 |
| 설계 | `/sc:design` | 아키텍처, API, 컴포넌트 설계 | 코드 구현 |
| 계획 | `/sc:workflow` | 태스크 분해, 실행 순서 결정 | 실제 구현 실행 |
| 구현 | `/sc:implement` | 코드 작성, 테스트 준비 | 독단적 아키텍처 변경 |
| 검증 | `/sc:test` | 테스트 실행, 커버리지 측정 | 코드 수정 |
| 개선 | `/sc:improve` | 안전한 리팩토링, 품질 향상 | 대규모 아키텍처 변경 |

---

## 3. 상황별 단축 경로

### 버그 수정 (간단한 경우)
```
/sc:troubleshoot → /sc:implement → /sc:test → /sc:git
```

### 코드 품질 개선
```
/sc:analyze → /sc:improve → /sc:test → /sc:git
```

### 기존 코드 이해
```
/sc:explain        # 특정 코드/개념 설명
/sc:index-repo     # 저장소 전체 구조 파악 (94% 토큰 절감)
```

### 뭘 해야 할지 모를 때
```
/sc:recommend      # 입력을 분석해서 적합한 명령 추천
```

### 문서 중심 프로젝트 (코드 구현 없음)
```
/sc:brainstorm → /sc:design → /sc:implement
# /sc:workflow 생략 가능
```

### 대규모 프로젝트 (병렬 처리)
```
Phase 1: /sc:brainstorm + /sc:design (병렬 탐색)
Phase 2: /sc:workflow (구현 계획)
Phase 3: /sc:implement (기능별 병렬 구현, --delegate 플래그)
Phase 4: /sc:analyze + /sc:test (병렬 검증)
Phase 5: /sc:document (문서화)
Phase 6: /sc:git (버전 관리)
```

---

## 4. 보조/유틸리티 명령

| 명령 | 용도 | 사용 시점 |
|------|------|----------|
| `/sc:research` | 외부 웹 리서치 (기술 조사, BP 탐색) | 설계 전 기술 검토 시 |
| `/sc:document` | 특정 컴포넌트 문서화 | 구현 완료 후 |
| `/sc:spec-panel` | 다중 전문가 명세 리뷰 | 설계 검증 시 |
| `/sc:estimate` | 개발 공수 추정 | 계획 수립 시 |
| `/sc:save` / `/sc:load` | 세션 컨텍스트 저장/복원 | 세션 전환 시 |
| `/sc:reflect` | 작업 회고 & 검증 | 구현 완료 후 |
| `/sc:cleanup` | 데드 코드 제거, 구조 최적화 | 리팩토링 시 |
| `/sc:index` | 프로젝트 문서 & 지식 베이스 생성 | 프로젝트 정리 시 |

---

## 5. 주요 실행 플래그

모든 `/sc:` 명령에 공통 적용 가능한 향상 플래그:

| 플래그 | 효과 |
|--------|------|
| `--think`, `--think-hard`, `--ultrathink` | 분석 깊이 조절 |
| `--safe-mode` | 최대 검증, 보수적 실행 |
| `--delegate [auto\|files\|folders]` | 서브에이전트 병렬 처리 |
| `--parallel` | 동시 실행 활성화 |
| `--validate` | 실행 전 리스크 평가 |
| `--context7` | Context7 MCP 활성화 (프레임워크 문서 조회) |
| `--sequential` | Sequential Thinking MCP 활성화 |

---

## 6. PM Agent 자동 오케스트레이션

`/sc:pm`은 세션 시작 시 자동 활성화되는 오케스트레이터:

```
1. 세션 시작 → PM Agent 자동 활성화
2. Serena MCP로 이전 세션 컨텍스트 복원
3. 이전 진행 상황 보고
4. 사용자 입력 대기
5. 입력 분석 → 적합한 /sc: 명령으로 자동 라우팅
   - 모호한 요청 → /sc:brainstorm 모드 진입
   - 명확한 버그 → /sc:troubleshoot 직행
   - 구현 요청 → /sc:implement 직행
```

---

## 7. PDCA 사이클 (PM Agent 내장)

PM Agent는 지속적 개선을 위한 PDCA 사이클을 자동 관리:

```
Plan  → docs/pdca/[feature]/plan.md     # 가설 및 계획 기록
Do    → docs/pdca/[feature]/do.md       # 실행 과정, 오류, 해결 기록
Check → docs/pdca/[feature]/check.md    # 예상 vs 실제 결과 비교
Act   → 성공 시: docs/patterns/[feature].md (패턴화)
        실패 시: docs/mistakes/[feature]-DATE.md (교훈 기록)
        → CLAUDE.md에 글로벌 학습 반영
```

---

## 8. SDD 통합 워크플로우 (실제 프로젝트 적용)

실제 프로젝트에서는 기본 흐름(섹션 1)에 **SDD(Spec-Driven Development)** 가이드라인을 결합하여 Phase 기반으로 운영한다. SDD는 "SPEC 없이 구현 금지"를 핵심 원칙으로 하며, 기능 단위로 SPEC → Plan → 구현 → 테스트를 반복한다.

### 8.1 전체 Phase 구조

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 Phase 1: 설계
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  /sc:brainstorm        → 요구사항 명세서
        ↓
  /sc:design            → C4 다이어그램 + ADR
        ↓
  /sc:spec-panel        → 아키텍처 정의서 검증
        ↓
  Phase 0 초기화        → 프로젝트 구조 + git init

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 Phase 2: 구현
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  /sc:workflow          → Phase별 구현 계획
        ↓                 (태스크 분해, 순서, 의존성)

  ┌─── 기능 단위 반복 (SDD 루프) ──────────────┐
  │                                            │
  │  SPEC 작성      → specs/{기능}_spec_*.md   │
  │      ↓           (spec-writer 에이전트)     │
  │  Plan 수립      → tasks/plan_*.md          │
  │      ↓           (사용자 승인 후 구현)      │
  │  /sc:implement  → 코드 구현 (TDD)          │
  │      ↓           (service-builder 에이전트) │
  │  /sc:test       → 테스트 실행 + 커버리지    │
  │      ↓           (test-validator 에이전트)  │
  │  /sc:git        → 커밋                     │
  │                                            │
  └──── 다음 기능으로 반복 ────────────────────┘

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 Phase 3: 품질 확보
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  /sc:analyze           → 코드 품질/보안 분석
  /sc:improve           → 리팩토링
  /sc:test              → 통합 테스트 + 커버리지 강화

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 Phase 4: 운영 준비
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  런북 작성, DB 백업 절차, CHANGELOG 정리
```

### 8.2 기본 흐름 vs SDD 통합 흐름 차이

| 항목 | 기본 흐름 (섹션 1) | SDD 통합 흐름 |
|------|-------------------|--------------|
| 구현 시작 전 | 바로 `/sc:implement` | **SPEC 작성 필수** → Plan 승인 → 구현 |
| 구현 단위 | 한 번에 전체 | **기능 단위 반복** (기능 A → 기능 B → ...) |
| 설계 검증 | 없음 | `/sc:spec-panel`로 아키텍처 정의서 검증 |
| `/sc:improve` 위치 | 구현 직후 | **Phase 3에서 통합** 수행 |
| 서브에이전트 활용 | 선택 | `spec-writer`, `service-builder`, `test-validator` 에이전트 활용 |
| 산출물 관리 | 자유 | `specs/`, `tasks/`, `docs/` 분류 체계 적용 |

### 8.3 SDD 루프의 핵심 규칙

1. **SPEC 없이 구현 시작 금지** — `spec-writer` 에이전트를 먼저 실행
2. **3단계 이상 또는 아키텍처 결정이 필요한 작업은 Plan 필수** — 사용자 승인 후 구현
3. **테스트 없이 기능 완료 선언 금지** — 최소 단위 테스트 + 헬스체크
4. **2회 수정 실패 시 같은 접근 반복 금지** — `/clear` 후 re-plan

### 8.4 적용 판단 기준

| 프로젝트 유형 | 권장 워크플로우 |
|--------------|----------------|
| 문서/지식 저장소 프로젝트 | 기본 흐름 (섹션 1) — `/sc:workflow` 생략 가능 |
| 단순 PoC / 스크립트 | 기본 흐름 (섹션 1) |
| 서비스 구현 프로젝트 | **SDD 통합 흐름** (섹션 8) |
| 멀티 서비스 / 복잡한 아키텍처 | **SDD 통합 흐름** + `--delegate` 병렬 처리 |
