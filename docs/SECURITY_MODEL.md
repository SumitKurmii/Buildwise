# Buildwise Security Model (Phase 0 Baseline)

**Implementation status:** Not yet implemented (Phase 0 baseline specification only).  
**Authoritative sources:** [Buildwise Product Specification §7–8](Buildwise_Product_Specification_v1.md#L450-L579), [Antigravity Build Runbook §4 & §7](Buildwise_V1_Antigravity_Build_Runbook.md#L113), and [SECURITY.md](../SECURITY.md).

## 1. Core Security Principles

1. **Three-part permission evaluation:** Effective permission is always the intersection of **human permission**, **agent role capability**, and **tenant/project policy**. Agent persona or prompt instructions never grant access.
2. **Deny by default:** Every endpoint, query, retrieval call, and tool invocation must evaluate authorization explicitly. Explicit denials override broad grants; missing tenant or project context fails closed.
3. **Server-derived identity & tenant scope:** Tenant and user identities are derived solely from the authenticated session, never from client-supplied tenant IDs.
4. **Pre-ranking retrieval filtering:** Tenant, project, and ACL filters must execute inside the retrieval query **before** vector/lexical ranking, never as post-filtering.
5. **Untrusted content isolation:** Repository files, uploaded documents, logs, and tool outputs are untrusted data and must be separated and delimited from trusted system instructions and tool definitions.
6. **Tool call re-authorization:** Every tool invocation is an independent security decision validated against strict schemas, allowlists, timeouts, output limits, and risk classifications.
7. **Phase 5 vs. Phase 6 separation:** Phase 5 review findings, proposal records, and the approval state machine are isolated from Phase 6 patch application, workspace connectors, and sandbox execution. Write-capable and execution paths stay behind feature flags (`OFF` by default).

## 2. Role Architecture (Schema vs. V1.0 Personal Mode)

- **Schema system roles (preserved per Spec §6.2):** `Owner`, `Admin`, `Project Lead`, `Collaborator`, `Viewer`.
- **V1.0 personal workspace roles (Spec §7.3):** `Workspace Owner`, `Project Lead`, `Collaborator`, `Viewer`.

## 3. R1 Security-Critical Components (Owner Line-by-Line Review Required)

Per Runbook §7, the following 11 components cannot be marked `VERIFIED` or enabled until tested and read line-by-line by the owner:

1. Authorization function and tenant/project scoping layer (Phase 1 — **blocking prerequisite**).
2. Authentication, password hashing (if local auth is selected), session, and CSRF handling (Phase 1).
3. Retrieval query and pre-ranking ACL filters (Phase 2 — **blocking prerequisite**).
4. Secret and configuration handling; log redaction (Phase 3).
5. Tool permission enforcement (`capability ∩ permission`) (Phase 4 — **blocking prerequisite**).
6. Approval state machine and verification check before every write path (Phase 5 — **blocking prerequisite**).
7. Patch application and file-hash verification (Phase 6 — **blocking prerequisite**, flag `OFF`).
8. Connector root, path traversal, and symlink enforcement (Phase 6 — **blocking prerequisite**, flag `OFF`).
9. Sandbox configuration and command allowlist (Phase 6 — **blocking prerequisite**, flag `OFF`).
10. Prompt assembly separating trusted instructions from untrusted retrieved content (Phase 4/7).
11. Organization roles and sensitive-data enforcement (Phase 8 — flag `OFF`).

## 4. Related Security Artifacts

- Top-level policy & status entry: [SECURITY.md](../SECURITY.md)
- Threat model stub: [docs/threat-model/README.md](threat-model/README.md)
- Auth, RLS, connector, and sandbox decision records: [docs/ADR/README.md](ADR/README.md)
