# Code Conventions

> Auto-loads. Source of authority for stack-specific rules.
> Stack snapshot dated: 2026-05-07.

## Language & runtime

- **Python**: 3.10+. Repo CI relies on syntax/features available in 3.10.
- **Notebooks**: Jupyter `.ipynb`, runnable in both local Jupyter and Google Colab. New notebooks copy `notebook_template.ipynb` as the starting structure (license header, install cell, auth cell, restart kernel cell, imports, body, cleanup).

## Lint & format

| Tool | Config | Scope | Run |
|---|---|---|---|
| `ruff` | `.ruff.toml` (root) | All `.py` and `.ipynb` (ruff's notebook mode) | `ruff check .` / `ruff format --check .` |
| `prettier` | `.prettierrc` (root) | Markdown | `npx prettier --check "**/*.md"` |
| `lychee` | `lychee.toml` | Outbound links in docs and notebooks | `lychee --config lychee.toml .` |
| `.editorconfig` | repo root | All editors | (auto, IDE-driven) |

`.git-blame-ignore-revs` exists — large mass-format commits should be appended there so `git blame` skips them.

## Repository layout (existing top-level dirs)

| Dir | Content |
|---|---|
| `agents/` | Agent samples (ADK, custom orchestration) |
| `gemini/` | Gemini API/SDK starter notebooks, function calling, sample apps |
| `search/` | Vertex AI Search / Agent Search notebooks |
| `rag-grounding/` | RAG and grounding samples |
| `vision/` | Imagen / Veo samples |
| `audio/` | Audio/speech samples |
| `embeddings/`, `open-models/`, `partner-models/`, `sdk/`, `tools/`, `translation/`, `workshops/` | Topical sample collections |
| `setup-env/` | Environment bootstrap scripts |
| `scripts/` | Repo maintenance scripts |
| `docs/guides/` | AI-assistant guides (this contract) |

New additions follow the existing top-level taxonomy. Do not invent a new top-level directory without a Plan.

## Notebook conventions

- License header cell (Apache-2.0) at top — copy from `notebook_template.ipynb`.
- Author/contributor metadata block follows repo template.
- One `pip install` cell, pinned where reproducibility matters.
- One auth cell (Colab vs Workbench branches).
- Kernel restart cell after install.
- Imports cell separate from setup.
- Output cells cleared (or stable) before commit; CI link-check runs over rendered output.
- Use `display(Markdown(...))` over `print()` for narrative output.

## Python-module conventions

- Type hints on all public functions; `from __future__ import annotations` only where 3.9 compatibility is needed.
- Prefer `pathlib.Path` over `os.path`.
- No print-debugging in committed code; use `logging`.
- Tests under `tests/` co-located with the module they cover; `pytest` is the runner.

## Test rules

- Unit tests: mocks acceptable for external services.
- **Integration tests must not mock** the database or the Vertex/Gemini API surface. Use a real test project, `vcrpy` cassettes, or testcontainers. (See `.claude/rules/sdd-workflow.md` Forbiddens.)
- Coverage threshold is per-subproject; check the local README before claiming "tests pass".

## Docker

This repo does not host services centrally. Per-sample `Dockerfile`s live next to their sample app. Conventions:
- Single-stage `python:3.11-slim` base unless a sample requires CUDA.
- `requirements.txt` pinned.
- Non-root user in final image.

## Documentation

- Markdown formatted by Prettier; tables aligned by Prettier.
- All outbound URLs must pass `lychee` (configured ignores in `lychee.toml`).
- New top-level READMEs follow existing tone (concise, table-driven, link-rich).
