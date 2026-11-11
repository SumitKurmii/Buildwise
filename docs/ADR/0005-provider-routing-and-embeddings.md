# ADR-0005: Model Gateway, Routing, Embeddings & Provider Capabilities

- **Status:** Provider Interface & A6 Baseline Accepted; **Decisions 12 & 13 and Provider Capability Verification Pending**
- **Date:** 2026-11-12
- **References:** [Spec §4.6](../Buildwise_Product_Specification_v1.md#L238-L257), [Spec §9.3–9.4](../Buildwise_Product_Specification_v1.md#L600-L618), [Runbook §1.1–1.3, Phase 3](../Buildwise_V1_Antigravity_Build_Runbook.md#L32-L49)

## Accepted Baseline Rules

1. **Provider Abstraction & Secret Hygiene:**
   - All LLM calls go through a backend `model_gateway` adapter interface; browser clients never call model providers directly.
   - Initial provider target: Experiential Labs OpenAI-compatible endpoint (`https://api.experientiallabs.ai/v1`), initial reasoning model ID `claude-fable-5.2`, authenticated via server-side `EXPLABS_API_KEY`.
   - Model identifiers and base URLs live in configuration/allowlists (`model_configs`), never hard-coded as unconfigurable constants or invented. Promotional free routes (if any) must be configured separately.
2. **Fake Provider & No-LLM Mode (A6):**
   - Automated unit/integration tests use a deterministic fake provider; no real provider calls occur in automated CI.
   - Non-AI features (auth, projects, files, tasks, evidence, audit views) must work cleanly when LLM calls are disabled.
   - Real provider usage requires explicit token budgets, per-call latency/token/cost accounting, and hard caps.
3. **Deterministic-First Routing:**
   - Use deterministic DB/tool queries for structured facts (task status, gates, permissions) and simple UI commands rather than calling an LLM unnecessarily.
4. **Manual Model Override & Visibility (Spec §4.6):**
   - Provide a manual model override (constrained to allowlisted/permitted models and active budget limits) and show a visible indicator of which model handled each request.

---

## Unresolved Decision 12: Embedding Model & Reranker *(Needed by Phase 2)*

*(No verified Experiential Labs embedding model ID is confirmed in the baseline docs; capability must be verified before Phase 2 or a local model selected.)*

### Option A — Dedicated Experiential Labs / external API embedding model (if supported by endpoint)
- **Pros:** Zero local CPU/GPU memory overhead inside the Buildwise backend; faster ingestion for larger docs.
- **Cons / Trade-offs:** Incurs external API latency/cost and requires verifying whether `https://api.experientiallabs.ai/v1/embeddings` exists and what dimensions/model IDs are offered.

### Option B — Local dedicated open-weights embedding model (e.g., running locally in Python / container)
- **Pros:** Zero external token cost; works fully offline and inside no-LLM / quota-restricted runs; deterministic across environments.
- **Cons / Trade-offs:** Higher RAM/CPU usage and slower indexing on local hardware; requires model weight caching/pinning.

### Reranker Sub-option (Needed by Phase 2)
- **No reranker in V1.0 initial RAG** (hybrid lexical + pgvector dense retrieval only) vs. **lightweight cross-encoder / lexical reranker** — evaluated against the Phase 2 owner-authored 30+5 RAG benchmark.

---

## Unresolved Decision 13: Small Model for Low-Complexity Routing *(Needed by Phase 3)*

*(Spec §4.6 prohibits inventing model identifiers; small model choice depends on verified Experiential Labs catalog or deterministic fallback.)*

### Option A — Verified small/economical model from Experiential Labs catalog (if available)
- **Pros:** Lower cost and latency for simple classification/summarization while keeping single-provider setup.
- **Cons / Trade-offs:** Depends on verifying an approved small model ID and pricing on the Experiential Labs endpoint.

### Option B — No separate small LLM in V1.0 (deterministic routing + `claude-fable-5.2` only)
- **Pros:** Zero risk of unverified model IDs; simpler routing test matrix in Phase 3; deterministic logic handles simple queries at zero token cost.
- **Cons / Trade-offs:** All non-deterministic LLM responses use the larger model, raising token cost per conversation.

---

## Provider Capability Verification Checklist *(Before Phase 3 Live Call)*

Per Runbook §1.3 & Phase 3, verify without guessing:
1. Whether Antigravity model usage and `EXPLABS_API_KEY` share one quota, and set a hard development token cap.
2. Whether `claude-fable-5.2` supports OpenAI-compatible SSE streaming and tool/function calling schemas.
3. Whether Experiential Labs exposes an `/embeddings` endpoint or small-tier model IDs.

### Decision Status
- **Decision 12 (Embeddings/Reranker):** `Pending Owner Decision` (required before Phase 2).
- **Decision 13 (Small Routing Model):** `Pending Owner Decision` (required before Phase 3).
