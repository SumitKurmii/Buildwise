# ADR-0002: Data Model, System Roles & Tenant Isolation

- **Status:** Core Schema Baseline Accepted; **PostgreSQL RLS (Decision 9) Pending Owner Decision before Phase 1**
- **Date:** 2026-11-12
- **References:** [Spec §6](../Buildwise_Product_Specification_v1.md#L352-L448), [Spec §7.2–7.3](../Buildwise_Product_Specification_v1.md#L462-L485), [Runbook §1.1 & Phase 1](../Buildwise_V1_Antigravity_Build_Runbook.md#L31), Amendment A3, Phase 0 Owner Decision #7

## Accepted Baseline Rules

1. **Primary Relational & Vector Store:** PostgreSQL with `pgvector` extension, UUID primary keys, UTC timestamps, explicit foreign keys, indexed `tenant_id` and `project_id` columns, and soft deletion where appropriate.
2. **Six Conceptual Entity Domains (Spec §6.1–6.6):**
   - Identity & tenancy (`tenants`, `users`, `tenant_memberships`, `teams`, `team_memberships`, `invitations`).
   - Projects & access control (`projects`, `project_memberships`, `roles`, `permissions`, `role_permissions`, `resource_acl`, `policy_rules`).
   - Project resources & knowledge (`repositories`, `workspace_connections`, `resources`, `resource_versions`, `documents`, `document_chunks`, `embeddings`, `ingestion_jobs`, `knowledge_sources`).
   - Conversations & agents (`conversations`, `messages`, `message_sources`, `agent_profiles`, `agent_runs`, `agent_run_events`, `tool_invocations`, `approval_requests`).
   - Memory, plans & progress (`memory_items`, `milestones`, `tasks`, `task_dependencies`, `acceptance_criteria`, `progress_evidence`, `reviews`, `review_findings`).
   - Model usage & audit (`model_configs`, `model_usage`, `usage_budgets`, `secret_references`, `audit_events`).
3. **Tenant Scoping & Central Authorization:** Nearly all business entities are scoped to one `tenant_id`. Every endpoint and service call passes through one central deny-by-default authorization check; client-supplied tenant IDs are never trusted.
4. **ACL Invalidation & Retrieval Freshness (Spec §6.3):** Permission changes must invalidate or refresh indexed ACL metadata. Retrieval must verify current permissions; a stale `acl_snapshot` must never grant access.
5. **Secret-Storage Invariant (Spec §6.6):** `model_configs` must never store raw API keys, and `secret_references` stores only references to an external secret store—never secret plaintext.
6. **System Roles vs. V1.0 Personal Workspace Roles (Owner Decision #7):**
   - Preserve all five system roles from Spec §6.2 in the database role seed/schema design: `Owner`, `Admin`, `Project Lead`, `Collaborator`, `Viewer`.
   - Scope V1.0 personal workspace mode to the four personal roles from Spec §7.3 (`Workspace Owner` mapping to the `Owner` system role, plus `Project Lead`, `Collaborator`, and `Viewer`), while maintaining schema readiness for tenant `Admin` and future enterprise features (Phase 8).
7. **Authoritative Source Mirroring (A3):** For Shatranj One, repository docs (`PROJECT_VISION.md` and `README.md`) are authoritative for scope, gates, and acceptance criteria. Database tables (`milestones`, `tasks`, `acceptance_criteria`) mirror them read-only with `last_synced_commit` and drift detection.

---

## Unresolved Decision 9: Row-Level Security (RLS) in PostgreSQL *(Needed by Phase 1)*

Spec §6 states *"Enforce tenant isolation in application services and with PostgreSQL Row-Level Security where practical."* Runbook §1.1 leaves whether to enforce RLS in Phase 1 as an open owner choice.

### Option A — Application-layer scoping + PostgreSQL Row-Level Security (RLS)
- **How it works:** FastAPI sets the authenticated `app.current_tenant_id` (and user/project context where applicable) on each DB transaction/session, and PostgreSQL RLS policies enforce `tenant_id` isolation at the database engine level in addition to application queries.
- **Pros:**
  - Defense in depth: a forgotten `WHERE tenant_id = ...` filter in Python code or a raw SQL query still cannot read or mutate another tenant's rows.
  - Strong alignment with Phase 1–8 security guarantees and enterprise readiness.
- **Cons / Trade-offs:**
  - Requires careful SQLAlchemy/async connection-pool transaction management (setting and resetting `SET LOCAL` per transaction, handling superuser/migration roles that bypass RLS vs. restricted app DB role).
  - Adds setup and debugging complexity in Phase 1 migrations and integration tests.

### Option B — Application-layer tenant scoping only in Phase 1 (defer RLS)
- **How it works:** A mandatory repository/service abstraction and central authorization policy enforce `tenant_id` and `project_id` filtering on all queries; PostgreSQL RLS is not enabled in Phase 1.
- **Pros:**
  - Simpler initial database migrations, connection pooling, and test fixtures during week-1 Phase 1 implementation.
  - Lower friction when running cross-tenant admin/audit setup in tests.
- **Cons / Trade-offs:**
  - Single layer of defense: any query bypassing the scoped repository layer could accidentally leak rows across tenants.
  - Adding RLS later requires retroactive migration and refactoring of DB session/transaction setup.

### Decision Status
- **Status:** `Pending Owner Decision` (required before Phase 1 database/migration implementation begins).
