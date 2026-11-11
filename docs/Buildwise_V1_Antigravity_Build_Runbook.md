# Buildwise V1.0 — Antigravity Build Runbook

Purpose: run the complete V1.0 specification through Antigravity in about seven days, with honest status reporting, protected security-sensitive components, and a clear map of what the owner must personally review.

This runbook does not change the specification. Anything that changes or adds to a decision in the specification is marked **PROPOSAL** and needs the owner's approval before it is used.

---

## 0. Principles

1. The specification (Buildwise V1.0) is the baseline. Changes go through a written decision record, never silently.
2. Phases are ordered dependencies (the specification says so). The agent works one phase at a time.
3. Generated is not tested, tested is not verified. Every item carries one honest status (section 3).
4. Anything dangerous is OFF by default behind a feature flag until its security tests pass and the owner has reviewed it.
5. Shatranj One is independent. Buildwise never touches the real Shatranj One working tree.
6. The owner's limited review time goes to the security-sensitive components first (section 7).
7. Agents never grade their own homework on security: the authorization test matrix and a set of prompt-injection cases are written or approved by the owner.

---

## 1. Pre-flight (before the build starts)

### 1.1 Decisions the agent must not make silently

The specification defers these (section 21). Decide them now, or tell the agent to propose options and stop.

| Decision | Options to consider | Needed by |
|---|---|---|
| Authentication for the week | A proven auth framework with local accounts and secure session cookies; external OIDC/SSO later in Phase 8 (PROPOSAL) | Phase 1 |
| Session model | Server-side session cookie with CSRF protection, or short-lived tokens with refresh | Phase 1 |
| Row-Level Security in PostgreSQL | Use it as a second layer, or application-level scoping only (the specification says where practical) | Phase 1 |
| Embedding model and reranker | Provider embeddings if offered, otherwise a local model; must be tested for quality and cost | Phase 2 |
| Shatranj One ingestion in the first run | Controlled upload or read-only import of a pinned commit, not the local connector (PROPOSAL) | Phase 2 |
| Background jobs | In-process worker for V1.0, queue later | Phase 2 |
| Local connector form | Standalone companion process or VS Code extension | Phase 6 |
| Sandbox dependency policy | Pre-vendored or cached dependencies, or an allowlisted fetch step (Shatranj One's build downloads GoogleTest at configure time, and the specification forbids network by default) | Phase 6 |
| Small model for low-complexity routing | Provider's small model, or none in V1.0 | Phase 3 |
| Hosting | Local Docker Compose for the week; deployment decided later | Phase 7 |

### 1.2 Amendments (decided by the owner)

| Amendment | Decision | How it applies in this build |
|---|---|---|
| A1 Commit-pinned snapshots | Accepted | Every imported snapshot records a commit SHA and a dirty-working-tree flag. Without GitHub integration, the SHA comes from the fixture clone's git metadata (read-only) or is entered by the owner; the decision record states which. |
| A2 Environment-specific evidence | Accepted; GitHub integration later | Evidence records carry OS, compiler, command, exit code, summary and commit SHA. Until the integration exists, CI results are owner-entered evidence labelled as such, never inferred. |
| A3 Source-of-truth contract | Accepted | Repository documents are authoritative for Shatranj One scope, gates and acceptance criteria. The database mirrors them read-only with a last-synced commit and drift detection. Mentor proposes diffs only. |
| A4 Project-specific policies | Accepted | An owner-authored project charter (Appendix A) with pinned decisions and "learner-authored" mode for Shatranj One. Agents cannot edit it. |
| A5 Sandbox dependency strategy | Deferred to Phase 6 | A written decision record is a gate before Phase 6 starts. Phase 6 does not begin without it. |
| A6 Fake provider and no-LLM mode | Accepted | Fake provider for all automated tests; non-AI features must work with the model switched off; token budget includes evaluation runs. |
| A7 AI development policy | Accepted | Section 6.1 of this runbook is the policy. |

Accepted amendments are part of the baseline. Changing any of them needs a new written proposal.

### 1.3 Environment and safety setup

- Create a **separate repository** for Buildwise. The Antigravity workspace contains only that repository.
- Never open the real Shatranj One working tree in the Antigravity workspace. Create a **disposable fixture clone** of Shatranj One (a plain copy at a pinned commit) for connector, patch and sandbox tests.
- No git push capability for Buildwise in V1.0. It may propose diffs; it never pushes to any remote.
- Secrets: the provider key lives only in the server environment file on your machine, excluded from version control. Provide an example file with placeholders. The agent must never print, log or commit the key.
- Use the most restrictive command-approval mode your Antigravity version offers (check its documentation). Review shell commands that install packages or touch files outside the repository.
- Token budget: find out whether Antigravity's model usage and your provider allowance share one quota, and what the limits are. Set a hard cap for real provider calls during development; all tests use the fake provider unless marked as a manual integration test.
- Write the pass/fail **authorization test matrix** yourself before Phase 1 (section 5, Phase 1).

---

## 2. Global agent preamble (paste as project rules, or at the start of every phase)

```
You are building Buildwise V1.0 exactly as described in the specification file in this repository.

Rules:
1. Work on one phase at a time, in order. Do not start a later phase until the current phase's report is accepted by the owner.
2. Do not change the specification, approved decisions or the decision records without a written proposal and owner approval. Amendments A1-A4, A6 and A7 are accepted baseline; A5 is a decision gate before Phase 6.
3. Before coding a phase, produce a short plan (files, data changes, tests, risks) and wait for approval.
4. Write tests before or with the implementation. Authorization and security tests listed by the owner are the contract: make the code pass them; do not edit or weaken them.
5. Run the real tests and show the real command output. Never claim a test passed without output. If something could not be run, say so.
6. Use these statuses only: NOT STARTED, GENERATED, TESTED, VERIFIED, PROVISIONAL, BLOCKED. VERIFIED requires owner review. Record every item in STATUS.md with evidence.
7. Default deny. Dangerous capabilities (file writes, terminal execution, organization features) stay behind feature flags that are OFF until the owner enables them.
8. Never read, modify or commit anything outside this repository. Never access the real Shatranj One working tree; use the fixture clone.
9. Never print, log, commit or hard-code secrets. Provider keys stay server-side.
10. If a test fails after three fix attempts, mark the item BLOCKED with the evidence and continue only with work that does not depend on it. Do not loop.
11. Stop and ask when a decision is needed, when the token budget threshold is reached, or when the specification is ambiguous.
12. At the end of each phase, report: files changed, what was implemented, how to run it, tests run with real output, exit-criteria checklist, known issues, and anything unverified.
```

---

## 3. Status vocabulary and ledger

| Status | Meaning |
|---|---|
| NOT STARTED | Nothing exists |
| GENERATED | Code exists, not run or tests missing |
| TESTED | Automated tests pass, with the command output saved |
| VERIFIED | Tested, owner has reviewed the code or evidence, and CI is green on that commit |
| PROVISIONAL | Built on top of a failing or unverified earlier item |
| BLOCKED | Cannot progress; reason and evidence recorded |

STATUS.md holds one row per deliverable and exit criterion: phase, item, status, evidence (command and result, commit), owner review date, known issues. At the end of the week it becomes the honest implementation report.

---

## 4. Phase loop (repeat for every phase)

1. **Plan:** the agent submits the plan. The owner approves or edits (10 minutes).
2. **Contracts first:** security tests authored by the owner, plus agent-written tests for functionality.
3. **Implement** in small commits.
4. **Run everything:** the full suite, lint, type checks, migration check, secret scan and dependency audit. Real output.
5. **Exit-criteria table:** each criterion marked with its status and evidence.
6. **Owner checkpoint:** review the R1 components for the phase (section 7), run the app, try to break one rule.
7. **Tag** the commit for the phase, update STATUS.md, then move on.

Continue-with-flags rule (to keep going without hiding problems): a later phase may be built on a failing earlier phase only if everything in it is marked PROVISIONAL, **except** these security prerequisites, which block dependent work: tenant and project scoping (Phase 1), authorization before retrieval (Phase 2), tool permission enforcement (Phase 4), the approval state machine and patch-specific approval checks (Phase 5), and connector root enforcement, file-hash verification and sandbox isolation (Phase 6). Phase 5 approval controls must be implemented and tested before any write-capable path is introduced. Phase 6 hash verification and sandbox controls must be implemented and tested before patch application or command execution is enabled. Write-capable features stay disabled until all applicable prerequisites are TESTED and owner-reviewed.

---

## 5. Phase packets

### Phase 0 — Product and project baseline

**Instructions to the agent**
- Create the repository skeleton from the specification's structure section, STATUS.md, a decisions folder, and SECURITY.md (threat model stub).
- Write decision records for each decision in section 1.1 using the owner's choices. For undecided items, list options and trade-offs and stop.
- Create the Shatranj One project profile: name, repository reference, documentation sources. The documentation sources are exactly two files, PROJECT_VISION.md and README.md. There is no PLAN.md and none must be created.
- Import or confirm the roadmap, gates and acceptance criteria from PROJECT_VISION.md as read-only reference text.

**Acceptance criteria**
- Decision records exist for data model, authentication, connector and provider integration.
- The owner can explain the first-release scope and what is deferred.
- Shatranj One sources identified and recorded.

**Owner review:** read each decision record; approve or change.

### Phase 1 — Application foundation

**Instructions**
- React shell, FastAPI backend, PostgreSQL schema with migrations, authentication, personal tenant creation, project creation, membership and roles, basic audit events.
- Implement one central authorization function with deny-by-default. Every data access goes through tenant and project scoping.
- Implement the owner's authorization test matrix first; do not edit it.

**Owner-authored test matrix (minimum)**
- Owner can read and update their own project.
- A user with no membership is denied (not found or forbidden) for every endpoint.
- Guessing another tenant's project or resource identifier is denied and audited.
- A viewer cannot write.
- A disabled or removed membership is denied.
- Missing tenant context fails closed.
- Every endpoint has at least one allow and one deny test.

**Acceptance criteria**
- Owner can sign in, open a workspace, and create and view the Shatranj One project.
- Tenant and project scoping covered by automated tests that pass.
- Audit events recorded for allow and deny decisions, with correlation identifiers.
- If the selected authentication framework manages local passwords, they are stored only as strong hashes. If a trusted external identity provider manages authentication, the application stores the provider subject and required session data rather than duplicating passwords. Session cookies have secure attributes and CSRF protection is active.
- Logs contain no secrets or tokens (test).
- CI runs lint, type check, integration tests against a real PostgreSQL, migration check, secret scan, dependency audit.

**Owner review (R1):** authorization function, tenant scoping layer, authentication and session code.

### Phase 2 — Project knowledge and file access

**Instructions**
- Resource registry, controlled upload or read-only import of a pinned Shatranj One commit (from the fixture), document parser, chunking with file and line locators, embeddings, pgvector retrieval, citations.
- Apply tenant, project and ACL filters **inside the retrieval query before ranking**, never after.
- Abstain when the retrieved evidence is insufficient.
- Record commit SHA and a dirty flag with each snapshot (A1, accepted).

**Evaluation contract (owner-written):** about 30 questions about Shatranj One's documents and code, each with the expected source file and location, plus 5 questions whose answers are not in the sources (the system must abstain). The agent may not edit the answers.

**Acceptance criteria**
- Known questions answered with correct citations; report recall and citation correctness with the real numbers.
- Abstains on unanswerable questions.
- Unauthorized retrieval tests pass, including that other tenants' and other projects' chunks never appear in the candidate set.
- Ingestion treats document text as untrusted data.

**Owner review (R1):** the retrieval query and its filters; the ingestion path that decides which resource belongs to which project.

### Phase 3 — Chat and model gateway

**Instructions**
- Streaming chat with conversation history, OpenAI-compatible provider adapter for the Experiential Labs API, server-side key handling, model allowlist and router, usage and budget tracking.
- Provider behind an interface. Model identifiers and endpoints are configuration, not constants.
- Fake provider for all automated tests. The system must run in a no-LLM mode where non-AI features work.
- Verify streaming and tool-calling support against the provider with a small manual integration test.

**Acceptance criteria**
- A real Fable 5.2 call succeeds through the backend (manual integration test, token-capped).
- The provider key never appears in browser bundles, responses or logs (tests).
- Usage, errors and latency are recorded per call.
- Routing between small and large models is testable and visible to the user.
- Budget limit stops calls when exceeded (test with the fake provider).

**Owner review (R1):** secret and configuration handling, log redaction. **R2:** gateway and budget logic.

### Phase 4 — Three role modes

**Instructions**
- Teacher (read-only), Progress Mentor (task and progress capabilities), Supervisor (review workflow); role selector; agent run history.
- Enforce tool permissions on the server as the intersection of the agent's capabilities and the user's permissions. Prompt text is not enforcement.
- Apply the project's charter and pinned decisions to every run. For Shatranj One, "learner-authored" mode disables machine-applicable patch proposals by default and makes hints-first the default teaching behavior (A4, accepted). The Supervisor may still produce review findings and describe suggested changes; those descriptions are not executable or applicable patch proposals.

**Acceptance criteria**
- Each role produces distinct, structured outputs and invokes only permitted tools.
- Attempts to call a forbidden tool are rejected server-side regardless of the prompt (tests per role).
- Role choice cannot raise the user's permissions (test).
- Agent runs are recorded with tool invocations.

**Owner review (R1):** the tool permission matrix and its enforcement point; how the system prompt separates trusted instructions from retrieved content.

### Phase 5 — Progress tracking and technical review

**Instructions**
- Milestones, tasks, dependencies, acceptance criteria; evidence links to files, commits and test runs (with environment and commit SHA, per A2); Supervisor review records; owner approval and rejection interface.
- Mirror Shatranj One's gates and acceptance criteria from PROJECT_VISION.md read-only, with drift detection and the last synced commit (A3, accepted). Mentor may propose a document change only as a diff in the review queue; it never edits the two documents.
- CI results are owner-entered evidence, labelled as reported, until the GitHub integration exists (A2). Local test results are labelled with their environment; do not treat a Windows result as proof for Linux CI.
- Reference data such as perft counts lives in the repository's test data with a cited source; Supervisor checks against it, not model memory.

**Acceptance criteria**
- No status may be set to verified without linked evidence.
- Supervisor reviews a defined Shatranj One milestone and produces actionable findings.
- The approval state machine rejects missing, expired, revoked or mismatched approvals; every write path checks a valid, patch-specific approval (test). Actual patch application remains a Phase 6 capability and is separately gated by file-hash verification and sandbox controls.
- Decision conflicts (a finding that contradicts a pinned decision) are raised to the owner, never applied.

**Owner review (R1):** the approval state machine and the code that checks approval before any write path; evidence rules.

### Phase 6 — Controlled file modifications and execution (feature flag OFF by default)

**Gate before starting:** the A5 sandbox dependency decision record must be written and approved.

**Instructions**
- Diff proposal with file-hash verification; scoped local workspace connector (form per decision); approval-bound patch application; Docker test sandbox with allowlisted commands.
- Target only the fixture clone, never the real working tree.
- Sandbox: no outbound network, non-root user, read-only mount of the project except a scratch directory, CPU, memory and time limits, no access to host secrets. Resolve the dependency policy decision first.

**Acceptance criteria**
- The connector can read and write only inside the approved project root.
- Path traversal, symlink and out-of-root tests pass.
- An approved diff applies only if the file hash matches the expected version; a stale or modified file is rejected.
- Commands run in isolation with restricted privileges; non-allowlisted commands are rejected.
- With the flag OFF, no write or execution path is reachable (test).

**Owner review (R1):** path and root enforcement, hash verification and patch application, sandbox configuration and command allowlist. Do not enable the flag until reviewed.

### Phase 7 — Security hardening and evaluation

**Instructions**
- Threat model in SECURITY.md, prompt-injection corpus, tenant isolation and sensitive-data tests, audit viewer, usage dashboards, RAG and agent evaluation reports with limitations.
- The injection corpus must include cases written by the owner (at least 10): documents that instruct the agent to reveal other projects, change roles, call blocked tools, exfiltrate secrets, or approve its own patch.

**Acceptance criteria**
- Critical authorization tests pass.
- No known cross-tenant leakage in the suite.
- Untrusted documents cannot grant permissions or invoke blocked tools (tests).
- Tool permissions and approvals documented.
- Evaluation results and known limitations published with real numbers.

**Owner review (R1):** the injection corpus you wrote, the threat model, and the results of every critical test.

### Phase 8 — Enterprise enablement (feature flag OFF by default)

**Instructions**
- Organization administration and invitations, configurable roles, policy management, separate knowledge collections and budgets per tenant, optional MFA and SSO, enterprise audit export.
- Sensitive-data example (salary or HR) uses clearly labelled **synthetic data only**. Never real personal or salary data.

**Acceptance criteria**
- Two test tenants cannot access each other's projects, documents, embeddings, conversations or usage records.
- Salary and HR access is enforced before retrieval and every access is logged.
- Organization admins do not gain unrestricted access to sensitive business data without explicit authorization.

**Owner review (R1):** organization role model, separation of duties, sensitive-data enforcement. Keep the flag OFF until reviewed.

---

## 6. Cross-cutting requirements

- **CI on every commit:** lint, type check, unit and integration tests (real PostgreSQL), migration up and down, secret scan, dependency audit.
- **Dependencies:** pinned versions; verify each new package exists, is maintained and is needed. Record additions.
- **Negative tests:** for each critical control, break it deliberately and confirm the test fails.
- **Logging:** structured, with correlation identifiers; redaction test for secrets.
- **Documentation:** README, architecture diagrams, security model and evaluation report must describe only what is implemented.
- **Shatranj One protection:** the real working tree is never mounted; fixture only; no push capability; patch application disabled in learner-authored mode unless the owner explicitly requests and approves it.

### 6.1 AI development policy (A7, accepted)

- The owner writes the authorization test matrix, the evaluation questions and the prompt-injection cases that define security behavior. The agent makes the code pass them and does not edit them.
- Security-critical components (section 7, R1) are read line by line by the owner before they are trusted or enabled.
- One small change per commit. Each commit message names the phase and the specification section, and marks AI-assisted work.
- New dependencies are verified (exists, maintained, needed) and pinned; dependency audit and secret scanning run in CI.
- Terminal commands run under the most restrictive approval mode available; no production credentials in the workspace.
- For each critical control, break it deliberately and confirm that a test fails.
- AI review of AI-written code is not sufficient evidence by itself; owner review and independent tests are required for VERIFIED.
- Understanding gate by tier: security-critical code is understood before it is enabled; other code within the same week.

---

## 7. Owner review map

**R1 — read every line before the related feature is enabled or trusted**
1. Authorization function and tenant/project scoping layer.
2. Authentication, password hashing, session and CSRF handling.
3. Retrieval query and ACL filters (authorization before ranking).
4. Secret and configuration handling; log redaction.
5. Tool permission enforcement (capability intersect permission).
6. Approval state machine and the check before every write path.
7. Patch application and file-hash verification.
8. Connector root, path and symlink enforcement.
9. Sandbox configuration and command allowlist.
10. Prompt assembly (trusted instructions separated from untrusted content).
11. Organization roles and sensitive-data enforcement (Phase 8).

**R2 — read tests and the main path, skim the rest**
Model gateway and routing, budget and usage tracking, orchestrator run lifecycle, RAG pipeline, evidence and progress logic, audit logging, API validation.

**R3 — run it and observe; read later**
UI screens, dashboards, formatting, documentation generation.

Anything you have not reviewed stays at GENERATED or TESTED, never VERIFIED.

---

## 8. Seven-day schedule (agent time; owner checkpoints in brackets)

| Day | Agent work | Owner checkpoint |
|---|---|---|
| 1 | Pre-flight decisions recorded, Phase 0, Phase 1 | Approve plan; write test matrix; review R1 items 1-2 |
| 2 | Phase 2 | Write evaluation questions; review R1 item 3 |
| 3 | Phase 3 | Review R1 item 4; run the real integration call once |
| 4 | Phase 4 and Phase 5 | Review R1 items 5-6 |
| 5 | Phase 6 (flag OFF) | Review R1 items 7-9 |
| 6 | Phase 7 | Write injection cases; review threat model and results |
| 7 | Phase 8 (flag OFF), documentation, final status | Review R1 items 10-11; read the honest report |

If a day's phase is not finished, the agent reports the blockers and proceeds under the continue-with-flags rule. Stop early if the token budget threshold is reached. The seven-day schedule is an implementation target, not a verification deadline: no phase or feature may be marked VERIFIED merely to meet the schedule. Any security-sensitive capability whose required tests or owner review remain incomplete stays disabled.

---

## 9. End-of-week report (template)

- Phase table: each exit criterion with its status and evidence.
- Test summary: commands run and real results; tests that could not be run.
- Security: which R1 components you have reviewed, and which you have not.
- Known issues and BLOCKED items.
- Token and cost usage.
- What is safe to demo, and what must stay off.

---

## 10. Understanding plan (2-3 hours a week)

Reading order:
1. Trace one request from login through authorization to a database query and audit event.
2. Read the authorization function and its tests; change one rule yourself and add a test.
3. Read the retrieval query; explain why filtering before ranking matters.
4. Read the approval and hash flow; try to bypass it in a test.
5. Read the model gateway and how a prompt is assembled.

Each week, write a short ledger entry per module: what it does, its data flow, one thing you changed by hand. Rebuild one small piece from memory.

---

## 11. Resume gate

Do not list Buildwise as a finished project until:
1. It runs from a clean clone without Antigravity.
2. CI is green on that commit and the status report matches reality.
3. You can explain three flows from memory and have modified three behaviors yourself.
4. The README states what works, what does not and the measured numbers.
5. You can describe truthfully what you wrote, what was generated and what you reviewed.

Until then use the wording "in development" with only facts you can show.

---

## Appendix A — Shatranj One project charter (draft for owner approval)

Owner-authored and versioned. Agents may read it and may propose changes as a diff for the owner; they may not edit it. It holds stable rules only. Current progress is read from PROJECT_VISION.md and README.md and is never copied here.

**Source of truth**
- Exactly two documentation files: PROJECT_VISION.md (objectives, architecture, scope, roadmap, gates, key technical decisions) and README.md (public overview, build instructions, reported implementation progress).
- There is no docs/PLAN.md and none may be created.
- If code and documents disagree, report the discrepancy; do not resolve it silently.

**Pinned decisions (from PROJECT_VISION.md; conflicts go to the owner)**
- C++17 standalone engine; 64-entry array board, a1 = 0, h8 = 63; bitboards only if measurement justifies them.
- Correctness and perft before search optimization; measure before claiming any optimization.
- The engine is an independent executable using UCI; the Node.js backend runs it as a subprocess and never searches inside the event loop.
- Deployment shape: static frontend host; backend and engine binary together in a container on a host that supports long-running processes.
- Position identity: piece placement, side to move, castling rights and en-passant square; store the full FEN to verify matches.
- Deferred until after placements: neural networks, self-play, MCTS, Redis and queues, microservices, Kubernetes, observability stacks, WebAssembly.

**Working mode: learner-authored**
- The owner writes the first implementation of every Shatranj One milestone. AI tools tutor and review.
- Teacher explains with small examples and diagrams first, then asks questions, then gives hints in this order: hint, small example, partial code. Full code only if the owner asks after attempting.
- Reviews list problems first and separate compilation, correctness, design, performance and readability. Never treat a successful compile as correctness.
- Patch proposals are disabled by default for this project. Applying a patch needs an explicit owner request and a patch-specific approval.

**Evidence rules**
- A claim such as "tests passed" must cite the commit SHA, environment and command. Author-reported results are labelled "reported", and CI results are labelled with the run.
- Reference data (for example perft counts) comes from repository test data with a cited source, not from model memory.

**Hard limits**
- Buildwise never writes to or pushes from the real Shatranj One working tree. Development and tests use a disposable fixture clone.
- Gates and dates come from PROJECT_VISION.md. Buildwise work never takes priority over Shatranj One's gates or the owner's DSA preparation.