# HOL Toolkit

A suite of Claude Code skills for building hands-on lab (HOL) workshops that teach engineers how to use Claude Code.

Each lab is a small, self-contained app with planted bugs and a missing feature. Participants work through 5 acts — Explore, Fix, Spec, Build, Show & Tell — using Claude Code as their primary tool.

## What's in the box

Six skills that automate the lab creation pipeline:

| Skill | What it does |
|-------|-------------|
| `/hol-build-lab` | The main orchestrator. Takes a brief (PDF, doc, or text describing the company and app concept), asks clarifying questions, then generates a complete lab repo. |
| `/hol-brand` | Branding pass. Ensures the app uses the company's domain language, terminology, and naming conventions — not generic placeholders. |
| `/hol-data` | Generates realistic synthetic seed data with industry-authentic values, proper relationships, and edge cases that help surface bugs naturally. |
| `/hol-feature` | Proposes 3-5 features suitable for a ~30 min spec-driven build exercise. Each touches multiple layers and is demo-able. |
| `/hol-bug` | Plants 1-2 subtle multi-file bugs. Each spans 2-3 files, compiles cleanly, and has a satisfying "aha" moment when diagnosed. |
| `/hol-run-lab` | Validates a finished lab by simulating a participant walking through all 5 acts. Produces a report with pass/fail, timing estimates, and facilitator recommendations. |

## Quick start

```bash
cd hol-toolkit

# Generate a lab from a brief
claude
> /hol-build-lab path/to/company-brief.pdf

# Validate the lab works
> /hol-run-lab
```

The build skill will ask you a few questions about the app concept and domain before generating anything.

## Default stack

Generated labs use **Python / FastAPI / SQLite** with pytest. Each lab is ~10-15 files — small enough to explore in 10 minutes, large enough to have real multi-file debugging exercises.

## Workshop structure

Each lab follows a 5-act structure (~2.5 hrs of active work):

| Act | Duration | What happens |
|-----|----------|-------------|
| **Explore** | 10 min | Participants drop into the repo cold and use Claude Code to map the codebase. No README walkthrough. |
| **Fix** | 20 min | Find and fix the planted bug(s). Write a regression test. |
| **Spec** | 20 min | Receive a feature brief, write a technical spec using the included template. |
| **Build** | 30 min | Implement the feature from the spec using Claude Code. |
| **Show & Tell** | 15 min | 3-4 participants demo what they shipped. |

See `references/act-definitions.md` for detailed facilitator guidance on each act.

## Reference docs

- `references/app-scaffold.md` — The standard architecture generated labs follow
- `references/act-definitions.md` — Detailed 5-act workshop structure with timing, facilitator tips, and success criteria
## How a generated lab is structured

```
{lab-app}/
├── main.py              # FastAPI app
├── models.py            # SQLAlchemy models
├── schemas.py           # Pydantic schemas
├── database.py          # SQLite setup
├── routers/             # API endpoints
├── services/            # Business logic (where bugs live)
├── seed.py              # Synthetic data seeder
├── tests/               # pytest suite (some fail due to planted bugs)
├── requirements.txt
├── CLAUDE.md            # Participant starter file
└── .hol/
    ├── templates/       # Spec template for Act 3
    ├── features/        # Feature proposals
    ├── bugs/            # Answer keys (gitignored for participants)
    ├── facilitator/     # Workshop guide
    └── validation/      # Lab runner report
```

## Running a workshop

1. **Prep:** Run `/hol-build-lab` with a brief for the target company. Review the output. Run `/hol-run-lab` to validate.
2. **Distribute:** Fork or clone the lab repo for each participant. The `.hol/bugs/` and `.hol/facilitator/` directories are gitignored so participants don't see answer keys.
3. **Facilitate:** Follow the timing and hints in `.hol/facilitator/README.md`. The act definitions in `references/` have detailed guidance for each phase.
