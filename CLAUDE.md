# HOL Toolkit

This repo contains Claude Code skills for building hands-on lab (HOL) workshops that teach engineers how to use Claude Code.

## What This Repo Is

A toolkit of 6 skills that automate the creation and validation of HOL workshop labs. Each lab is a small Python/FastAPI/SQLite app with planted bugs and a missing feature.

## Skills

- `/hol-build-lab` — Main orchestrator. Takes a HOL brief (PDF/doc/text) and generates a complete lab repo.
- `/hol-brand` — Branding pass. Ensures the app matches a company's domain and language.
- `/hol-data` — Generates realistic synthetic seed data for the branded app.
- `/hol-feature` — Proposes 3-5 features suitable for a 30-min spec-driven build exercise.
- `/hol-bug` — Plants subtle multi-file bugs for a debugging exercise.
- `/hol-run-lab` — Validates a lab by simulating a participant walking through all 5 Acts.

## Default Stack

Generated lab apps use: Python, FastAPI, SQLite, pytest. ~10-15 files.

## Reference Docs

- `references/app-scaffold.md` — Standard architecture for generated apps
- `references/act-definitions.md` — The 5-Act workshop structure
## Typical Workflow

1. Write or receive a HOL brief (company profile + app concept)
2. Run `/hol-build-lab` with the brief
3. Review the generated lab
4. Run `/hol-run-lab` to validate it works end-to-end
5. Fork the lab repo for each participant
