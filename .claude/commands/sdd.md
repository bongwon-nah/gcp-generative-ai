---
name: sdd
description: SDD (Spec-Driven Development) workflow entry point. Walks through Pre-flight → SPEC → SPEC review → Plan → Plan review → Implement → Validate → Commit → Postmortem for a single feature. Use when starting any non-trivial change.
---

# /sdd <target>

> Single-feature SDD trip. Source of authority: `.claude/rules/sdd-workflow.md`, `docs/guides/superclaude-workflow/guide.md` §8.

## Argument

`<target>` — short slug for the feature (e.g., `gemini-vision-caption`, `search-rerank-eval`). Used in artifact filenames.

## Stages and outputs

| # | Stage | Action | Output | Gate |
|---|---|---|---|---|
| 0 | Pre-flight | `docs/guides/ai-onboarding/guide.md` §2 Preflight | (in-context check) | Halt if FAIL |
| 1 | SPEC | Invoke `spec-writer` agent | `specs/<target>_spec_<date>.md` | — |
| 2 | SPEC review | Present SPEC to user | Status: draft → approved | **User approval required** |
| 3 | Plan | Main session drafts implementation plan | `tasks/plan_<target>_<date>.md` | — |
| 4 | Plan review | Present Plan to user | Plan signed off | **User approval required** |
| 5 | Implement | Invoke `service-builder` agent (TDD) | source + tests | — |
| 6 | Validate | Invoke `test-validator` agent (and `notebook-runner` if `.ipynb` touched) | report PASS/CONDITIONAL/FAIL | Halt unless PASS |
| 7 | Commit | Main session commits — see `CLAUDE.md` Key Commands and repo style | git commit | Hooks must pass; `--no-verify` forbidden |
| 8 | Postmortem | Append to `tasks/lessons.md` if anything non-obvious surfaced | lessons entry | — |

## Stop conditions (do not proceed past these without user input)

- Pre-flight FAIL → enter onboarding, do not start SDD.
- SPEC has unanswered §6 Open questions → return to user.
- Plan touches scope outside SPEC §3 → return to user.
- Validate returns FAIL → return to `service-builder` (or user if 2-strike rule trips).
- Commit hook fails → fix, do **not** `--no-verify`.

## Skip-conditions (when SDD is overkill)

`/sdd` is the default for non-trivial work. Skip it only for:
- Single-file typo / formatting fix.
- Read-only investigation.
- Doc-only tweaks under `docs/guides/` (which themselves require user approval — see `.claude/rules/sdd-workflow.md` Forbiddens).

If skipping, state in chat that SDD was skipped and why.
