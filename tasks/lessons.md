# tasks/lessons.md

> Append-only log of lessons learned. Read at every session start (`docs/guides/ai-onboarding/guide.md` §2.4).
> Do not delete entries — supersede them with a newer entry that explains why the previous rule changed.

## Format

```
## YYYY-MM-DD — <short title>

**Context**: what was happening when the lesson was learned.
**Mistake / Insight**: what went wrong (or what surprised us).
**Rule going forward**: the actionable rule, in imperative voice.
```

When to append:

- The user corrects an approach ("no, don't do X", "stop doing Y").
- A non-obvious choice is validated ("yes, that was the right call").
- A pattern repeats across two or more incidents and deserves codification.

When NOT to append:

- A one-off fix already captured in a commit message.
- Code patterns derivable from reading current source — those belong in `.claude/rules/code-conventions.md`.

---

## 2026-05-07 — AI-assistant onboarding bootstrap completed

**Context**: First session on this fork (`feature/nabo-change`). User invoked the docs/guides/ system. Preflight (`docs/guides/ai-onboarding/guide.md` §2) failed — none of `CLAUDE.md`, `.claude/`, `tasks/`, `specs/`, `docs/pdca/` existed.

**Mistake / Insight**: The onboarding guide was created precisely to prevent silent no-op onboarding. By following §3 Step 1–5 deterministically (read all 7 guides → map artifacts → present Plan → wait for approval → bootstrap → self-verify), the bootstrap completed cleanly and Preflight will PASS in the next session.

**Rule going forward**: At every session start, run §2 Preflight first. If any required artifact is missing, halt feature work and return to §3 Full Onboarding. Reference guides:
- `docs/guides/README.md`
- `docs/guides/ai-onboarding/guide.md`
- `docs/guides/claude-md-guide/guide.md`
- `docs/guides/claude-rules/guide.md`
- `docs/guides/harness-patterns/guide.md`
- `docs/guides/harness-patterns/claude-code-session-management-guide.md`
- `docs/guides/superclaude-workflow/guide.md`
