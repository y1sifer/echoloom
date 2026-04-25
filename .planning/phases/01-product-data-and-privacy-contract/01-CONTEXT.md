# Phase 1: Product, Data, and Privacy Contract - Context

**Gathered:** 2026-04-25
**Status:** Ready for planning

<domain>
## Phase Boundary

This phase locks the product, data, and privacy contract before schemas, user-data flows, translation integrations, or review behavior are implemented. It does not build UI or application code. It defines the MVP acceptance path, v1 storage posture, source-text retention, deletion behavior, logging boundaries, translation provider strategy, provider request context, advanced-capture gates, and learning-quality signals.

</domain>

<decisions>
## Implementation Decisions

### MVP Acceptance Path
- **D-01:** The MVP contract must include a manual end-to-end verification script covering: translate text, select a word or phrase, save it with context, find it in the library, and review it when due.
- **D-02:** Later automated E2E coverage is still required by `QUAL-02`, but this phase should first write the human-readable acceptance path that future planning can convert into implementation checks.

### Storage Posture
- **D-03:** v1 is web-first. Supabase Auth/Postgres/RLS should be treated as the system of record unless later planning discovers a blocker.
- **D-04:** Local-first/offline-first is not the v1 default. Any local cache should be secondary to the authenticated web storage model.

### Source Text Retention
- **D-05:** Only user-saved vocabulary item context is retained long term.
- **D-06:** Ordinary translation sessions should be short-lived, clearable, or not retained beyond what is needed for the current workflow.
- **D-07:** Full permanent translation history is not part of v1.

### Deletion Rules
- **D-08:** Deleting a vocabulary item must also remove its associated stored context and review data.
- **D-09:** Anonymous aggregate metrics may remain after deletion, as long as they cannot reconstruct source text, translated private text, saved context, or user-identifying vocabulary history.

### Logging and Analytics Boundary
- **D-10:** Configurable debug logging is allowed, but it must be disabled by default.
- **D-11:** Production logs, analytics, traces, and error reports must not contain raw source text, translated private text, saved context, historical vocabulary content, or review-history content.
- **D-12:** If debug logging is introduced for development, planning must include explicit controls that keep it opt-in, environment-scoped, and unsuitable for production defaults.

### Translation Provider Strategy
- **D-13:** Traditional machine translation is a first-class v1 translation path.
- **D-14:** AI translation is optional, not the default definition of translation. AI may be used as a learning enhancement for structured explanations, candidate vocabulary/phrase extraction, and contextual notes.
- **D-15:** v1 should implement one traditional translation provider and preserve a provider adapter boundary for future providers such as DeepL, Google, Microsoft, or OpenAI.
- **D-16:** v1 should not build multiple traditional providers unless research/planning identifies a concrete reason.

### Provider Request Context
- **D-17:** Traditional translation providers may receive only the current source text plus source and target language needed for that translation request.
- **D-18:** AI enhancement may receive only the current sentence or paragraph and the selected term or phrase needed for contextual explanation.
- **D-19:** Providers must not receive historical vocabulary, review history, unrelated saved contexts, unrelated translation sessions, or broad user data.

### Advanced Capture Gate
- **D-20:** Screenshot/OCR, browser selection, and desktop/system-wide selection stay out of v1.
- **D-21:** Advanced capture can be discussed only after the text-only loop is complete and review burden is manageable.

### Learning Quality Signals
- **D-22:** MVP validation should track review completion, overdue burden, lapse rate, edit-from-review frequency, and delete/pause rate.
- **D-23:** Usage volume alone, such as translation count or saved item count, is not enough to prove the learning loop works.

### the agent's Discretion
- Exact wording and file structure for the acceptance contract.
- Exact Supabase schema names and policy shape, as long as they satisfy the locked storage posture and privacy/deletion rules.
- Exact traditional translation provider choice, after research/planning evaluates fit, cost, language coverage, API behavior, and data handling.
- Exact metrics implementation, as long as the locked learning-quality signals are available.

</decisions>

<canonical_refs>
## Canonical References

**Downstream agents MUST read these before planning or implementing.**

### Project scope and roadmap
- `.planning/PROJECT.md` — Product definition, core value, active scope, and out-of-scope boundaries.
- `.planning/REQUIREMENTS.md` — v1 requirements, especially `PROD-01`, `PROD-02`, and `PROD-03`.
- `.planning/ROADMAP.md` — Phase 1 goal, success criteria, dependency order, and post-v1 advanced-capture note.
- `.planning/STATE.md` — Current project state, blockers, and session continuity.

### Research findings
- `.planning/research/SUMMARY.md` — Research synthesis, storage/auth tension, provider boundary implications, and Phase 1 flags.
- `.planning/research/STACK.md` — Current web-first stack recommendation, provider adapter recommendation, and Supabase/OpenAI notes.
- `.planning/research/ARCHITECTURE.md` — `CaptureContext`, modular monolith boundaries, and future capture adapter model.
- `.planning/research/PITFALLS.md` — Context, machine-translation, review-overload, and privacy pitfalls that Phase 1 must prevent.

</canonical_refs>

<code_context>
## Existing Code Insights

### Reusable Assets
- None yet. The repository currently contains planning and product-definition documents only.

### Established Patterns
- Planning artifacts are committed under `.planning/`.
- Current implementation direction is web-first and context-first, based on research artifacts.
- No application code exists yet, so downstream planning should create initial project structure rather than integrate with existing source files.

### Integration Points
- Phase 2 will consume this contract when creating the web app scaffold, Supabase schema/RLS posture, provider boundary, and typed domain/use-case boundaries.
- Later phases must preserve the provider request boundary and logging/deletion rules defined here.

</code_context>

<specifics>
## Specific Ideas

- The user explicitly clarified that AI translation should not be treated as the only translation path.
- Traditional machine translation should be a core v1 capability; AI is a possible enhancement layer for learning support.
- The privacy boundary must distinguish normal provider requests from logs, analytics, traces, and error reports.

</specifics>

<deferred>
## Deferred Ideas

- Multiple translation providers in v1 — defer unless research/planning finds a concrete need.
- AI as the primary translation path — not the v1 default.
- OCR, browser selection, and desktop/system-wide selection — post-v1 only after the text-only loop and review burden are validated.

</deferred>

---

*Phase: 01-product-data-and-privacy-contract*
*Context gathered: 2026-04-25*
