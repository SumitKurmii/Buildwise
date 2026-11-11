# Buildwise V1.0 — Implementation Status Ledger

Allowed statuses ([Runbook §3](docs/Buildwise_V1_Antigravity_Build_Runbook.md#L88-L99)):
- `NOT STARTED` — Nothing exists
- `GENERATED` — Exists / drafted, not run or tests missing (or awaiting owner review)
- `TESTED` — Automated tests pass with saved command output
- `VERIFIED` — Tested, owner has reviewed code/evidence, and CI is green on that commit
- `PROVISIONAL` — Built on top of a failing or unverified earlier item
- `BLOCKED` — Cannot progress; reason and evidence recorded

> **Note:** Statuses below track Buildwise implementation progress. Buildwise's domain-level task workflow (`Planned`, `In progress`, `Implemented`, `Tested`, `Verified`, `Blocked`, `Deferred` in Spec §3.2) is separate.

---

## Phase 0 — Product and Project Baseline

| Phase | Item | Status | Evidence | Owner Review Date | Known Issues |
|---|---|---|---|---|---|
| Phase 0 | Approved Spec & Runbook baseline (`docs/Buildwise_Product_Specification_v1.md`, `docs/Buildwise_V1_Antigravity_Build_Runbook.md`) | GENERATED | Committed at `9c4885d` | Pending | Approved baseline; remains non-`VERIFIED` until Phase 0 sign-off and CI exist |
| Phase 0 | Workspace rules (`.agent/rules/buildwise.md`) | GENERATED | Committed by owner at `04c8d89` | Pending | Committed by owner; awaits Phase 0 ledger sign-off and CI |
| Phase 0 | Repository skeleton (modular monolith inside `apps/api/app/`, `apps/web/`, `packages/`, `infra/`, `tests/`) | GENERATED | Empty `.gitkeep` placeholders created (uncommitted) | Pending | No implementation code (by design) |
| Phase 0 | Root governance & config stubs (`README.md`, `CONTRIBUTING.md`, `STATUS.md`, `.env.example`, `.gitignore`) | GENERATED | Files created (uncommitted) | Pending | — |
| Phase 0 | Security documentation hierarchy (`SECURITY.md`, `docs/SECURITY_MODEL.md`, `docs/threat-model/README.md`) | GENERATED | Baseline stubs created (uncommitted) | Pending | Threat model full corpus deferred to Phase 7 |
| Phase 0 | Architecture Decision Records (`docs/ADR/README.md`, `0001`–`0007`) | GENERATED | ADRs created with Options & Trade-offs for undecided items (uncommitted) | Pending | Decisions 7–16 remain open for owner choice before respective phases |
| Phase 0 | Shatranj One project profile & baseline charter (`docs/projects/shatranj-one/PROFILE.md`, `CHARTER.md`) | GENERATED | Created per Appendix A (`PROJECT_VISION.md` + `README.md` only; no `PLAN.md`) | Pending | — |
| Phase 0 | Shatranj One roadmap, gates & acceptance criteria import (`docs/projects/shatranj-one/ROADMAP_GATES.md`) | BLOCKED | Stub created; no Shatranj One sources in workspace | Pending | **Blocker:** Awaiting owner-provided approved read-only copies of `PROJECT_VISION.md`, `README.md`, and source commit metadata (A1) |
| Phase 0 | Exit Criterion: Decision records exist for data model, auth, connector, provider integration & §1.1 items | GENERATED | `docs/ADR/0001`–`0007` created | Pending | Requires owner review & sign-off |
| Phase 0 | Exit Criterion: Owner can explain V1.0 first-release scope and deferred items | GENERATED | Summarized in Phase 0 report and `docs/ADR/README.md` | Pending | Requires owner confirmation |
| Phase 0 | Exit Criterion: Shatranj One sources identified and recorded | BLOCKED | Source filenames (`PROJECT_VISION.md`, `README.md`) recorded; repo snapshot pending | Pending | Awaiting approved read-only source copies and commit metadata |

---

## Phases 1–8 — Downstream Phases (Not Started)

| Phase | Item | Status | Evidence | Owner Review Date | Known Issues |
|---|---|---|---|---|---|
| Phase 1 | Application foundation (React shell, FastAPI backend, PostgreSQL schema/migrations, auth, tenant/project scoping, central authz, audit events, owner authz test matrix) | NOT STARTED | — | — | Blocked on Phase 0 sign-off + Phase 1 decisions (ADR-0002 RLS, ADR-0003 Auth/Session) + owner authz matrix + quota check |
| Phase 2 | Project knowledge & file access (resource registry, controlled import/upload, parser, chunking, embeddings, pre-ranking ACL pgvector retrieval, citations, owner RAG eval set) | NOT STARTED | — | — | Blocked on Phase 1 security gate + Phase 2 decisions (ADR-0004 ingestion/jobs/SHA, ADR-0005 embeddings) |
| Phase 3 | Chat & model gateway (streaming chat, history, Experiential Labs adapter, server-side `EXPLABS_API_KEY`, allowlist/router, usage/budgets, fake provider & no-LLM mode) | NOT STARTED | — | — | Blocked on Phase 2 + ADR-0005 small-model decision & provider verification |
| Phase 4 | Three role modes (Teacher, Progress Mentor, Supervisor profiles, server-side tool capability intersection, A4 `learner-authored` constraints, run history) | NOT STARTED | — | — | Blocked on Phase 3 |
| Phase 5 | Progress tracking & technical review (milestones/tasks/dependencies, A2 environment-tagged evidence, A3 read-only mirror + drift detection, Supervisor reviews, approval state machine) | NOT STARTED | — | — | Blocked on Phase 4 tool permission gate; approval checks isolated from Phase 6 patch application |
| Phase 6 | Controlled file modifications & execution (diff proposals w/ hash verification, scoped connector, patch apply, Docker sandbox) — **FLAG OFF BY DEFAULT** | NOT STARTED | — | — | Blocked on Phase 5 approval gate + **mandatory A5 decision gate** + ADR-0004 connector form decision |
| Phase 7 | Security hardening & evaluation (full threat model in `docs/threat-model/README.md` indexed from `SECURITY.md`, owner 10+ prompt-injection corpus, isolation tests, audit/usage views, eval reports) | NOT STARTED | — | — | Blocked on Phase 6 + ADR-0007 hosting decision |
| Phase 8 | Enterprise enablement (orgs, invitations, configurable roles, per-tenant collections/budgets, optional MFA/SSO, audit export, synthetic HR/salary example) — **FLAG OFF BY DEFAULT** | NOT STARTED | — | — | Blocked on Phase 7 |
