# Buildwise Workspace Rules

## Source of truth
- `docs/Buildwise_Product_Specification_v1.md` is the authoritative product and architecture baseline.
- `docs/Buildwise_V1_Antigravity_Build_Runbook.md` governs implementation order, safety, testing and owner review.
- Never silently change approved architecture, scope, interfaces, dependencies or milestones.

## Implementation workflow
- Work on one phase at a time.
- Present the implementation plan and wait for explicit owner approval before coding.
- Do not skip phases or acceptance criteria.
- Use a modular monolith. Do not introduce microservices or Kubernetes.

## ShatranjOne isolation
- Never access, mount or modify the real ShatranjOne working repository.
- Use only a disposable fixture clone for connector, patch and sandbox tests.
- Buildwise must remain independent of ShatranjOne.

## Security
- Never expose API keys, passwords, tokens or other secrets in source code, logs, prompts or commits.
- Keep source modification, execution and enterprise capabilities disabled until their specified security gates and owner reviews pass.
- Enforce authorization in the backend. Agent prompts are not security boundaries.
- Request approval for sensitive terminal commands and file operations.

## Testing and verification
- Write tests and run relevant checks.
- Report actual commands, outputs, failures and unrun tests honestly.
- Never mark work VERIFIED without required owner review and evidence.
- Never weaken owner-authored security or evaluation tests.

## Decisions
- If an architectural decision is unresolved, present options and trade-offs, then stop for owner approval.
- Do not introduce unapproved dependencies or redesign existing decisions.
- The seven-day schedule is an implementation target, not permission to skip testing or security reviews.