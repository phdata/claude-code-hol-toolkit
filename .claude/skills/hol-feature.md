---
name: hol-feature
description: "Propose 3-5 feature ideas for a HOL spec-driven build exercise. Features touch multiple layers, are buildable in ~30 min with Claude Code, and are visually demo-able."
argument-hint: "<optional-domain-context>"
---

# HOL Feature Ideator

You are proposing feature ideas for a HOL workshop. Participants will pick one feature, write a technical spec (Act 3, ~20 min), then build it with Claude Code (Act 4, ~30 min).

## Step 1: Understand the App

Read ALL source files in the project to understand:
- What the app does (domain, core flows)
- Current API endpoints and what they return
- The data model (models, relationships, fields)
- Existing business logic in services/
- What the seed data looks like
- What tests exist

## Step 2: Generate Proposals

Propose **3-5 features**. Each must meet ALL of these criteria:

### Suitability Criteria
- **Multi-layer**: Touches at least 2 layers (router + service, service + model, or all three)
- **Buildable**: ~25 min implementation time with Claude Code (not trivial, not huge)
- **Specifiable**: Can be described in a 1-2 sentence brief, specced in ~5 min
- **Demo-able**: Has a visible before/after (new endpoint returns different data, new logic changes behavior)
- **No new dependencies**: Uses only what's in requirements.txt
- **Standalone**: Doesn't depend on other proposed features

### Good Feature Patterns
- **Recommendation/matching**: "Show similar X based on Y" — needs a scoring function, new endpoint, touches service + router
- **Aggregation/analytics**: "Show summary stats for X" — needs a query, computation, new endpoint
- **Business rule**: "Apply discount/upgrade/priority based on conditions" — needs logic in service, affects existing flow
- **Search/filter enhancement**: "Filter X by multiple criteria with ranking" — needs query building, affects router + service
- **Notification/alert**: "Flag X when condition is met" — needs a check in service, new field or endpoint

### Bad Feature Patterns (Avoid)
- Pure CRUD (just another create/read/update/delete — too simple)
- UI-only changes (no backend logic to implement)
- External API integrations (requires new dependencies and API keys)
- Database migrations (too much infrastructure work for 30 min)
- Authentication/authorization (too generic, not domain-specific)

## Step 3: Write Proposals

For each feature, write:

```markdown
### {Feature Name}

**Brief:** {1-2 sentence description — this is what participants receive in Act 3}

**Why it's good for the lab:**
- {Why it touches multiple layers}
- {Why it's demo-able}
- {Why it matches the domain}

**Affected files:**
- `services/{file}.py` — {what changes}
- `routers/{file}.py` — {what changes}
- `models.py` — {what changes, if any}
- `tests/test_{file}.py` — {new tests needed}

**Spec outline:**
1. {Requirement or design decision}
2. {Requirement or design decision}
3. ...
5. {Edge case to consider}

**Stretch goals** (for fast finishers):
- {Enhancement 1}
- {Enhancement 2}
```

## Step 4: Save Output

Write all proposals to `.hol/features/proposals.md` with a header:

```markdown
# Feature Proposals for {App Name}

Pick ONE feature for the spec-driven build exercise (Acts 3-4).
Each feature is designed to be specced in ~5 min and built in ~25 min with Claude Code.

---

{proposals here}
```

## Step 5: Recommend

At the end of the file, add a "Facilitator Recommendation" section suggesting which feature is the best default choice and why (typically the one with the clearest before/after demo and the most satisfying implementation path).
