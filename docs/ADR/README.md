# Buildwise Architecture Decision Records (ADRs)

All architectural choices and unresolved pre-flight options are tracked here per [Spec §16 Phase 0](../Buildwise_Product_Specification_v1.md#L870-L880), [Spec §21](../Buildwise_Product_Specification_v1.md#L1061-L1073), and [Runbook §1.1–1.2 & §5](../Buildwise_V1_Antigravity_Build_Runbook.md#L21-L52).

**Policy:** Agents must never silently choose among unresolved options. Any decision marked `Pending Owner Decision` or `Deferred` lists neutral **Options & Trade-offs** and waits for explicit owner approval before the corresponding phase begins.

## ADR Index

| ADR | Topic | Spec / Runbook Coverage | Status |
|---|---|---|---|
| [0001](0001-modular-monolith-and-repo-layout.md) | Modular Monolith & Repository Layout | Spec §12–13; Phase 0 decisions #1–3, #8–10 | Accepted |
| [0002](0002-data-model-and-tenant-isolation.md) | Data Model, Roles & Tenant Isolation | Spec §6, §7.3, §16; Runbook §1.1 (Decision 9: PostgreSQL RLS) | Partially Accepted; **RLS Pending Owner Decision (Phase 1)** |
| [0003](0003-authentication-and-session-model.md) | Authentication & Session Model | Spec §6.1, §7.7, §13, §16; Runbook §1.1 (Decisions 7–8) | **Pending Owner Decision (Phase 1)** |
| [0004](0004-connector-and-ingestion-strategy.md) | Shatranj One Sources, Ingestion & Connector | Spec §4.3, §16, §17; A1, A3; Runbook §1.1 (Decisions 10–11, 14–15a) | Baseline Rules Accepted; **4 Choices Pending Owner Decision (Phases 2 & 6)** |
| [0005](0005-provider-routing-and-embeddings.md) | LLM Provider, Model Routing & Embeddings | Spec §4.6, §9.3–9.4, §16; A6; Runbook §1.1 (Decisions 12–13) | Gateway & A6 Accepted; **2 Choices Pending Owner Decision (Phases 2 & 3)** |
| [0006](0006-agent-roles-and-learner-authored-policy.md) | Agent Roles, `learner-authored` Mode & Phase 5/6 Separation | Spec §3, §7.4, §8; A4; Phase 0 decisions #4, #10 | Accepted |
| [0007](0007-sandbox-dependencies-and-hosting.md) | Sandbox Dependencies (A5 Gate) & Hosting Topology | Spec §8.3, §21; A5; Runbook §1.1 (Decisions 15b, 16) | **Deferred Gate / Pending Owner Decision (Phases 6 & 7)** |

## Summary of Unresolved Decisions (Decisions 7–16)

| Ref | Decision | Needed By | ADR | Current Status |
|---|---|---|---|---|
| Decision 7 | Authentication for V1.0 (includes Runbook PROPOSAL) | Phase 1 | [ADR-0003](0003-authentication-and-session-model.md) | Pending Owner Decision |
| Decision 8 | Session model (cookie+CSRF vs. token+refresh) | Phase 1 | [ADR-0003](0003-authentication-and-session-model.md) | Pending Owner Decision |
| Decision 9 | PostgreSQL Row-Level Security (RLS vs. app-level only) | Phase 1 | [ADR-0002](0002-data-model-and-tenant-isolation.md) | Pending Owner Decision |
| Decision 10 | First-run Shatranj One ingestion (includes Runbook PROPOSAL) | Phase 2 | [ADR-0004](0004-connector-and-ingestion-strategy.md) | Pending Owner Decision |
| Decision 11 | Background jobs (in-process worker vs. Redis + queue) | Phase 2 | [ADR-0004](0004-connector-and-ingestion-strategy.md) | Pending Owner Decision |
| Decision 12 | Embedding model and reranker | Phase 2 | [ADR-0005](0005-provider-routing-and-embeddings.md) | Pending Owner Decision |
| Decision 13 | Small model for low-complexity routing | Phase 3 | [ADR-0005](0005-provider-routing-and-embeddings.md) | Pending Owner Decision |
| Decision 14 | A1 commit SHA source without GitHub integration | Phase 2 | [ADR-0004](0004-connector-and-ingestion-strategy.md) | Pending Owner Decision |
| Decision 15a | Local workspace connector form | Phase 6 | [ADR-0004](0004-connector-and-ingestion-strategy.md) | Deferred to Phase 6 |
| Decision 15b | A5 sandbox dependency strategy (GoogleTest download vs. offline sandbox) | **Gate before Phase 6** | [ADR-0007](0007-sandbox-dependencies-and-hosting.md) | Mandatory Gate before Phase 6 |
| Decision 16 | Hosting topology | Phase 7 | [ADR-0007](0007-sandbox-dependencies-and-hosting.md) | Deferred to Phase 7 |

*(Phase 8+ items from [Spec §21](../Buildwise_Product_Specification_v1.md#L1070-L1071) — enterprise SSO/compliance specifics and end-user custom agent profiles — remain deferred to Phase 8.)*
