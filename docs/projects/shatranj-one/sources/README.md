![C++ Engine CI](https://github.com/SumitKurmii/ShatranjOne/actions/workflows/ci.yml/badge.svg)

# ShatranjOne

A chess engine written in C++17, with CMake, GoogleTest, CTest, and GitHub Actions CI. The long-term goal is to build a full-stack chess platform around the custom engine.

## Tech Stack

- C++17
- CMake
- GoogleTest
- CTest
- GitHub Actions CI
- React (planned)
- Node.js (planned)

## Roadmap

- [x] Repository setup and GitHub Actions CI
- [x] Position representation and FEN
- [ ] Legal move generation and perft
- [ ] Search
- [ ] UCI protocol
- [ ] Web application
- [ ] Deployment

See [PROJECT_VISION.md](PROJECT_VISION.md) for the complete roadmap, project architecture, scope, and technical decisions.

## Build

### Windows (MinGW)

From the repository root:

```powershell
cmake -S engine -B engine/build -G "MinGW Makefiles"
cmake --build engine/build
```

### Linux / macOS / CI

With a C++ compiler and CMake installed:

```bash
cmake -S engine -B engine/build
cmake --build engine/build
```

## Run the Engine

### Windows

```powershell
.\engine\build\shatranj-one.exe
```

### Linux or macOS

```bash
./engine/build/shatranj-one
```

## Run Tests

From the repository root:

```bash
ctest --test-dir engine/build --output-on-failure
```

**Latest reported local result: 81/81 tests passing.**

**Latest reported GitHub Actions result: 81/81 tests passing.**

The exact commit SHA associated with the reported CI run has not yet been confirmed.

## Continuous Integration

GitHub Actions CI is configured for the C++ engine.

The workflow covers:
- Debug
- Release

The workflow configures, builds, and runs CTest on `ubuntu-latest` for pushes and pull requests to `main`.

The CI pipeline was also tested with an intentional failure to verify that failing tests make the workflow fail.

## Progress

### Position System

- [x] Created `Position` class
- [x] Added 64-square mailbox board representation (`a1 = 0`, `h8 = 63`)
- [x] Added piece encoding
- [x] Added side-to-move state
- [x] Added castling rights
- [x] Added en-passant target square
- [x] Added halfmove and fullmove counters
- [x] Added position reset functionality
- [x] Added board and state accessors
- [x] Added validation for invalid setter inputs

### FEN

- [x] Parse board placement
- [x] Parse side to move
- [x] Parse castling rights
- [x] Parse en-passant target square
- [x] Parse halfmove clock
- [x] Parse fullmove number
- [x] Validate invalid piece characters and malformed board ranks
- [x] Validate invalid side-to-move field
- [x] Validate castling rights and king/rook placement
- [x] Validate en-passant square and pawn placement
- [x] Validate move counters
- [x] Reject extra FEN fields
- [x] Validate king counts and pawn placement on terminal ranks
- [x] Preserve the previous position when FEN loading fails
- [x] Serialize FEN and test round trips

### Build and Testing

- [x] CMake build configured
- [x] GoogleTest integrated
- [x] CTest configured
- [x] Initial test infrastructure verified
- [x] 81/81 tests passing locally
- [x] 81/81 tests passing in GitHub Actions (author-reported)

## Next Milestone: Legal Move Generation and Perft

The next engine milestone is implementing pseudo-legal and legal move generation, followed by special moves, make/unmake, and perft validation.

Correctness will be established using reference perft positions and node counts before moving on to search optimization.

For the full project objectives, roadmap, scope, and architectural decisions, see [PROJECT_VISION.md](PROJECT_VISION.md).