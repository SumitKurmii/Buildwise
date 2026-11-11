# Buildwise Threat Model (Phase 0 Stub)

**Status:** Stub only (scheduled for full completion and adversarial verification in **Phase 7**).  
**Related documents:** [SECURITY.md](../../SECURITY.md) and [docs/SECURITY_MODEL.md](../SECURITY_MODEL.md).

## 1. Protected Assets

- Tenant-isolated project data, documents, vector chunks, and conversations.
- Server-side provider API keys (`EXPLABS_API_KEY`), session/signing secrets, and external credentials.
- Host filesystem and the real **Shatranj One** working repository (must remain unreachable from Buildwise).
- Task/evidence integrity and owner approval state transitions.
- Model budget and token quotas.

## 2. Trust Boundaries

1. **Browser client ↔ FastAPI backend:** Client requests, tenant IDs, and selected agent roles are untrusted; identity comes strictly from verified session state.
2. **Backend ↔ PostgreSQL / `pgvector`:** Tenant and ACL predicates must apply before ranking and retrieval.
3. **Trusted system policy ↔ Retrieved content / model output / tool arguments:** All ingested files, repository text, and LLM-supplied tool arguments are untrusted.
4. **Buildwise backend ↔ Local workspace connector / Docker sandbox (Phase 6):** Root path validation, hash matching, non-root execution, no host secrets, and restricted network isolation.
5. **Buildwise workspace ↔ Real Shatranj One repository:** Hard organizational and operational isolation boundary (fixture clones and approved read-only copies only).

## 3. Initial Threat Categories (To Expand & Test in Phases 1–7)

| ID | Threat | Target Phase & Mitigation |
|---|---|---|
| T1 | Cross-tenant data or vector chunk leakage | Phase 1 central authz + scoping tests; Phase 2 pre-ranking ACL query filter; optional PostgreSQL RLS (ADR-0002) |
| T2 | Privilege escalation via AI role selection or prompt injection | Phase 4 server-side capability intersection; Phase 7 owner-authored 10+ prompt-injection test corpus |
| T3 | Secret leakage in browser bundle, responses, DB, or logs | Phase 1/3 server-side secret refs, `.env` exclusion, log redaction tests |
| T4 | Unauthorized or stale file modification | Phase 5 patch-specific expiring approval state machine; Phase 6 target file-hash verification + feature flag `OFF` by default |
| T5 | Path traversal, symlink escape, or host access via connector/sandbox | Phase 6 root canonicalization, symlink/out-of-root negative tests, non-root Docker sandbox, A5 dependency gate |
| T6 | Unbounded LLM cost or silent evidence fabrication | Phase 3 token/budget hard stops + fake provider mode; Phase 5 evidence-linked status rules |

## 4. Phase 7 Verification Requirements (Pending)

- Full threat model review by owner (**R1**).
- Execution of the owner-authored prompt-injection corpus (minimum 10 cases attempting cross-project reads, role elevation, blocked tool calls, secret exfiltration, and self-approval).
- Deliberate break-tests confirming critical authorization and isolation controls fail closed when disabled or misconfigured.
