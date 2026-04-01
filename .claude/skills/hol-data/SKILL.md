---
name: hol-data
description: "Generate realistic synthetic seed data for a HOL lab app. Reads the app's models and creates 20-50 records per entity with industry-authentic values, realistic relationships, and useful edge cases."
argument-hint: "<company-profile-or-domain-description>"
---

# HOL Synthetic Data Generator

You are generating realistic seed data for a HOL lab app. The data should feel authentic to the company's domain — not obviously fake placeholder data.

## Input

The user has provided a domain description via `$ARGUMENTS`. This may be a company profile, industry description, or file path.

## Step 1: Understand the Data Model

Read `models.py` and `schemas.py` to understand:
- All entity types and their fields
- Relationships between entities (foreign keys)
- Enum-like fields (status, tier, category) and their valid values
- Required vs optional fields
- Field types and constraints

## Step 2: Design the Dataset

Plan the data before generating it:

### Volume
- **Primary entities** (the main "thing" — itineraries, products, courses): 20-30 records
- **Secondary entities** (users, customers, guests): 10-15 records
- **Junction/transaction entities** (bookings, orders, enrollments): 30-50 records
- **Reference entities** (categories, tiers, locations): 5-10 records

### Realism Requirements
- **Names**: Use plausible names for the domain. For cruise lines: real port cities, realistic ship names, standard cabin categories. For manufacturing: real material names, standard unit codes. For finance: realistic ticker symbols, standard account types.
- **Dates**: Use dates within a reasonable window (next 6-12 months for future events, past 1-2 years for historical data). Use proper date formats.
- **Numbers**: Prices, quantities, and scores should be in realistic ranges for the industry. No $1 cruises or $999,999 widgets.
- **Relationships**: Every foreign key must reference a valid record. A booking references a real guest AND a real itinerary.
- **Status distribution**: Mix of statuses — not all "confirmed" or all "active". Include some cancelled, pending, completed.

### Edge Cases (Important for Bug Discovery)
Mix in a few records that exercise boundary conditions:
- A record with the minimum/maximum value for a numeric field
- A record with an empty optional field
- A record right at a tier boundary (e.g., a guest with exactly enough points for Gold)
- A record with dates at edge boundaries (first/last day of a range)
- A few records that look similar but differ in one key field

These edge cases help participants discover planted bugs more naturally.

## Step 3: Generate seed.py

Rewrite `seed.py` with the full dataset. The file should:

```python
"""Seed the database with synthetic data for the {App Name} HOL lab."""
from database import SessionLocal, init_db, engine, Base
from models import ModelA, ModelB, ModelC
from datetime import date, datetime

def seed():
    # Drop and recreate tables for clean state
    Base.metadata.drop_all(bind=engine)
    init_db()

    db = SessionLocal()
    try:
        # Create reference/lookup data first
        # ...

        # Create primary entities
        # ...

        # Create secondary entities
        # ...

        # Create transaction/junction records
        # ...

        db.commit()
        print(f"Seeded: X records of A, Y records of B, Z records of C")
    finally:
        db.close()

if __name__ == "__main__":
    seed()
```

### Data Format Rules
- Define data inline in the seed file (not external JSON/CSV)
- Use model constructors directly: `ModelA(field1="value", field2=123)`
- Add records in bulk with `db.add_all([...])`
- Print a summary of what was seeded
- The script must be idempotent (drop and recreate tables)

## Step 4: Verify

```bash
uv run python seed.py          # Should complete without errors
uv run pytest                  # All tests should still pass
uv run uvicorn main:app --reload  # Quick check that data is served
```

Test a few API endpoints to confirm data is visible and relationships work.

## Step 5: Report

Summarize the dataset:
- Record counts per entity
- Notable edge cases included
- Any fields you weren't sure about realistic values for
