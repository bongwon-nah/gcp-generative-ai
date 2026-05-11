# docs/pdca/

> Plan-Do-Check-Act cycle artifacts. Source of authority: `docs/guides/superclaude-workflow/guide.md` §7, `docs/guides/harness-patterns/claude-code-session-management-guide.md` §3.3.

## Layout

```
docs/pdca/
└── <feature-slug>/
    ├── plan.md     # hypothesis, design, expected outcome
    ├── do.md       # execution log, errors hit, resolutions tried
    ├── check.md    # actual vs expected; what surprised us
    └── act.md      # patterns confirmed → docs/patterns/<name>.md
                    # mistakes captured → docs/mistakes/<name>-<date>.md
                    # global learnings → CLAUDE.md if cross-feature
```

## When to create a feature directory

- Net-new feature work where the path is uncertain.
- Bug investigations where the root cause is unclear at start.
- Architecture experiments where outcome will inform future decisions.

Routine fixes (typo, dependency bump, generated-file refresh) do not need a PDCA cycle.

## Relationship to specs/ and tasks/

- `specs/` — the **what** (acceptance criteria).
- `tasks/plan_*.md` — the **how** (implementation steps, signed off by user).
- `docs/pdca/<feature>/` — the **learning loop** running alongside; survives after the feature ships.

The three are complementary, not duplicative. Do not collapse them.

## Patterns and mistakes (sibling directories)

These are created on first need; do not stub them empty:

- `docs/patterns/` — distilled, reusable success patterns.
- `docs/mistakes/` — failure analyses with prevention checklists.

When `act.md` produces something worth promoting, write it there and link from `act.md`.
