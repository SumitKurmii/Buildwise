# Contributing to Buildwise

Buildwise follows the phased workflow and AI development policy (**A7**) defined in [docs/Buildwise_V1_Antigravity_Build_Runbook.md](docs/Buildwise_V1_Antigravity_Build_Runbook.md) and [.agent/rules/buildwise.md](.agent/rules/buildwise.md).

## Core Rules

1. **One phase at a time:** Do not start a later phase until the current phase's deliverables, tests, and owner review are accepted.
2. **Plan before coding:** Submit a short phase plan (files, data changes, tests, risks) and wait for explicit owner approval before editing code.
3. **No silent architecture changes:** Never alter approved specification scope, interfaces, milestones, or dependencies without a written ADR proposal and owner approval.
4. **Modular monolith only:** Keep backend modules inside `apps/api/`. Do not introduce microservices or Kubernetes in V1.0.
5. **Shatranj One isolation:** Never access, mount, modify, or push to the real Shatranj One repository. Use only owner-supplied read-only copies or disposable fixture clones. Shatranj One's only authoritative docs are `PROJECT_VISION.md` and `README.md`; never create or use `PLAN.md`.
6. **Tests & contracts first:** Owner-authored security tests, RAG evaluation sets, and prompt-injection cases are the contract and must never be weakened or edited by an agent. For critical security controls, verify negative (break) tests fail as expected.
7. **Owner review & understanding gates (A7):** Security-critical (**R1**) code must be read line by line and understood by the owner before it is trusted or enabled; non-R1 code is reviewed/understood within the same week. AI review of AI-written code alone never suffices for `VERIFIED`.
8. **Honest status reporting:** Record items in [STATUS.md](STATUS.md) using only `NOT STARTED`, `GENERATED`, `TESTED`, `VERIFIED`, `PROVISIONAL`, or `BLOCKED`. Never mark an item `VERIFIED` without required tests and owner review.

## Commit & Dependency Discipline

- **Small commits:** Keep one logical change per commit.
- **Commit message format:** Reference the phase and specification section, and mark AI-assisted commits (for example: `Phase 1 (Spec §6.1): add initial user and tenant tables [AI-assisted]`).
- **Terminal & workspace safety:** Run terminal commands under the most restrictive approval mode available and keep production credentials out of the workspace.
- **Dependencies:** Verify every new dependency exists, is maintained, and is strictly needed; pin exact versions and record additions.
- **Secrets:** Never commit `.env` files, API keys, credentials, or tokens. Keep `EXPLABS_API_KEY` server-side only.
- **CI checks (from Phase 1 onward):** Lint, type check, unit and integration tests against real PostgreSQL, up/down migration check, secret scan, and dependency audit.
