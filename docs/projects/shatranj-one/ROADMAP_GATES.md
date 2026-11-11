# Shatranj One — Roadmap, Gates, and Acceptance Criteria (Read-Only Mirror)

**Status:** `GENERATED` (imported read-only from approved source copies; awaiting owner Phase 0 review)

## 1. Source Snapshot Metadata (A1 & A3)

- **Source location (approved read-only copies):**
  - `docs/projects/shatranj-one/sources/PROJECT_VISION.md`
  - `docs/projects/shatranj-one/sources/README.md`
- **Source commit SHA:** `72f8ee8c9922f170ab0f04b0afda7641abdde32a`
- **Original working tree status:** Clean (`dirty_working_tree: false`)
- **Integrity note:** Both source copies were verified byte-for-byte against the originals via SHA256 prior to import (owner-confirmed).
- **Imported timestamp (UTC):** `2026-11-11T23:22:50Z` *(from `2026-11-12T04:52:50+05:30`)*
- **Source-of-truth rule (A3):** `PROJECT_VISION.md` and `README.md` remain authoritative. There is no `PLAN.md` and none may be created. Buildwise treats this mirror as read-only reference data; discrepancies between code and these documents must be reported, never silently resolved.

---

## 2. Roadmap Milestones & Target Dates (`PROJECT_VISION.md §5`, `README.md §Roadmap`)

> **Planning note (`PROJECT_VISION.md §5`, L90):** Target dates are planning targets; completion depends on testing and demonstrated functionality. If a milestone slips, optional scope is reduced rather than skipping correctness checks.

| # | Milestone / Intended Outcome | Target Date (`PROJECT_VISION.md §5`) | Dependencies & Ordering (`PROJECT_VISION.md` / `README.md`) | Reported Status (`README.md §Roadmap & §Progress`) |
|---|---|---|---|---|
| M1 | Reliable repository, reproducible build, and automated CI | October 31, 2026 | Foundation for reproducible fresh-clone builds and CI regression checks (`§9`) | `[x]` Repository setup and GitHub Actions CI (`README.md` L19, L77–87, L122–129) |
| M2 | Position representation, legal move generation, and perft validation | December 21, 2026 | Depends on M1 build/CI setup; split in `README.md` into Position/FEN (done) and Legal move gen + perft (next milestone, `README.md` L20–21, L131–135) | **Partial:** `[x]` Position representation and FEN (`README.md` L20, L91–120); `[ ]` Legal move generation and perft (`README.md` L21, L131–135) |
| M3 | Search implementation and UCI support | January 31, 2027 | **Explicit gate (`§7` L124, `§3` L47):** Legal move generation, make/unmake, and perft validation must pass **before** search is trusted/optimized | `[ ]` Search; `[ ]` UCI protocol (`README.md` L22–23) |
| M4 | Deployed, playable application | February 28, 2027 | Depends on tested C++ UCI engine executable (M3) + React frontend + Node.js backend subprocess wrapper (`§4`, `§7` L123, L125) | `[ ]` Web application; `[ ]` Deployment (`README.md` L14–15, L24–25) |
| M5 | Game history and review functionality | March 31, 2027 | Depends on playable web app (M4); browser storage + PGN export first, PostgreSQL introduced when game history requires it (`§4` L66, `§7` L126) | `[ ]` Not yet started (part of Web application / post-M4 scope) |
| M6 | Feature freeze and stabilization | April 30, 2027 | Follows core platform features (M1–M5) | `[ ]` Not yet started |
| M7 | Testing, documentation, benchmarks, demonstration, and interview preparation | May 2027 | Follows feature freeze & stabilization (M6) | `[ ]` Not yet started |

---

## 3. Milestone Breakdown, Dependencies & Acceptance Criteria (Source-Derived Summary)

This section summarizes requirements from the authoritative source documents. Consult the cited source sections for their original wording.

### M1 — Repository, Reproducible Build & Automated CI
- **Source citations:** `PROJECT_VISION.md §5, §6, §9` (L82, L107, L151–152); `README.md §Tech Stack, §Build, §Run Tests, §Continuous Integration, §Progress` (L8–13, L29–87, L122–129)
- **Requirements / Criteria in sources:**
  - Reproducible build from a fresh clone (`C++17`, `CMake`, `GoogleTest`, `CTest`).
  - Automated CI catches regressions: GitHub Actions workflow configures, builds (Debug and Release), and runs `CTest` on `ubuntu-latest` for pushes and PRs to `main`; intentional failure verified so failing tests fail the workflow.
- **Reported status (`README.md` L71–75, L128–129):**
  - Author-reported `81/81 tests passing` locally and `81/81 tests passing` in GitHub Actions.
  - **A2 Evidence caveat (`README.md` L75):** *"The exact commit SHA associated with the reported CI run has not yet been confirmed."*

### M2 — Position Representation, FEN, Legal Move Generation & Perft Validation
- **Source citations:** `PROJECT_VISION.md §3` (L24–30, L47), `§5` (L83), `§7` (L122, L124, L127); `README.md §Progress, §Next Milestone` (L20–21, L91–120, L131–135)
- **Sub-part 2A — Position & FEN (reported `[x]` in `README.md` L20, L91–120):**
  - 64-entry array board (`a1 = 0`, `h8 = 63`); piece encoding, side to move, castling rights, en-passant square, halfmove/fullmove counters, reset/accessor validation.
  - Full FEN parsing, validation (invalid pieces/ranks/side/castling/en-passant/counters/extra fields/kings/terminal-rank pawns), preserving previous position on failure, serialization, and round-trip tests.
- **Sub-part 2B — Legal Move Generation & Perft (reported `[ ]` / Next Milestone in `README.md` L21, L131–135):**
  - Pseudo-legal and legal chess move generation.
  - Special moves: castling, en passant, pawn promotion.
  - Make/unmake tests.
  - Perft validation using known reference positions and reference node counts.
  - **Not specified in source documents:** Specific perft depth numbers, reference FEN strings, and numeric node counts (must come from Shatranj One repository test data per Charter §4 / A2, not model memory).

### M3 — Search, Evaluation & UCI Protocol
- **Source citations:** `PROJECT_VISION.md §3` (L32–47), `§5` (L84), `§7` (L123–124); `README.md §Roadmap` (L22–23)
- **Hard dependency:** Perft and make/unmake correctness (M2) required before trusting or optimizing search (`§3` L47, `§7` L124).
- **Requirements / Criteria in sources:**
  - Basic position evaluation function; negamax with alpha-beta pruning; iterative deepening and move ordering; quiescence search; transposition table.
  - Benchmark search speed, node counts, and playing strength (measure before claiming optimizations, `§9` L150).
  - Implement Universal Chess Interface (UCI) protocol usable by compatible tools and Shatranj One's backend.
  - Planning target: search depth 12 in suitable positions, subject to measured performance and resources (`§3` L45 — explicitly a goal, not a guarantee).
  - **Not specified in source documents:** Specific NPS benchmarks, Elo targets, or memory/TT size limits.

### M4 & M5 — Web Application, Game History/Review & Deployment
- **Source citations:** `PROJECT_VISION.md §4` (L49–76), `§5` (L85–86), `§6` (L105–118), `§7` (L123, L125–126); `README.md §Tech Stack, §Roadmap` (L14–16, L24–25)
- **Dependencies:**
  - M4 depends on standalone C++ UCI executable (M3); Node.js backend spawns and manages it as a subprocess with search limits, timeouts, and a concurrency limit (never searching inside the Node.js event loop, `§4` L64, `§7` L123).
  - Persistent database storage (PostgreSQL) is introduced only when game history requires it (`§4` L66, `§7` L126); browser storage and PGN export come first (`§7` L126).
  - Optional stretch goal (server-authoritative 1v1 WebSocket multiplayer, or small eval-weight tuning experiment) is gated on completing earlier milestones (`§4` L74, `§6` L109).
- **Requirements / Criteria in sources:**
  - Frontend: React UI with interactive chessboard/move controls, engine-game interface, engine recommendations/analysis display, and game history/review screens.
  - Backend: Node.js API, subprocess UCI integration with limits/timeouts/concurrency cap, guest mode first (user accounts only if schedule allows).
  - Deployment: Static host for frontend; backend and engine binary together in a container on a host supporting long-running processes; working browser-accessible deployed demo.

### M6 & M7 — Stabilization, Final Acceptance & Placement Readiness
- **Source citations:** `PROJECT_VISION.md §5` (L87–88), `§6` (L92–118), `§10` (L157–168)
- **Acceptance criteria (`Definition of Success` by end of May 2027, `§10` L159–168):**
  1. Custom C++ chess engine with tested legal move generation and working search.
  2. UCI support.
  3. Deployed web application with meaningful playable functionality.
  4. Game review and history features *(subject to scope-cut rules in §6 if schedule slips, while never cutting custom engine, move-gen correctness, deployed playable app, core tests, and docs)*.
  5. Automated build and test checks.
  6. Documentation, performance measurements, and demonstration suitable for placement interviews.
  7. System design and implementation can be explained clearly.

---

## 4. Scope Priorities, Cut Order & Deferred Items (`PROJECT_VISION.md §6, §8`)

- **Must have:** Tested engine core, UCI, deployed playable application, README, benchmarks, CI.
- **Strong additions:** Saved games, PGN import, position history, game review, user accounts (guest mode first).
- **Optional (at most one):** Server-authoritative WebSocket multiplayer, or a small evaluation-weight tuning experiment.
- **Cut order if schedule slips:** (1) optional stretch goal → (2) advanced history statistics (keep PGN import, replay, single-position lookup) → (3) user accounts (ship guest mode) → (4) asynchronous analysis jobs beyond a simple in-process queue.
- **Never cut:** Custom engine, move-generation correctness, deployed playable application, core tests, documentation.
- **Deferred (post-placements / non-V1):** Neural-network evaluation, training/large-scale self-play, MCTS, Redis & background job queues, microservices & Kubernetes, advanced distributed observability, bitboards/magic bitboards (unless justified by measurement), WebAssembly in-browser compilation, social features/tournaments/rating systems.
