# SDD Workflow — Mandatory Order

> Source of authority: `docs/guides/superclaude-workflow/guide.md` §8, `docs/guides/ai-onboarding/guide.md` §6.
> This rule auto-loads in every session.

## Required order

```
SPEC  →  Plan  →  Implement  →  Test  →  Commit
```

No step is skippable.

| Stage | Output | Owner |
|---|---|---|
| SPEC | `specs/<feature>_spec_<date>.md` | `spec-writer` agent |
| Plan | `tasks/plan_<feature>_<date>.md` (user-approved) | main session |
| Implement | source code, tests | `service-builder` agent |
| Test | test report, coverage | `test-validator` agent |
| Commit | git commit, message follows repo style | main session |

## Plan gate

Before Implement, a Plan is **required** if any of the following hold:

- The change touches ≥3 files, or
- It introduces a new module, dependency, public API, or directory, or
- It modifies `.claude/`, `CLAUDE.md`, `docs/guides/`, or any file under `tasks/`, `specs/`, `docs/pdca/`.

A Plan is delivered to the user and must receive an explicit "go / 진행 / 승인" before any file is written.

## 2-strike rule

If the same approach fails twice (test red, build red, the user rejects the same fix), stop. Do not iterate a third time. Run `/clear` (or its harness equivalent), re-plan from SPEC, and propose a different approach to the user.

## Forbiddens (each with alternative)

| Forbidden | Why | Alternative |
|---|---|---|
| Implementation without a written SPEC | Hidden assumptions ship as bugs | Run `spec-writer`; produce `specs/<feature>_spec_*.md`; get user-confirm before coding |
| Time estimates ("this will take 2 hours") | Unreliable; over-commits the user | Express in scope/risk: "small / medium / load-bearing"; or list concrete steps |
| `git commit --no-verify` (or `--no-gpg-sign` to bypass signing) | Skips hooks that gate quality and security | Investigate and fix the hook failure; ask the user only if the hook itself is broken |
| Force-push to shared branches (`main`, default branches, anything with collaborators) | Destroys peers' work | Push a new branch and open a PR; rebase locally, then `--force-with-lease` only on personal branches with explicit user approval |
| Mocked DB / external service in **integration** tests | Mocks pass while prod migrations break | Use a real test instance (docker-compose, testcontainers, ephemeral Vertex project, recorded fixtures with `vcrpy`) for integration tests; mocks are fine in unit tests |
| Skipping `tasks/lessons.md` review at session start | Repeats past mistakes | Read it during Preflight (`docs/guides/ai-onboarding/guide.md` §2.4) before any other work |
| Generating files under `.claude/`, `CLAUDE.md`, `docs/guides/` without approval | These are architectural | Submit a Plan, wait for explicit approval, then write |

## SDD loop reminder

Inside a feature, SPEC → Plan → Implement → Test → Commit is one trip. Do not start the next feature until the current trip closes. If the user redirects mid-loop, halt and re-enter at SPEC for the new direction; do not silently widen scope.
