# ADR-0004: Shatranj One Sources, Ingestion & Connector Strategy

- **Status:** Source Rules & A1/A3 Baseline Accepted; **Decisions 10, 11, 14, and 15a Pending Owner Decision**
- **Date:** 2026-11-12
- **References:** [Spec §4.3–4.4](../Buildwise_Product_Specification_v1.md#L179-L224), [Spec §16–17](../Buildwise_Product_Specification_v1.md#L894-L1000), [Runbook §1.1–1.3, Appendix A](../Buildwise_V1_Antigravity_Build_Runbook.md#L33-L45)

## Accepted Baseline Rules

1. **Strict Two-File Authoritative Contract for Shatranj One (A3, Phase 0 Decision #5):**
   - Authoritative docs are exclusively `PROJECT_VISION.md` and `README.md`.
   - No `PLAN.md` or `docs/PLAN.md` exists or may be created.
2. **Real Working Tree Isolation:**
   - Buildwise never mounts, reads, writes, or pushes to the real Shatranj One working repository.
   - All Phase 2/5/6 development and testing use owner-provided read-only doc copies or a disposable fixture clone at a pinned commit.
3. **Commit-Pinned Snapshots (A1):**
   - Every imported snapshot records a commit SHA and a `dirty_working_tree` flag.

---

## Unresolved Decision 10: First-Run Shatranj One Ingestion *(Needed by Phase 2)*

### Option A — Controlled upload or read-only import of a pinned commit; local connector deferred to Phase 6 *(Runbook PROPOSAL)*
- **How it works:** Phase 2 ingests approved files via a controlled backend upload workflow or a read-only import from a pinned Shatranj One fixture archive/directory explicitly staged for Buildwise. Live local connector is deferred to Phase 6.
- **Pros:**
  - Eliminates host-filesystem exposure risks during Phase 2 while still testing parsing, chunking, pre-ranking ACLs, and RAG evaluation.
  - Keeps Phase 2 focused on RAG correctness and isolation rather than desktop/IDE daemon protocols.
- **Cons / Trade-offs:**
  - Requires manual re-import/upload when Shatranj One snapshots change until Phase 6.
  - Requires owner approval as a Runbook PROPOSAL.

### Option B — Build read-only local workspace connector in Phase 2
- **How it works:** Implement the scoped read-only companion/extension connector early in Phase 2 to pull files directly from a selected fixture folder.
- **Pros:**
  - Live snapshot refresh works earlier.
- **Cons / Trade-offs:**
  - Pulls Phase 6 R1 root/symlink/traversal security surface into Phase 2, violating ordered phase dependencies and the 7-day scope boundary.

---

## Unresolved Decision 11: Background Jobs for Ingestion *(Needed by Phase 2)*

### Option A — In-process background worker for V1.0; Redis + queue later
- **How it works:** FastAPI `BackgroundTasks` or an in-process async worker pool updates PostgreSQL `ingestion_jobs` rows without introducing Redis in Phase 2.
- **Pros:**
  - Fewer moving parts in local dev/CI (no Redis container required in Phase 2).
  - Sufficient for modest single-project Shatranj One corpora while keeping UI non-blocking.
- **Cons / Trade-offs:**
  - In-flight jobs do not survive process restarts cleanly unless resumed from `ingestion_jobs` DB state on startup.

### Option B — Redis + dedicated worker queue from Phase 2
- **How it works:** Add Redis + worker process (e.g., Celery/RQ/ARQ) in Phase 2.
- **Pros:**
  - True process isolation and durable queue retries out of the box.
- **Cons / Trade-offs:**
  - Additional infrastructure container, configuration, and CI complexity earlier than needed (Spec §13 notes *"Redis + worker queue when needed"*).

---

## Unresolved Decision 14: A1 Commit SHA Source Without GitHub Integration *(Needed by Phase 2)*

### Option A — Read automatically (read-only) from fixture clone's Git metadata when importing a fixture snapshot
- **Pros:** Accurate, hard to mistype; automatically detects `dirty_working_tree` via read-only `git status`/metadata check.
- **Cons / Trade-offs:** Only available when importing a Git-backed fixture directory (not plain file uploads).

### Option B — Owner-entered commit SHA + dirty flag on upload/import
- **Pros:** Works cleanly with controlled file/archive uploads that strip `.git` metadata.
- **Cons / Trade-offs:** Relies on accurate manual entry by the owner during upload.

*(Note: A hybrid is also possible — auto-read read-only from fixture Git metadata when present, and require owner-entered metadata on plain uploads — subject to owner decision.)*

---

## Unresolved Decision 15a: Local Workspace Connector Form *(Deferred to Phase 6)*

### Option A — Standalone local companion process (e.g., Python/CLI agent with outbound HTTPS/WebSocket to Buildwise API)
- **Pros:** Editor-agnostic; works even when VS Code is closed; simpler automated integration testing against a fixture directory.
- **Cons / Trade-offs:** User runs and manages a separate local daemon/CLI.

### Option B — VS Code extension
- **Pros:** Integrated directly inside the owner's editor UI with native workspace-folder selection.
- **Cons / Trade-offs:** Higher packaging, TypeScript extension-host boilerplate, and harder automated headless testing in CI.

### Decision Status
- **Decisions 10, 11, 14:** `Pending Owner Decision` (required before Phase 2).
- **Decision 15a:** `Deferred to Phase 6` (required before Phase 6 connector work starts).
