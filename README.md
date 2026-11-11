# Buildwise (V1.0 — In Development)

**Current status:** Phase 0 baseline only. No application runtime, database schema, authentication, RAG pipeline, or UI has been implemented yet.

## Overview

Buildwise is a project-aware AI engineering workspace being built as a modular monolith in Python (FastAPI) and TypeScript (React), backed by PostgreSQL (`pgvector`), with deny-by-default authorization and role-scoped AI assistants (**Teacher**, **Progress Mentor**, and **Supervisor**).

The first workspace project is **Shatranj One**. Buildwise remains independent of the real Shatranj One working repository and uses only approved read-only copies or disposable fixture clones at pinned commits.

## Authoritative Baseline Documents

- Product & Architecture Specification: [docs/Buildwise_Product_Specification_v1.md](docs/Buildwise_Product_Specification_v1.md)
- Build Runbook & Owner Checkpoints: [docs/Buildwise_V1_Antigravity_Build_Runbook.md](docs/Buildwise_V1_Antigravity_Build_Runbook.md)
- Workspace Rules: [.agent/rules/buildwise.md](.agent/rules/buildwise.md)
- Implementation Status Ledger: [STATUS.md](STATUS.md)
- Security Baseline: [SECURITY.md](SECURITY.md)
- Architecture Decision Records: [docs/ADR/README.md](docs/ADR/README.md)
- Shatranj One Profile & Charter: [docs/projects/shatranj-one/PROFILE.md](docs/projects/shatranj-one/PROFILE.md)

## What Exists Today (Phase 0)

- Approved V1.0 product specification, Antigravity build runbook, and workspace rules.
- Repository directory skeleton for the modular monolith (`apps/api/app/*`, `apps/web/`, `packages/`, `infra/`, `tests/`).
- Root governance files (`STATUS.md`, `SECURITY.md`, `CONTRIBUTING.md`, `.env.example`, `.gitignore`).
- Initial Architecture Decision Records (`docs/ADR/0001` through `0007`), with deferred decisions documented as Options & Trade-offs awaiting owner approval.
- Shatranj One project profile and baseline charter (`docs/projects/shatranj-one/CHARTER.md`).

## What Is Not Yet Implemented

- Phase 1–8 application code, database migrations, authentication, authorization engine, knowledge retrieval, model gateway, agent orchestrator, UI, and sandboxed execution.
- Import of Shatranj One's `PROJECT_VISION.md` and `README.md` roadmap/gates (currently `BLOCKED` pending owner-supplied read-only source files and commit metadata).
