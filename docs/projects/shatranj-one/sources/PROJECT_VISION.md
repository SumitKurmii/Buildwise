# Shatranj One — Project Vision

## 1. Overview

Shatranj One is a full-stack chess platform built around a chess engine implemented from scratch in C++.

The project combines chess-engine development, algorithmic problem-solving, backend engineering, frontend development, testing, and deployment into one long-term engineering project.

The goal is not merely to build a chess website or integrate an existing engine. It is to understand how a chess engine works internally and build a complete application around it.

## 2. Core Objectives

- Develop an independently implemented C++ chess engine.
- Build a web application for playing chess against the engine and other players.
- Provide chess-game analysis and move recommendations.
- Store games and allow users to review their previous matches.
- Demonstrate sound software engineering through testing, CI, documentation, and deployment.
- Build a project that provides meaningful material for placement interviews and technical discussions.

## 3. Chess Engine

The engine is the technical core of Shatranj One.

### Position and Move Generation

- Design the board and position representation.
- Implement FEN parsing and serialization.
- Generate pseudo-legal and legal chess moves.
- Handle special moves, including castling, en passant, and pawn promotion.
- Validate move generation using perft tests and known reference positions.

### Search and Evaluation

- Develop a basic position evaluation function.
- Implement negamax with alpha-beta pruning.
- Add iterative deepening and move ordering.
- Implement quiescence search.
- Add a transposition table.
- Benchmark search speed, node counts, and playing strength.

### Engine Communication

- Implement the Universal Chess Interface (UCI) protocol.
- Make the engine usable by compatible chess tools and the Shatranj One application.
- Target search depth 12 in suitable positions, subject to measured performance and available computing resources. Depth is a goal, not a guarantee of playing strength.

**Primary engineering principle:** Correct move generation and search behavior come before optimization or advanced features.

## 4. Web Application

The application will connect the chess engine to a usable chess-playing experience.

### Frontend

- React-based user interface.
- Interactive chessboard and move controls.
- Engine-game interface.
- Display of engine recommendations and analysis.
- Game history and review screens.

### Backend

- Node.js backend for application APIs.
- Communication between the application and the C++ engine, with search limits, timeouts, and a concurrency limit.
- Game management, with guest mode first and user accounts only if the schedule allows.
- Persistent storage for completed games and relevant game information, introduced when game history requires it.

### Product Features

- Play against the custom engine.
- Review completed games.
- Analyze positions and display recommended moves.
- Deploy a working version that can be demonstrated through a browser.
- Optional stretch goal, only if earlier milestones are completed: server-authoritative one-on-one matches over WebSockets.

The frontend, backend, and database choices should remain practical and should be introduced when the corresponding functionality requires them.

## 5. Development Roadmap

| Target date | Intended outcome |
|---|---|
| October 31, 2026 | Reliable repository, reproducible build, and automated CI |
| December 21, 2026 | Position representation, legal move generation, and perft validation |
| January 31, 2027 | Search implementation and UCI support |
| February 28, 2027 | Deployed, playable application |
| March 31, 2027 | Game history and review functionality |
| April 30, 2027 | Feature freeze and stabilization |
| May 2027 | Testing, documentation, benchmarks, demonstration, and interview preparation |

These dates are targets for planning. Actual completion will depend on testing and demonstrated functionality. If a milestone slips, reduce optional scope rather than skipping correctness checks.

## 6. Scope Before Placements

The priority through May 2027 is to deliver a working, understandable, and demonstrable product rather than accumulate a large number of technologies.

The required focus areas are:

1. A correct and tested chess-engine core.
2. Reliable communication between the engine and application.
3. A usable, deployed chess platform.
4. Clear documentation, benchmarks, and a credible project demonstration.

The project must remain manageable alongside DSA preparation, academic performance, and placement preparation.

### Scope Tiers

- **Must have:** tested engine core, UCI, deployed playable application, README, benchmarks, and CI.
- **Strong additions:** saved games, PGN import, position history, game review, and user accounts (guest mode first).
- **Optional (at most one):** WebSocket multiplayer, or a small evaluation-weight tuning experiment.

### Cut Order if the Schedule Slips

1. The optional stretch goal.
2. Advanced history statistics (keep PGN import, replay, and single-position lookup).
3. User accounts (ship guest mode).
4. Asynchronous analysis jobs beyond a simple in-process queue.

**Never cut:** the custom engine, move-generation correctness, the deployed playable application, core tests, and documentation.

## 7. Key Technical Decisions

- **Board representation:** a 64-entry array behind a clean interface (`a1 = 0`, `h8 = 63`). Bitboards are considered only if profiling shows a measurable benefit after correctness is established.
- **Engine process:** the engine is an independent C++ executable that communicates over UCI. The Node.js backend starts and manages it as a subprocess; searches never run inside the Node.js event loop.
- **Correctness first:** perft and make/unmake tests are required before search is trusted.
- **Deployment shape:** the frontend is hosted on a static host; the backend and engine binary run together in a container on a host that supports long-running processes.
- **Persistence:** browser storage and PGN export first; PostgreSQL when game history requires it.
- **Position identity:** piece placement, side to move, castling rights, and en-passant square; the full FEN is stored to verify matches.

## 8. Deferred Features

The following are future research or expansion possibilities, not requirements for the initial release:

- Neural-network-based position evaluation.
- Training models and large-scale self-play.
- Monte Carlo Tree Search.
- Redis and background job queues.
- Microservices and Kubernetes.
- Advanced distributed infrastructure and observability.
- Bitboards and magic bitboards unless measurements justify them.
- Compiling the engine to WebAssembly for in-browser play.
- Social features, tournaments, and rating systems.

These features should be reconsidered after placements or when the core product is complete and stable.

## 9. Engineering Principles

- Build incrementally and keep each stage testable.
- Prefer correctness over premature optimization.
- Use tests to verify behavior rather than relying only on successful compilation.
- Measure performance before making optimization claims.
- Keep the build reproducible from a fresh clone.
- Use CI to catch regressions early.
- Document implemented and verified functionality separately from planned features.
- Keep the architecture as simple as the current requirements allow.
- Record significant design decisions, the alternatives rejected, and their trade-offs.

## 10. Definition of Success

By the end of May 2027, Shatranj One should have:

- A custom C++ chess engine with tested legal move generation and working search.
- UCI support.
- A deployed web application with meaningful playable functionality.
- Game review and history features.
- Automated build and test checks.
- Documentation, performance measurements, and a demonstration suitable for placement interviews.

Success means having a working system whose design and implementation can be explained clearly — not simply having a long feature list.