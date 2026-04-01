---
name: hol-build-lab
description: "Build a complete HOL lab repo from a brief. Reads a PDF/doc/text brief describing the company and app concept, asks clarifying questions, then generates a fully working Python/FastAPI/SQLite app with planted bugs, seed data, and feature exercises."
argument-hint: "<path-to-brief-or-inline-description>"
---

# HOL Lab Builder

You are building a hands-on lab (HOL) for a Claude Code workshop. The lab is a small Python/FastAPI/SQLite app that participants will explore, debug, spec, and build features in.

## Input

The user has provided a HOL brief via `$ARGUMENTS`. This may be:
- A file path to a PDF or markdown document
- Inline text describing the company, app concept, and workshop goals

## Step 1: Parse the Brief

Read the brief and extract:
- **Company name** and industry
- **App name** and one-sentence description
- **Domain entities** (e.g., guests, itineraries, bookings, cabins)
- **Core flows** that should work (e.g., search, profile, booking)
- **Bugs** described in the brief (if any — otherwise you'll design them)
- **Feature exercise** described in the brief (if any)
- **Brand tone** and any specific terminology

## Step 2: Confirm Understanding

Before generating any code, present your understanding back to the user and ask:

1. Does the app name and concept look right?
2. Are these the right domain entities and relationships?
3. Any specific bugs or features you want, or should I design them?
4. Any brand-specific requirements (colors, terminology, tone)?
5. Should the app have a minimal HTML frontend, or API-only?

Wait for the user to confirm before proceeding.

## Step 3: Generate the Base App

Follow the architecture in `references/app-scaffold.md`. Generate a **fully working** Python/FastAPI/SQLite app with these files:

### Required Files
- `main.py` — FastAPI app with CORS, lifespan startup, router includes
- `database.py` — SQLite engine, SessionLocal, Base, get_db(), init_db()
- `models.py` — 3-5 SQLAlchemy models with relationships matching the domain entities
- `schemas.py` — Pydantic v2 request/response schemas with model_config
- `routers/__init__.py` + 2-3 route files — CRUD + domain-specific query endpoints
- `services/__init__.py` + 1-2 service files — Business logic (pricing, matching, filtering)
- `seed.py` — Populates the database with 10-20 records per entity (placeholder data for now)
- `tests/conftest.py` — Test fixtures (test database, test client)
- `tests/test_*.py` — 5-10 tests covering main flows
- `pyproject.toml` — project metadata, dependencies (fastapi, uvicorn, sqlalchemy, pydantic, pytest, httpx), requires-python >= 3.11
- `README.md` — Minimal (app name, one sentence, how to run)

### Architecture Rules
- Routers call services, services query the database. No business logic in routers.
- At least one enum-like field (status, tier, category) on a model.
- At least one foreign key relationship between models.
- Use domain-specific names everywhere — no generic "item", "record", "entity".
- The app must start with `uv run uvicorn main:app --reload` and all tests must pass with `uv run pytest`.

### Verify the base app works:
```bash
uv sync
uv run python seed.py
uv run pytest
uv run uvicorn main:app --reload  # quick smoke test, then stop
```

**Commit** the working base app: `git add -A && git commit -m "Add base {app-name} app"`

## Step 4: Run Sub-Skills

Run these skills sequentially, committing after each:

### 4a. Branding Pass
Run `/hol-brand` with the company profile from the brief. This ensures all naming, copy, and data feel authentic to the company. Commit the result.

### 4b. Synthetic Data
Run `/hol-data` with the domain description. This replaces placeholder seed data with realistic entries. Commit the result.

### 4c. Feature Ideas
Run `/hol-feature` to generate 3-5 feature proposals. This produces two files: `.hol/features/briefs.md` (participant-facing, brief descriptions only) and `.hol/features/proposals.md` (facilitator-only, full details). The user (or the brief) picks one to leave as the exercise. Commit the result.

### 4d. Plant Bugs
Run `/hol-bug` to plant 1-2 multi-file bugs. Answer keys go to `.hol/bugs/answers.md`. Commit the result.

## Step 5: Create Lab Infrastructure

### CLAUDE.md (Participant Starter)
Create a `CLAUDE.md` that tells participants:
- App name and one-sentence description
- How to install: `uv sync`
- How to seed: `uv run python seed.py`
- How to run: `uv run uvicorn main:app --reload`
- How to test: `uv run pytest`
- Stack: Python, FastAPI, SQLite
- Do NOT include bug hints, feature ideas, or architecture details

### Spec Template
Create `.hol/templates/feature-spec.md`:
```markdown
# Feature Spec: {Feature Name}

## Problem Statement
What problem does this feature solve?

## Requirements
### Must Have
- ...

### Nice to Have
- ...

## Design Decisions
| Decision | Choice | Rationale |
|----------|--------|-----------|
| ... | ... | ... |

## Implementation Plan
1. ...
2. ...
3. ...

## Edge Cases
- ...

## Test Plan
- [ ] ...
```

### Facilitator Guide
Create `.hol/facilitator/README.md` combining:
- Workshop timing (from `references/act-definitions.md`)
- Bug answer keys (from `.hol/bugs/answers.md`)
- Feature proposals (from `.hol/features/proposals.md`) — facilitator-only detail
- Reference to `.hol/features/briefs.md` as the handout for Act 3
- Tips for helping stuck participants

### Validator Skill
Copy `.claude/skills/hol-run-lab.md` from the toolkit into the generated lab repo so `/hol-run-lab` can be run directly from the lab directory.

### .gitignore addition
Add `.hol/bugs/` and `.hol/facilitator/` to `.gitignore` so participants don't see answer keys.

Commit all infrastructure files.

## Step 6: Final Verification

1. Fresh install: `uv sync`
2. Seed the database: `uv run python seed.py`
3. Run tests: `uv run pytest` — document which tests fail (these are the bug symptoms)
4. Start the app: `uv run uvicorn main:app --reload` — verify it starts and serves data
5. Report the final state to the user:
   - Total file count
   - Which tests fail and why (bug symptoms)
   - Feature proposals summary
   - Any issues found

Tag the repo: `git tag v1.0-lab`
