# specs/

> Feature SPECs — written by the `spec-writer` agent before any implementation begins. Source of authority: `.claude/agents/spec-writer.md`, `.claude/rules/sdd-workflow.md`.

## Files

| File | Purpose |
|---|---|
| `<feature-slug>_spec_<YYYY-MM-DD>.md` | One file per feature. Status field tracks lifecycle: `draft` → `approved` → (later) `superseded`. |

## Lifecycle

1. `spec-writer` creates the file with `Status: draft`.
2. User reviews; SPEC moves to `Status: approved` only with explicit user approval.
3. Implementation cannot start until status is `approved`.
4. If a SPEC is replaced by a newer one, update its status to `superseded` with a link to the replacement; do not delete.

## Section template

`spec-writer` always produces these six sections:

1. **Goal** — single sentence, user-visible outcome.
2. **Non-goals** — explicit out-of-scope items.
3. **Scope** — files, modules, dependencies touched.
4. **Interfaces** — public APIs, CLI flags, notebook cells with input/output types.
5. **Acceptance criteria** — testable bullets, each one independently verifiable by `test-validator`.
6. **Open questions** — items requiring user decision before Plan stage.

A SPEC with unanswered §6 questions cannot leave `draft`.
