# CLAUDE.md

> Operating contract for AI assistants working in this repository.
> See `docs/guides/` for the full guide system. This file is the entry contract; the guides are the law.

## Project Purpose

This repository is a fork of Google Cloud's `generative-ai` samples collection — notebooks, code samples, sample apps, and resources that demonstrate Vertex AI / Gemini API workflows. Local work happens on `feature/nabo-change`. Changes here are **not** intended for upstream PRs unless explicitly stated.

## Current Stage

Learning & experimentation on `feature/nabo-change`. AI-assistant onboarding bootstrap completed 2026-05-07. No active feature implementation in progress.

## Key References

- `@docs/guides/README.md` — guide index
- `@docs/guides/ai-onboarding/guide.md` — Preflight + full-onboarding procedure (read every session start)
- `@docs/guides/claude-md-guide/guide.md` — rules for editing this file
- `@docs/guides/claude-rules/guide.md` — `.claude/` directory contract
- `@docs/guides/harness-patterns/guide.md` — session/lessons/Current-Work patterns
- `@docs/guides/superclaude-workflow/guide.md` — `/sc:` pipeline + SDD integration

## Target Tech Stack

- **Language**: Python 3.10+ (notebooks and `.py` modules)
- **Notebooks**: Jupyter / Colab — template at `notebook_template.ipynb`
- **AI SDKs**: Google Gen AI SDK (`google-genai`), Vertex AI SDK, ADK (Agent Development Kit) where used in `agents/`
- **Lint/format**: `ruff` (config: `.ruff.toml`), `prettier` for Markdown (config: `.prettierrc`)
- **Repo conventions**: `.editorconfig`, `.git-blame-ignore-revs` for mass formatting commits
- Detailed conventions: `@.claude/rules/code-conventions.md`

## Workflow Rules

1. **Every session starts with Preflight** — see `docs/guides/ai-onboarding/guide.md` §2. The first tool call of every session checks artifacts; reads `tasks/lessons.md`, this file's "Current Work", and `.claude/rules/sdd-workflow.md`.
2. **SDD is the default** — see `.claude/rules/sdd-workflow.md`. SPEC before code; Plan-gate for ≥3-step or architectural work; tests before "done".
3. **No file in `.claude/`, `specs/`, `tasks/`, or `docs/pdca/` is generated without an approved Plan.** Architecture decisions are user-gated.
4. **2-strike rule**: same approach failing twice → `/clear` and re-plan; do not loop.
5. **Forbiddens** (each with alternative — see `.claude/rules/sdd-workflow.md`): spec-less implementation, time estimates, `--no-verify` on commits, force-push to shared branches, mocked DB in integration tests.

## Sub-Agent Roster

Defined under `.claude/agents/`:

| Agent | Role | Model | Tools |
|---|---|---|---|
| `spec-writer` | Authors SPEC.md before implementation | opus | Read, Grep, Glob, Bash, Write |
| `service-builder` | Implements features (TDD) | opus | Read, Grep, Glob, Bash, Write, Edit |
| `test-validator` | Runs tests, verifies SPEC compliance (read-only) | sonnet | Read, Grep, Glob, Bash |
| `notebook-runner` | Executes/verifies `.ipynb` cells, captures outputs | sonnet | Read, Grep, Glob, Bash |

Same file should not be edited by multiple agents in parallel.

## Slash Commands

Defined under `.claude/commands/`:

- `/sdd <target>` — SDD workflow entry point: Pre-flight → SPEC → SPEC review → Plan → Plan review → Implement → Validate → Commit → Postmortem.

`/sc:` namespace commands (SuperClaude) are documented in `docs/guides/superclaude-workflow/guide.md`.

## Skills

`.claude/skills/` is currently empty. Add skills as repeatable workflows emerge (e.g., notebook-publish, sample-scaffold). User-invocable `/sc:*` skills are loaded by the SuperClaude environment.

## Key Commands

```bash
# Lint Python (matches CI)
ruff check .
ruff format --check .

# Auto-fix
ruff check --fix .
ruff format .

# Lint Markdown
npx prettier --check "**/*.md"

# Run a notebook headlessly (preferred over manual cell execution)
jupyter nbconvert --to notebook --execute <path>.ipynb --output <out>.ipynb

# Lychee link check (matches `lychee.toml` policy used in CI)
lychee --config lychee.toml .
```

## Current Work

_None._ Append entries here when starting multi-session work; remove on completion. Format: `YYYY-MM-DD — <feature> — <state> — resume hint`.

## Known Unknowns

- Whether contributions from `feature/nabo-change` will ever be proposed upstream — **assumption: no, until told otherwise**.
- Test command for sample apps under `agents/`, `gemini/`, `search/` — varies per subdirectory; resolve at task time by reading the sample's local README.

## Changelog

- **2026-05-07** — AI-assistant onboarding bootstrap completed. Created `.claude/`, `CLAUDE.md`, `tasks/lessons.md`, `specs/`, `docs/pdca/` per `docs/guides/ai-onboarding/guide.md` §3.
