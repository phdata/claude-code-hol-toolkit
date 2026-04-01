---
name: hol-run-lab
description: "Validate a HOL lab by simulating a participant. Walks through all 5 Acts (Explore, Fix, Spec, Build, Show & Tell) and produces a validation report with pass/fail, timing, and facilitator recommendations."
---

# HOL Lab Validator

You are simulating a workshop participant to validate that a HOL lab works end-to-end. Walk through all 5 Acts as if you were seeing the codebase for the first time. Be honest about what's confusing, what's too easy, and what doesn't work.

## Important Rules

1. **Do NOT read `.hol/bugs/answers.md` until AFTER you've attempted Act 2.** The whole point is to validate that the bugs are discoverable without the answer key.
2. **Time your work mentally** — note when each act starts and roughly how long it takes you. Human participants will take 2-3x longer.
3. **Be critical** — if something is confusing, broken, or too easy, say so. A failed validation now saves 20 frustrated participants later.

## Act 1 — Explore (Target: 10 min for humans)

Simulate a cold start. You've never seen this repo before.

1. Read `CLAUDE.md` — is it clear enough to get started?
2. Install and run:
   ```bash
   uv sync
   uv run python seed.py
   uv run pytest
   uv run uvicorn main:app --reload
   ```
3. Explore the codebase — map the architecture, understand the domain
4. Trace one main data flow (e.g., "how does a booking get created?")
5. Hit a few API endpoints to see what data exists

**Evaluate:**
- [ ] Can a participant understand the app's purpose in under 5 min?
- [ ] Does the app start without errors?
- [ ] Is the seed data visible via API?
- [ ] Are the file names and structure intuitive?
- [ ] Is the CLAUDE.md helpful without being a spoiler?

## Act 2 — Fix (Target: 20 min for humans)

Find and fix the planted bugs WITHOUT reading the answer keys.

1. Run `pytest` and examine the failures
2. For each failing test:
   - Read the test to understand what it expects
   - Trace the failure through the codebase (router → service → model → schema)
   - Identify the root cause
   - Apply the fix
   - Write a regression test
3. Run `pytest` — all tests should pass after fixes

**Evaluate:**
- [ ] Are test failure messages clear enough to start debugging?
- [ ] Is the diagnosis path logical (symptoms lead toward the cause)?
- [ ] Is the "aha" moment satisfying?
- [ ] Is the bug subtle enough to require real debugging (not obvious from reading one file)?
- [ ] Is it fair (no domain expertise required, just code tracing)?
- [ ] Can a meaningful regression test be written?

**After fixing:** Now read `.hol/bugs/answers.md` and compare:
- Did you find the same root cause?
- Was your fix the same or equivalent?
- How long did it take vs the 20-min target?

## Act 3 — Spec (Target: 20 min for humans)

1. Read `.hol/features/proposals.md`
2. Pick the recommended feature (or any feature)
3. Read the feature brief (the 1-2 sentence description)
4. Fill out `.hol/templates/feature-spec.md` for the chosen feature
5. Save the spec to `.hol/validation/feature-spec-filled.md`

**Evaluate:**
- [ ] Is the feature brief clear enough to spec from?
- [ ] Does the spec template guide without over-constraining?
- [ ] Can you identify the affected files and design decisions?
- [ ] Are there real edge cases to consider?

## Act 4 — Build (Target: 30 min for humans)

Implement the feature from your spec.

1. Create the implementation: new/modified service functions, router endpoints, models if needed
2. Write tests for the new feature
3. Run the full test suite — everything should pass
4. Verify the feature works via API

**Evaluate:**
- [ ] Is the feature buildable in ~30 min (estimated for a human with Claude Code)?
- [ ] Does it touch multiple layers as promised?
- [ ] Is the result demo-able (clear before/after)?
- [ ] Did any unexpected complications arise?

## Act 5 — Report

Generate a validation report at `.hol/validation/report.md`:

```markdown
# Lab Validation Report — {App Name}

Date: {today}

## Summary
- Overall: PASS / FAIL / PASS WITH NOTES
- Lab is ready for: {audience} participants

## Act-by-Act Results

### Act 1 — Explore
- Status: PASS / FAIL
- Notes: {observations}
- Estimated human time: {X} min (target: 10 min)

### Act 2 — Fix
- Status: PASS / FAIL
- Bugs found: {N} / {N total}
- Diagnosis quality: {GOOD / FAIR / POOR — explain}
- Notes: {observations}
- Estimated human time: {X} min (target: 20 min)

### Act 3 — Spec
- Status: PASS / FAIL
- Notes: {observations}
- Estimated human time: {X} min (target: 20 min)

### Act 4 — Build
- Status: PASS / FAIL
- Feature built: {name}
- Complications: {any}
- Estimated human time: {X} min (target: 30 min)

## Issues Found
1. {issue — severity — recommendation}
2. ...

## Facilitator Recommendations
- {recommendation 1}
- {recommendation 2}
- ...

## Verdict
{Final assessment: is this lab ready for a live workshop?}
```

Undo all your changes after generating the report (the lab should be left in its "bugged" state for participants):
```bash
git checkout -- .
```

Keep only the validation report:
```bash
git checkout HEAD -- .hol/validation/
```
