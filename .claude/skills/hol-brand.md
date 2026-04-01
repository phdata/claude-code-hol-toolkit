---
name: hol-brand
description: "Rebrand a lab app to match a company profile. Updates app name, domain language, API paths, seed data names, and UI copy to feel authentic to the target company and industry."
argument-hint: "<company-profile-text-or-file-path>"
---

# HOL Branding Pass

You are applying company branding to a HOL lab app. The goal is to make the app feel like it was built for (or by) the target company — not a generic demo.

## Input

The user has provided a company profile via `$ARGUMENTS`. This may be a file path or inline text. Extract:
- **Company name**
- **Industry** (cruise, manufacturing, finance, healthcare, etc.)
- **App name** (if specified)
- **Domain terminology** (what they call their customers, products, transactions)
- **Brand tone** (formal/casual, any specific language preferences)

## Step 1: Audit Current State

Read all source files in the project. Build a checklist of everything that needs branding:

- [ ] App title in `main.py` (FastAPI title parameter)
- [ ] README.md heading and description
- [ ] CLAUDE.md (if it exists)
- [ ] Model class names in `models.py` (e.g., `Guest`, `Booking`, `Itinerary`)
- [ ] Schema class names in `schemas.py`
- [ ] Router file names and URL prefixes in `routers/`
- [ ] Service file names and function names in `services/`
- [ ] Variable names throughout (especially in business logic)
- [ ] API endpoint paths (`/guests`, `/bookings`, etc.)
- [ ] Test descriptions and test data in `tests/`
- [ ] Seed data entity names in `seed.py` (ship names, city names, person names, etc.)
- [ ] Comments and docstrings
- [ ] Any frontend templates or static files

## Step 2: Build Domain Mapping

Before making any changes, create a complete mapping table:

| Current Term | Branded Term | Where Used |
|-------------|-------------|------------|
| (generic name) | (domain-specific name) | (files) |

Rules:
- Use the company's actual terminology where possible
- Names should feel natural to someone in that industry
- API paths should use kebab-case: `/loyalty-tiers`, `/trip-recommendations`
- Python identifiers should use snake_case: `loyalty_tier`, `trip_recommendation`
- Class names should use PascalCase: `LoyaltyTier`, `TripRecommendation`
- Seed data should use realistic values (real city names, plausible dates, industry-standard terms)

## Step 3: Apply Branding

Work through the mapping systematically, file by file, in dependency order:
1. `models.py` (base types everything depends on)
2. `schemas.py` (depends on model names)
3. `database.py` (if any model references)
4. `services/` (depends on models and schemas)
5. `routers/` (depends on services and schemas)
6. `seed.py` (depends on models)
7. `tests/` (depends on everything)
8. `main.py` (router imports and app title)
9. `README.md`, `CLAUDE.md`
10. Any templates or static files

For each file:
- Rename classes, functions, and variables per the mapping
- Update string literals (user-facing text, error messages)
- Update comments and docstrings
- Update import statements
- Rename the file itself if the filename should change (e.g., `routers/items.py` → `routers/itineraries.py`)

## Step 4: Verify

Run the full test suite and app startup:
```bash
pytest
uvicorn main:app --reload  # smoke test
```

If anything breaks, fix it. The app must be fully functional after branding.

## Step 5: Report

List all changes made, organized by file. Flag any terms you weren't sure about so the user can review.
