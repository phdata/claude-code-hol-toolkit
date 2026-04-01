---
name: hol-bug
description: "Plant subtle multi-file bugs in a HOL lab app. Bugs span 2-3 files, are diagnosable from symptoms, have a clear 'aha' moment, and can be caught by a test."
argument-hint: "<number-of-bugs (default: 2)>"
---

# HOL Bug Maker

You are planting bugs in a HOL lab app for a debugging exercise. Participants will use Claude Code to find and fix these bugs (Act 2, ~20 min). The bugs must be subtle enough to require real diagnosis but fair enough to have a satisfying "aha" moment.

## Input

`$ARGUMENTS` specifies the number of bugs to plant (default: 2). Typically 1-2 for a half-day lab.

## Step 1: Map Data Flows

Read ALL source files and map every data flow path through the app:

```
Router (receives request, validates via schema)
  → Service (business logic, queries)
    → Model (ORM, database operations)
      → Schema (serializes response)
```

For each flow, note:
- Which files are involved
- Where business logic decisions happen (conditionals, calculations, filters)
- Where data transformations occur (mapping, aggregation, formatting)
- Where defaults or fallbacks are applied

## Step 2: Select Injection Points

Choose injection points that meet ALL criteria:

### Must Have
- [ ] **Spans 2-3 files** — the root cause is in one file, but the symptoms show in another
- [ ] **No syntax errors** — the app starts and mostly works
- [ ] **Visible symptoms** — at least one existing test fails, or API returns wrong data
- [ ] **Diagnosable** — a developer using Claude Code can trace from symptom to cause in ~15 min
- [ ] **Satisfying "aha"** — the root cause makes you go "oh, I see" not "that's random"
- [ ] **Testable** — a regression test can be written to catch it

### Must NOT
- [ ] Break app startup (import errors, missing tables)
- [ ] Cause data corruption or security issues
- [ ] Be findable by just reading one file
- [ ] Be a typo or syntax error
- [ ] Require domain expertise to understand

## Step 3: Bug Pattern Catalog

Choose from these patterns (adapt to the specific codebase):

### Pattern A: Wrong Tier/Category Logic
**Where:** Service layer applies wrong business logic based on a tier, category, or status field.
**Example:** Gold-tier guests get Silver-tier pricing because the service checks `tier == "silver"` instead of `tier == "gold"`, or uses `<=` instead of `<` on a threshold.
**Spans:** `services/*.py` (root cause) + `routers/*.py` (wrong response) + `models.py` (tier definition)
**Symptoms:** Tests comparing expected vs actual pricing/discounts fail. API returns wrong values for specific tier.

### Pattern B: Query Filter Error
**Where:** A database query uses the wrong comparison operator or misses a condition.
**Example:** Date range filter uses `<` instead of `<=`, excluding the last day. Or a filter applies to the wrong field.
**Spans:** `services/*.py` (root cause in query) + `routers/*.py` (missing results in response)
**Symptoms:** Search/filter endpoint returns fewer results than expected. Edge-case test fails.

### Pattern C: Schema Default Override
**Where:** A Pydantic schema has a wrong default value that silently overrides user input or database values.
**Example:** `discount_percent: float = 0.0` when it should be `Optional[float] = None`, causing all records to show 0% discount unless explicitly set.
**Spans:** `schemas.py` (root cause) + `services/*.py` (uses schema) + `routers/*.py` (serves wrong data)
**Symptoms:** API returns unexpected defaults. Tests checking specific field values fail.

### Pattern D: Wrong Aggregation/Sort
**Where:** A computation in the service layer uses the wrong field, wrong order, or wrong aggregation.
**Example:** Sorting by `created_at` instead of `departure_date`, or summing `base_price` instead of `total_price`.
**Spans:** `services/*.py` (root cause) + `routers/*.py` (wrong order/total in response)
**Symptoms:** List endpoints return data in unexpected order. Summary endpoints show wrong totals.

### Pattern E: Relationship Traversal Error
**Where:** Code follows the wrong relationship or misses a join condition.
**Example:** Fetching a guest's bookings but filtering by `guest_id` on the wrong table, or missing an eager load causing empty nested data.
**Spans:** `services/*.py` or `models.py` (root cause) + `routers/*.py` (incomplete response)
**Symptoms:** Nested data missing or showing wrong related records.

## Step 4: Implement Bugs

For each bug:

1. **Make the code change** — a small, targeted edit (typically 1-3 lines changed per file)
2. **Verify the app still starts** — `uv run uvicorn main:app --reload` should work
3. **Verify symptoms are visible** — run `uv run pytest` and confirm the right tests fail with useful (not cryptic) error messages
4. **Document the answer key**

## Step 5: Write Answer Keys

Create `.hol/bugs/answers.md`:

```markdown
# Bug Answer Keys — {App Name}

FACILITATOR ONLY — Do not share with participants.

---

## Bug 1: {Short Name}

**Symptom:** {What the participant sees — which test fails, what API returns wrong}

**Root Cause:** {1-2 sentence explanation}

**Affected Files:**
- `{file1}` line {N} — {what's wrong here}
- `{file2}` line {N} — {how it manifests here}

**The Fix:**
```diff
--- a/{file1}
+++ b/{file1}
@@ ...
- {buggy line}
+ {fixed line}
```

**Regression Test:**
```python
def test_{bug_name}_regression():
    # This test would have caught the bug
    ...
```

**Facilitator Hints** (if participant is stuck):
- After 10 min: "Look at which test is failing and what value it expected vs got"
- After 15 min: "The issue is in the {service/schema/query} layer"
```

Repeat for each bug.

## Step 6: Verify Final State

```bash
uv run pytest  # Some tests should fail — document which ones
uv run uvicorn main:app --reload  # App should still start
```

Report to the user:
- How many bugs planted
- Which tests fail (these are the symptoms participants will start from)
- Brief description of each bug (without spoiling the "aha")
