# Project Profile — Shatranj One

**Status:** Profile baseline created and authoritative documentation imported (`GENERATED`; awaiting owner Phase 0 review).

## 1. Identity & Scope

- **Project name:** Shatranj One
- **Tenant scope:** Personal workspace owned by Sumit (first connected project in Buildwise V1.0)
- **Repository boundary rule:** Buildwise **never** mounts, reads, modifies, or pushes to the real Shatranj One working repository. All Buildwise Phase 2/5/6 development and tests use approved read-only source copies or a disposable fixture clone at a pinned commit.
- **Approved source snapshot (A1):**
  - Source copy directory: `docs/projects/shatranj-one/sources/` (SHA256-verified against originals by owner)
  - Source commit SHA: `72f8ee8c9922f170ab0f04b0afda7641abdde32a`
  - Original working tree: Clean (`dirty_working_tree: false`)

## 2. Authoritative Documentation Sources (Strict Two-File Contract)

Per [Runbook §5 Phase 0](../../Buildwise_V1_Antigravity_Build_Runbook.md#L124-L125) and **A3 / Appendix A**:
- **Authoritative documentation sources are strictly:**
  1. `PROJECT_VISION.md` (objectives, architecture, scope, roadmap, gates, key technical decisions)
  2. `README.md` (public overview, build instructions, reported implementation progress)
- **Explicit prohibition:** There is **no `PLAN.md`** (and no `docs/PLAN.md`), and none must ever be created or assumed.

## 3. Active Project Policies (Accepted Baseline)

- **A1 (Commit-pinned snapshots):** Every imported snapshot must record its commit SHA and dirty-working-tree flag.
- **A2 (Environment-specific evidence):** Evidence records include OS, compiler, command, exit code, summary, and commit SHA. Until GitHub integration exists, CI results are owner-entered and labelled as reported.
- **A3 (Source-of-truth contract):** `PROJECT_VISION.md` and `README.md` remain authoritative; Buildwise mirrors gates/acceptance criteria read-only with last-synced commit and drift detection. Progress Mentor may propose documentation diffs in the review queue only and never edits these files directly.
- **A4 (Project charter & `learner-authored` mode):** Governed by [CHARTER.md](CHARTER.md), derived from approved Runbook Appendix A. Machine-applicable patch proposals are **disabled by default**; Teacher uses hints-first pedagogy; Supervisor may still produce review findings and descriptive suggested changes.
- **A5 (Sandbox dependency gate):** Shatranj One's configure-time GoogleTest download must be resolved via a written decision record before Phase 6 begins (`docs/ADR/0007-sandbox-dependencies-and-hosting.md`).

## 4. Related Project Records

- Baseline Charter: [CHARTER.md](CHARTER.md)
- Roadmap, Gates & Acceptance Criteria Mirror: [ROADMAP_GATES.md](ROADMAP_GATES.md) *(imported `GENERATED` from `sources/PROJECT_VISION.md` & `sources/README.md` at `72f8ee8c9922f170ab0f04b0afda7641abdde32a`)*
