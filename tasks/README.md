# tasks/

> Working state for SDD trips. Source of authority: `.claude/commands/sdd.md`, `.claude/rules/sdd-workflow.md`.

## Files

| File | Purpose |
|---|---|
| `lessons.md` | Append-only log read at session start. Format and rules inside. |
| `plan_<feature>_<YYYY-MM-DD>.md` | One file per SDD trip. Contains the Plan that the user signed off in stage 4 of `/sdd`. |

## Conventions

- One Plan file per feature; do not edit a Plan after it has been signed off — append a new dated Plan if direction changes.
- A Plan file references its SPEC: `specs/<feature>_spec_<date>.md`.
- When a feature is complete, leave its Plan file in place. Postmortem learnings belong in `lessons.md`, not in the Plan.
- This directory is **not** a general TODO list. The harness's task tools cover ephemeral in-session tracking.
