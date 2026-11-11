# Buildwise Security Baseline

**Current implementation state:** Phase 0 documentation baseline only. No runtime security controls, authentication, authorization, retrieval filters, or sandboxes are implemented yet.

## Security Documentation Hierarchy

1. **`SECURITY.md` (this file):** Top-level security posture, default-deny feature flags, secret-handling rules, and review gates.
2. **[docs/SECURITY_MODEL.md](docs/SECURITY_MODEL.md):** Detailed security and authorization architecture specification aligned with Spec §7–8 and Runbook §7 (R1 owner review map).
3. **[docs/threat-model/README.md](docs/threat-model/README.md):** Threat model (Phase 0 stub; expanded and tested with owner-authored adversarial cases in Phase 7).

## Mandatory Safety Posture (From Day 0)

- **Default deny:** Backend policy checks govern every request and every tool call. Agent role prompts are not security boundaries.
- **Feature flags OFF by default:** Dangerous capabilities (Phase 6 file modifications, local workspace connector, sandboxed test execution, and Phase 8 enterprise/organization features) must remain disabled behind default-`false` flags until all prerequisite security tests pass and the owner has completed line-by-line **R1** code review.
- **Phase 5 vs. Phase 6 separation:** Phase 5 review findings, proposal records, and the approval state machine are strictly separate from Phase 6 patch application and sandbox execution.
- **Secret protection:** Provider keys (`EXPLABS_API_KEY`) and session/signing secrets stay server-side in environment or secret-manager configuration, never in frontend bundles, database plaintext, prompts, logs, or Git commits.
- **Shatranj One isolation:** Buildwise never mounts, reads, writes, or pushes to the real Shatranj One working tree.
- **Reporting & review rule:** Security-critical (**R1**) modules stay `GENERATED` or `TESTED`—never `VERIFIED`—until line-by-line owner review passes.
