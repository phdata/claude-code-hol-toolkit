# Reference App Scaffold — Python / FastAPI / SQLite

This document defines the standard architecture for HOL lab apps. When generating a new lab, follow this structure.

## Target Size

~10-15 source files. Small enough to explore in 10 minutes with Claude Code, large enough to have real multi-file data flows for debugging exercises.

## Directory Structure

```
{app-name}/
├── main.py                  # FastAPI app, CORS, lifespan/startup
├── database.py              # SQLite engine, SessionLocal, Base, init_db()
├── models.py                # SQLAlchemy ORM models (3-5 tables)
├── schemas.py               # Pydantic request/response schemas
├── routers/
│   ├── __init__.py
│   ├── {domain_a}.py        # e.g., itineraries.py, inventory.py
│   ├── {domain_b}.py        # e.g., bookings.py, orders.py
│   └── {domain_c}.py        # e.g., guests.py, customers.py
├── services/
│   ├── __init__.py
│   ├── {domain_a}.py        # Business logic for domain A
│   └── {domain_b}.py        # Business logic for domain B
├── seed.py                  # Populate SQLite with synthetic data
├── tests/
│   ├── __init__.py
│   ├── conftest.py          # Pytest fixtures (test client, test db)
│   ├── test_{domain_a}.py
│   └── test_{domain_b}.py
├── requirements.txt
├── CLAUDE.md                # Participant starter (NO bug hints)
├── .hol/
│   ├── templates/
│   │   └── feature-spec.md  # Spec template for Act 3
│   ├── features/
│   │   └── proposals.md     # Feature ideas from hol-feature
│   ├── bugs/
│   │   └── answers.md       # Bug answer keys (facilitator only)
│   ├── facilitator/
│   │   └── README.md        # Facilitator guide
│   └── validation/
│       └── report.md        # From hol-run-lab
└── README.md                # Minimal — participants use Claude Code to explore
```

## Key Conventions

### main.py
```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from contextlib import asynccontextmanager
from database import init_db
from routers import domain_a, domain_b, domain_c

@asynccontextmanager
async def lifespan(app: FastAPI):
    init_db()
    yield

app = FastAPI(title="{App Name}", lifespan=lifespan)
app.add_middleware(CORSMiddleware, allow_origins=["*"], allow_methods=["*"], allow_headers=["*"])
app.include_router(domain_a.router, prefix="/domain-a", tags=["Domain A"])
app.include_router(domain_b.router, prefix="/domain-b", tags=["Domain B"])
app.include_router(domain_c.router, prefix="/domain-c", tags=["Domain C"])
```

### database.py
```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker, DeclarativeBase

DATABASE_URL = "sqlite:///./app.db"
engine = create_engine(DATABASE_URL, connect_args={"check_same_thread": False})
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

class Base(DeclarativeBase):
    pass

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

def init_db():
    Base.metadata.create_all(bind=engine)
```

### models.py
- 3-5 SQLAlchemy models with relationships
- Use domain-specific names (not generic "Item", "Record")
- Include at least one enum-like field (status, tier, category)
- Include at least one relationship (foreign key)
- Example: Guest → Booking → Itinerary, with loyalty_tier on Guest

### schemas.py
- Pydantic v2 models (model_config with from_attributes=True)
- Separate Create/Response schemas where appropriate
- Default values on optional fields (these become bug injection points)

### routers/
- One router per domain area
- Standard CRUD + 1-2 domain-specific queries
- Depends(get_db) for database sessions
- Each router should call service layer, NOT contain business logic directly

### services/
- Pure business logic functions
- Take db session as parameter
- This is where the interesting logic lives (pricing, matching, filtering)
- Primary target for bug injection (logic errors, not syntax errors)

### Data Flow (for debugging exercises)
```
Router (receives request, validates input)
  → Service (applies business logic, queries DB)
    → Model/Query (SQLAlchemy ORM operations)
      → Schema (serializes response)
```

Bugs should span at least 2 of these layers to create a realistic debugging exercise.

### tests/
- pytest with httpx AsyncClient (or TestClient)
- conftest.py provides test database and client fixtures
- 5-10 tests covering the main flows
- Tests should be good enough that planted bugs cause visible failures
- Use descriptive test names: `test_gold_guest_gets_gold_discount`

### requirements.txt
```
fastapi>=0.115.0
uvicorn>=0.34.0
sqlalchemy>=2.0
pydantic>=2.0
pytest>=8.0
httpx>=0.28.0
```

### CLAUDE.md (Participant Starter)
Should include:
- App name and one-sentence description
- How to set up: `pip install -r requirements.txt`
- How to run: `uvicorn main:app --reload`
- How to test: `pytest`
- Stack summary (Python, FastAPI, SQLite)
- NO hints about bugs, features, or internal architecture
