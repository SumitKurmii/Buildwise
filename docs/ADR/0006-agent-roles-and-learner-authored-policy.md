# ADR-0006: Agent Roles, `learner-authored` Policy & Phase 5/6 Separation

- **Status:** Accepted (Phase 0 baseline)
- **Date:** 2026-11-12
- **References:** [Spec §3](../Buildwise_Product_Specification_v1.md#L67-L150), [Spec §7.4 & §8](../Buildwise_Product_Specification_v1.md#L487-L579), [Runbook §4, Phase 4–6, Appendix A](../Buildwise_V1_Antigravity_Build_Runbook.md#L195-L243), Phase 0 Owner Decisions #4, #10

## Accepted Decisions

1. **Three Operating Modes, One Enforced Backend Policy (Spec §3 & §7.4):**
   - **Teacher:** Read-only project/file/test inspection; citations required; proposes snippets or patches **in chat only**; never modifies files or tasks.
   - **Progress Mentor:** Read-only source inspection; creates/updates tasks, milestones, and progress notes in PostgreSQL only; cannot mark `Verified` without linked evidence; proposes doc updates only as review-queue diffs (per A3); never modifies source files.
   - **Supervisor:** In Phase 4/5, inspects implementations, writes **review notes only** (produces prioritized findings: `Approved`, `Approved with follow-ups`, `Changes requested`, `Unable to verify`), may **propose tasks only** (never overwrites Mentor progress status), and escalates any finding contradicting pinned decisions in `PROJECT_VISION.md` or the project charter to the owner rather than applying it. In general projects, machine-applicable diff proposals with file-hash verification, patch application, and allowlisted sandboxed checks belong to Phase 6 (OFF by default).
   - **Common role denials:** None of the three roles may commit, push, deploy, change access policies, or read secrets (`git push` is excluded from Buildwise V1.0).
   - **Enforcement rule:** System prompts never grant permissions. Effective capability is evaluated server-side per request and per tool call as `human permission ∩ agent capability ∩ tenant/project policy`.
2. **Shatranj One `learner-authored` Project-Specific Policy Override (A4, Owner Decision #4):**
   - Shatranj One operates in **`learner-authored` mode** governed by its project charter ([CHARTER.md](../projects/shatranj-one/CHARTER.md)).
   - **Enforced as a project-specific policy (not just global feature flags):** Machine-applicable patch proposals remain **disabled by default for Shatranj One**, including when general Phase 6 capabilities (`ENABLE_WORKSPACE_WRITES`, connector, and sandbox flags) are eventually implemented or enabled for other projects. Even if global Phase 6 feature flags are later turned on, Shatranj One's project-level policy still blocks machine-applicable patch proposals and patch application by default.
   - **Supervisor:** May produce structured review findings and descriptive suggested changes (human-readable guidance), and these must not become executable or machine-applicable patches.
   - **Teacher:** Enforces hints-first pedagogy (*hint → small example → partial code*), providing full code only if the owner explicitly asks after attempting.
   - **Change control:** Any future relaxation or exception to this restriction requires explicit owner approval (and patch-specific approval where applicable) and must respect the Shatranj One project charter.
3. **Strict Phase 4/5 vs. Phase 6 Boundary (Owner Decision #10):**
   - **Phase 4** delivers role profiles, server-side tool capability intersection, and run traces.
   - **Phase 5** owns milestones, tasks, dependencies, evidence links (with A2 OS/compiler/command/exit-code/SHA metadata), Supervisor review records, pinned-decision conflict escalation, and the **approval state machine** (rejecting missing, expired, revoked, or mismatched approvals).
   - **Phase 6** owns hash-verified diff proposals, file-hash-verified patch application, scoped workspace connectors, and Docker sandbox command execution. All Phase 6 paths stay behind global feature flags (`ENABLE_WORKSPACE_WRITES=false`, `ENABLE_TEST_SANDBOX=false`, `ENABLE_LOCAL_CONNECTOR=false`) until Phase 6 tests and R1 owner reviews pass, and remain further constrained by project-specific policies such as Shatranj One's A4 `learner-authored` restriction.

## Consequences

- Prevents Phase 4/5 work from prematurely introducing file-write or shell-execution attack surface.
- Guarantees Shatranj One's `learner-authored` educational integrity is enforced in profile/policy configuration rather than relying solely on conversational reminders.
