# ADR-0003: Authentication & Session Model

- **Status:** **Pending Owner Decision before Phase 1**
- **Date:** 2026-11-12
- **References:** [Spec §6.1](../Buildwise_Product_Specification_v1.md#L356-L367), [Spec §7.7](../Buildwise_Product_Specification_v1.md#L535-L544), [Spec §13](../Buildwise_Product_Specification_v1.md#L771), [Runbook §1.1 & Phase 1](../Buildwise_V1_Antigravity_Build_Runbook.md#L29-L30)

## Context

Spec §6.1 notes that `auth_subject` references an external identity provider subject and recommends not storing passwords when a trusted identity provider manages auth, while Spec §13 permits either an established OIDC provider or a proven auth framework. Runbook §1.1 flags V1.0 authentication as a **PROPOSAL** requiring owner approval and leaves the session mechanism open before Phase 1 starts.

---

## Unresolved Decision 7: Authentication for V1.0 *(Needed by Phase 1)*

### Option A — Proven auth framework with local accounts for V1.0; external OIDC/SSO in Phase 8 *(Runbook PROPOSAL)*
- **How it works:** Use an established Python authentication library (no custom password cryptography) with strong password hashing (e.g., Argon2id/bcrypt via proven library) for local V1.0 accounts, preserving the `auth_subject` column for Phase 8 OIDC/SSO integration.
- **Pros:**
  - Works completely offline / locally during V1.0 development without external IdP setup, DNS callbacks, or third-party outage risk.
  - Faster, deterministic Phase 1 integration testing against real PostgreSQL.
- **Cons / Trade-offs:**
  - Application manages password reset/verification flows and stores password hashes locally in V1.0.
  - Requires explicit Runbook PROPOSAL approval since it defers external OIDC from Spec §6.1 to Phase 8.

### Option B — External OIDC identity provider from Phase 1
- **How it works:** Integrate a hosted or self-hosted OIDC provider (e.g., Auth0, Clerk, Keycloak) in Phase 1; Buildwise stores only `auth_subject` and session records, never passwords.
- **Pros:**
  - Zero password storage or credential-handling logic inside Buildwise from day one.
  - Built-in email verification, MFA, and SSO foundations early.
- **Cons / Trade-offs:**
  - Adds external provider provisioning/hosting setup, local callback complexity, and test mocking overhead in Phase 1.

---

## Unresolved Decision 8: Session Model *(Needed by Phase 1)*

### Option A — Server-side session cookies (`HttpOnly`, `SameSite`, `Secure`) + CSRF protection
- **How it works:** Backend issues an opaque session identifier stored server-side (PostgreSQL/cache), sent in an `HttpOnly` cookie, paired with CSRF token/origin validation on state-changing endpoints.
- **Pros:**
  - Immediate session revocation works reliably (matches Spec §7.7 revocation and membership-disable tests).
  - Browser JS cannot read session secrets via XSS (`HttpOnly`).
  - Aligns directly with Spec §7.7 cookie/CSRF requirements.
- **Cons / Trade-offs:**
  - Requires explicit CSRF middleware and cookie domain/SameSite configuration between React (`apps/web`) and FastAPI (`apps/api`) during local dev.
  - Requires a server lookup per request (trivial in a modular monolith).

### Option B — Short-lived signed bearer tokens (JWT) + refresh tokens
- **How it works:** Backend issues short-lived access tokens plus rotatable refresh tokens stored server-side.
- **Pros:**
  - Familiar pattern for non-browser API clients (e.g., future Phase 6 local companion connector, though connectors can also use dedicated API credentials).
  - Avoids cookie CSRF if kept strictly in memory (`Authorization` header), though persistence across page reloads requires refresh cookies or storage trade-offs.
- **Cons / Trade-offs:**
  - Immediate access revocation still requires server-side token blocklists or very short TTLs.
  - Storing tokens in `localStorage` exposes them to XSS; storing refresh tokens in cookies re-introduces cookie/CSRF considerations anyway.

### Decision Status
- **Decision 7 Status:** `Pending Owner Decision` (must approve Runbook PROPOSAL or choose external OIDC before Phase 1).
- **Decision 8 Status:** `Pending Owner Decision` (required before Phase 1).
