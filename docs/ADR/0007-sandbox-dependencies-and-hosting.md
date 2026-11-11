# ADR-0007: Sandbox Dependency Strategy (A5 Gate) & Hosting Topology

- **Status:** **Deferred Gate (A5 mandatory before Phase 6) & Pending Owner Decision (Hosting before Phase 7)**
- **Date:** 2026-11-12
- **References:** [Spec §8.3](../Buildwise_Product_Specification_v1.md#L569-L579), [Spec §21](../Buildwise_Product_Specification_v1.md#L1068), [Runbook §1.1, A5, Phase 6–7](../Buildwise_V1_Antigravity_Build_Runbook.md#L36-L48)

---

## Accepted Sandbox Baseline (Spec §8.3 & Runbook Phase 6)

- **Feature Flag Default:** `ENABLE_TEST_SANDBOX` remains `false` (`OFF` by default) until all Phase 6 isolation tests pass and the owner completes **R1** code review.
- **Container & Execution Invariants:**
  - Runs as a **non-root user** inside a disposable Docker container.
  - **Read-only project mount** except for an isolated scratch directory.
  - **CPU, memory, and execution time limits** enforced per run.
  - **Allowlisted build/test commands only** (no arbitrary shell access and destructive commands blocked).
  - **No outbound network by default** and **no access to host secrets, home directories, SSH keys, or cloud credentials**.

---

## Unresolved Decision 15b (A5 Gate): Sandbox Dependency Policy *(Mandatory Gate Before Phase 6)*

Spec §8.3 and Runbook Phase 6 forbid outbound network access inside the disposable Docker test sandbox by default. However, Runbook §1.1 notes that Shatranj One's CMake build fetches **GoogleTest** at configure time. Under accepted amendment **A5**, Phase 6 **cannot start** until a written decision resolves this conflict.

### Option A — Pre-vendored or pre-cached dependencies baked into the sandbox image / fixture
- **How it works:** GoogleTest (at pinned version/hash) is pre-installed in the Docker test runner image or vendored in a read-only dependency cache volume; CMake runs offline (`FETCHCONTENT_FULLY_DISCONNECTED` or local `find_package`).
- **Pros:**
  - Keeps `--network=none` strictly enforced in the test sandbox at all times.
  - Faster, reproducible, offline test runs unaffected by external GitHub/network availability.
- **Cons / Trade-offs:**
  - Requires keeping the sandbox image/cache synchronized with Shatranj One's declared GoogleTest version and passing CMake cache overrides cleanly without altering Shatranj One's real repo.

### Option B — Separate short-lived, allowlisted configure/fetch stage before offline test execution
- **How it works:** An isolated pre-build/dependency-cache step fetches only pinned allowlisted hosts/hashes into a cache volume, after which compile and test execution run in a network-disabled container.
- **Pros:**
  - Works with unmodified upstream `FetchContent` configurations if dependencies change.
- **Cons / Trade-offs:**
  - Introduces limited outbound network surface and domain/proxy allowlist maintenance, increasing R1 review complexity.

---

## Unresolved Decision 16: Hosting Topology *(Needed by Phase 7)*

### Option A — Local Docker Compose for V1.0; cloud deployment decided later
- **How it works:** Run React, FastAPI, PostgreSQL (`pgvector`), and (when enabled in Phase 6) the sandbox runner locally via Docker Compose.
- **Pros:**
  - Zero cloud hosting cost, zero internet exposure of personal workspace or Shatranj One snapshots during the initial build, and aligns with Spec §14 local dev p95 assumptions.
- **Cons / Trade-offs:**
  - Accessible only on the owner's machine; external OIDC/GitHub webhooks (Phase 8+) would later need a hosted URL.

### Option B — Hosted cloud/VPS deployment in Phase 7
- **How it works:** Provision a managed PostgreSQL + container host (e.g., single VPS or managed PaaS) with TLS, managed secrets, and firewall rules.
- **Pros:**
  - Accessible remotely; enables real OAuth/GitHub webhook callbacks later.
- **Cons / Trade-offs:**
  - Adds infrastructure provisioning, TLS/domain setup, remote secret management, and public-exposure security burden before personal workflows stabilize.

### Decision Status
- **A5 Sandbox Dependency Policy:** `Mandatory Decision Gate before Phase 6` (Phase 6 must not begin until approved).
- **Decision 16 (Hosting Topology):** `Pending Owner Decision` (needed by Phase 7).
