# ADR-0001: Modular Monolith & Repository Layout

- **Status:** Accepted (Phase 0 baseline)
- **Date:** 2026-11-12
- **References:** [Spec §12–13](../Buildwise_Product_Specification_v1.md#L708-L777), [Workspace Rules](../../.agent/rules/buildwise.md), Phase 0 Owner Decisions #2, #3, #8, #9, #10

## Context

Spec §12 depicts both `apps/api/` and top-level `services/*` folders while Spec §12–13 and the workspace rules mandate starting as a modular monolith without microservices or Kubernetes. Spec §12 and Runbook §5 Phase 0 also place security and ADR files in slightly different paths (`docs/ADR/` vs. `decisions/`, `docs/SECURITY_MODEL.md` vs. `SECURITY.md`).

## Accepted Decisions

1. **Modular monolith inside `apps/api/`:**
   - All backend domain modules (`core`, `auth`, `policy_engine`, `projects`, `retrieval`, `model_gateway`, `agent_orchestrator`, `test_runner`) reside as Python packages inside `apps/api/app/` rather than top-level deployable `services/*` directories.
   - Frontend lives in `apps/web/` (React + TypeScript); shared contracts/profiles/UI/observability live in `packages/`.
   - Phase 6 local connector (`apps/workspace-connector/`) remains an isolated empty placeholder until Phase 6.
2. **ADR & Governance Placement:**
   - All Architecture Decision Records reside in `docs/ADR/`.
   - `.agent/rules/buildwise.md` is the canonical workspace rules file, tracked in Git.
   - Root `STATUS.md` serves as the honest implementation ledger using the Runbook §3 status vocabulary.
3. **Consistent Security Documentation Hierarchy:**
   - `SECURITY.md` (repository root): top-level security posture, default-OFF feature flags, and index.
   - `docs/SECURITY_MODEL.md`: security and authorization architecture specification aligned with R1 review boundaries.
   - `docs/threat-model/README.md`: threat model (Phase 0 stub; full adversarial verification in Phase 7).
4. **Phase 5 vs. Phase 6 Architectural Separation:**
   - Phase 5 review findings, proposal records, and the approval state machine live cleanly separated from Phase 6 patch application, workspace connector access, and sandbox execution, which stay disabled behind feature flags (`OFF` by default).

## Consequences

- Keeps V1.0 local setup, testing, and security auditing simple without premature distributed-service overhead.
- Allows extracting modules into separate processes later only if operational needs justify it, without rewriting core contracts.
