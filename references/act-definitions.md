# HOL Workshop Structure — 5 Acts

Total duration: ~3.5 hours (including breaks and transitions)

## Act 1 — Explore (10 min)

**Goal:** Participants orient themselves in an unfamiliar codebase using Claude Code as their guide.

**Setup:** Drop into the repo cold. No README walkthrough — Claude Code IS the onboarding.

**What participants do:**
- Ask Claude Code to map the codebase and explain the architecture
- Trace a key data flow (e.g., "how does a booking get created?")
- Identify the main entities, routes, and services
- Run the test suite and note what passes/fails

**Success criteria:** Participant can describe the app's purpose, stack, and main data flows without reading documentation.

**Facilitator notes:** This act builds confidence with Claude Code. Some participants will try to read every file manually — nudge them to ask Claude Code instead.

---

## Act 2 — Fix (20 min)

**Goal:** Find and fix a planted multi-file bug using Claude Code for diagnosis.

**Setup:** Tests are failing. The bug spans 2-3 files and involves a logic error (not a syntax error).

**What participants do:**
- Start from the failing test output
- Ask Claude Code to trace the failure through the codebase
- Identify the root cause across multiple files
- Fix the bug
- Write a regression test that would have caught it

**Success criteria:** All tests pass. The participant can explain WHY the bug existed and what the test guards against.

**Bug quality checklist:**
- [ ] Spans 2-3 files (not just one line)
- [ ] App still starts and mostly works (not a crash)
- [ ] Symptoms are visible in test output
- [ ] Root cause has an "aha" moment
- [ ] A test can catch it

**Facilitator notes:** If someone is stuck after 10 min, hint at which test is failing. After 15 min, hint at which layer the bug is in (service? query? schema?).

---

## Act 3 — Spec (20 min)

**Goal:** Use Claude Code + a spec template to write a technical specification before coding.

**Setup:** Participants receive a feature brief (1-2 sentences). A spec template is at `.hol/templates/feature-spec.md`.

**What participants do:**
- Read the feature brief
- Use Claude Code to understand what exists and what needs to change
- Fill out the spec template: requirements, design decisions, implementation plan, edge cases
- Review the spec with Claude Code (ask it to poke holes)

**Success criteria:** A complete spec document that another engineer (or Claude Code) could implement from.

**Spec template sections:**
1. Problem statement
2. Requirements (must-have vs nice-to-have)
3. Design decisions (with rationale)
4. Implementation plan (ordered steps with file paths)
5. Edge cases and error handling
6. Test plan

**Facilitator notes:** Emphasize "think before you code." The spec doesn't need to be perfect — it needs to be specific enough to guide implementation.

---

## Act 4 — Build (30 min)

**Goal:** Execute the spec using Claude Code as a coding partner.

**Setup:** Participants have their spec from Act 3. Claude Code does the implementation.

**What participants do:**
- Hand the spec to Claude Code and let it propose an implementation
- Govern the agent: approve, redirect, refine
- Run tests as they go
- Handle edge cases that come up during implementation

**Success criteria:** Feature works, tests pass, app runs correctly with the new feature.

**Facilitator notes:** Everyone's implementation will differ — that's the point. Some will override Claude Code more, some less. Both approaches teach something. Remind participants to commit frequently.

---

## Act 5 — Show & Tell (15 min)

**Goal:** Demonstrate what was built and share what was learned.

**Setup:** 3-4 participants volunteer to demo (2-3 min each).

**What participants share:**
- What they built (quick demo)
- What surprised them about Claude Code
- Where they overrode or redirected Claude Code
- What they'd do differently next time

**Facilitator notes:** Celebrate diversity of approaches. Ask follow-up questions about the human-AI collaboration dynamic, not just the code.

---

## Timing Summary

| Act | Duration | Focus |
|-----|----------|-------|
| Setup & introductions | 15 min | Get everyone running |
| Act 1 — Explore | 10 min | Codebase orientation |
| Act 2 — Fix | 20 min | Bug diagnosis |
| Break | 10 min | |
| Act 3 — Spec | 20 min | Spec-driven design |
| Act 4 — Build | 30 min | Feature implementation |
| Break | 10 min | |
| Act 5 — Show & Tell | 15 min | Demos and discussion |
| Wrap-up | 10 min | Key takeaways |
| **Total** | **~2.5 hrs** | |

## Feature Brief Format

Keep it short — 1-2 sentences. Example:

> "When a guest views an itinerary, show 3-5 similar trips based on their booking history, loyalty tier, and preferences."

The brief should be:
- Clear enough to spec from
- Vague enough to require design decisions
- Scoped to ~30 min of implementation with Claude Code
