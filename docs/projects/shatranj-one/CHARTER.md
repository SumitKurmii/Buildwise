# Shatranj One — Project Charter (Approved Baseline)

**Status:** Approved baseline charter for Shatranj One (Phase 0 decision #4, transcribed from [Runbook Appendix A](../../Buildwise_V1_Antigravity_Build_Runbook.md#L374-L403)).  
**Modification policy:** Owner-authored and versioned. Agents may read this charter and propose changes only as a diff for the owner; agents must never edit or weaken it silently. Current progress is read from `PROJECT_VISION.md` and `README.md` and is never copied here.

---

## 1. Source of Truth

- Exactly two documentation files: `PROJECT_VISION.md` (objectives, architecture, scope, roadmap, gates, key technical decisions) and `README.md` (public overview, build instructions, reported implementation progress).
- There is no `docs/PLAN.md` (or `PLAN.md`) and none may be created.
- If code and documents disagree, report the discrepancy; do not resolve it silently.

## 2. Pinned Decisions (from `PROJECT_VISION.md`; conflicts go to the owner)

- C++17 standalone engine; 64-entry array board, `a1 = 0`, `h8 = 63`; bitboards only if measurement justifies them.
- Correctness and perft before search optimization; measure before claiming any optimization.
- The engine is an independent executable using UCI; the Node.js backend runs it as a subprocess and never searches inside the event loop.
- Deployment shape: static frontend host; backend and engine binary together in a container on a host that supports long-running processes.
- Position identity: piece placement, side to move, castling rights, and en-passant square; store the full FEN to verify matches.
- Deferred until after placements: neural networks, self-play, MCTS, Redis and queues, microservices, Kubernetes, observability stacks, WebAssembly.

## 3. Working Mode: `learner-authored`

- The owner writes the first implementation of every Shatranj One milestone. AI tools tutor and review.
- **Teacher:** Explains with small examples and diagrams first, then asks questions, then gives hints in this order: *hint → small example → partial code*. Full code only if the owner asks after attempting.
- **Supervisor:** Reviews list problems first and separate compilation, correctness, design, performance, and readability. Never treat a successful compile as correctness. Supervisor may produce review findings and descriptive suggested changes.
- **Patch proposals:** Machine-applicable patch proposals are **disabled by default** for Shatranj One. Proposing or applying a machine-applicable patch requires an explicit owner request and a patch-specific approval (and remains gated behind Phase 5/6 security controls).

## 4. Evidence Rules

- A claim such as "tests passed" must cite the commit SHA, environment, and command.
- Author-reported results are labelled `reported`, and CI results are labelled with the run.
- Reference data (for example perft counts) comes from repository test data with a cited source, not from model memory.

## 5. Hard Limits

- Buildwise never writes to or pushes from the real Shatranj One working tree. Development and tests use approved read-only source copies or a disposable fixture clone.
- Gates and dates come from `PROJECT_VISION.md`. Buildwise work never takes priority over Shatranj One's gates or the owner's DSA preparation.
