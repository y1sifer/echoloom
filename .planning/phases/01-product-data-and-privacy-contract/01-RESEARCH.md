# Phase 01: Product, Data, and Privacy Contract - Research

**Researched:** 2026-04-25 [VERIFIED: `date +%Y-%m-%d`]
**Domain:** Product, data-retention, provider-request, and privacy contract for Echoloom's text-only translation-to-memory MVP [VERIFIED: local `.planning/ROADMAP.md`; VERIFIED: local `01-CONTEXT.md`]
**Confidence:** HIGH for product/data/privacy contract scope, MEDIUM-HIGH for provider recommendation because actual paid provider account setup remains a later implementation/user setup check if needed. [VERIFIED: local `01-CONTEXT.md`; CITED: https://developers.deepl.com/api-reference/translate]

<user_constraints>
## User Constraints (from CONTEXT.md) [VERIFIED: local `.planning/phases/01-product-data-and-privacy-contract/01-CONTEXT.md`]

### Locked Decisions

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

### Claude's Discretion

- Exact wording and file structure for the acceptance contract.
- Exact Supabase schema names and policy shape, as long as they satisfy the locked storage posture and privacy/deletion rules.
- Exact traditional translation provider choice, after research/planning evaluates fit, cost, language coverage, API behavior, and data handling.
- Exact metrics implementation, as long as the locked learning-quality signals are available.

### Deferred Ideas (OUT OF SCOPE)

- Multiple translation providers in v1 - defer unless research/planning finds a concrete need.
- AI as the primary translation path - not the v1 default.
- OCR, browser selection, and desktop/system-wide selection - post-v1 only after the text-only loop and review burden are validated.
</user_constraints>

<phase_requirements>
## Phase Requirements [VERIFIED: local `.planning/REQUIREMENTS.md`; VERIFIED: local `.planning/ROADMAP.md`]

| ID | Description | Research Support |
|----|-------------|------------------|
| PROD-01 | The MVP acceptance path verifies that a user can translate text, save a selected word or phrase with context, find it in the library, and review it when due. [VERIFIED: local `.planning/REQUIREMENTS.md`] | Plan a human-readable acceptance script as the first contract artifact; later Playwright E2E can mirror the same steps. [VERIFIED: local `01-CONTEXT.md`] |
| PROD-02 | The project defines whether v1 is web-first authenticated storage or local-first single-user storage before implementation begins. [VERIFIED: local `.planning/REQUIREMENTS.md`] | Lock web-first authenticated storage with Supabase Auth/Postgres/RLS as system of record; do not re-open local-first in Phase 1 planning. [VERIFIED: local `01-CONTEXT.md`; CITED: https://supabase.com/docs/guides/database/postgres/row-level-security] |
| PROD-03 | The project defines privacy and retention rules for source text, saved context, provider requests, deletion, and raw-text logging before implementation begins. [VERIFIED: local `.planning/REQUIREMENTS.md`] | Plan a retention matrix, provider request contract, deletion cascade contract, and text-free observability boundary. [VERIFIED: local `01-CONTEXT.md`; CITED: https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html] |
</phase_requirements>

## Project Constraints (from AGENTS.md) [VERIFIED: local `AGENTS.md`]

- Responses to the user must be in Chinese and end with a final line containing `🐰`. [VERIFIED: local `AGENTS.md`]
- Echoloom must stay focused on turning translation moments into durable memory; v1 must not expand into a general translator, content platform, OCR tool, or AI tutor unless planning docs move that scope into active work. [VERIFIED: local `AGENTS.md`]
- The current default technical direction is Next.js App Router, React, TypeScript, Tailwind CSS, shadcn/ui/Radix, lucide-react, Supabase Auth/Postgres/Storage/RLS, OpenAI Responses API with Structured Outputs, `ts-fsrs`, Vitest, and Playwright. [VERIFIED: local `AGENTS.md`; VERIFIED: local `.planning/research/STACK.md`]
- Source context is core product data, not optional metadata. [VERIFIED: local `AGENTS.md`; VERIFIED: local `.planning/research/PITFALLS.md`]
- Raw source text and translated private text must not enter logs, analytics, traces, or error reports. [VERIFIED: local `AGENTS.md`; VERIFIED: local `01-CONTEXT.md`]
- Screenshot/OCR, browser extension selection, and desktop/system-wide capture are deferred until the core text-only learning loop is validated. [VERIFIED: local `AGENTS.md`; VERIFIED: local `01-CONTEXT.md`]
- No `CLAUDE.md` exists in the repository root, so there are no additional CLAUDE.md directives to include. [VERIFIED: `test -f CLAUDE.md` returned non-zero]
- No project-local `.claude/skills/` or `.agents/skills/` directory exists, so there are no project skills to apply. [VERIFIED: `find .claude/skills .agents/skills -maxdepth 2 -name SKILL.md -print` returned missing directories]

## Summary

Phase 1 should produce a planning contract, not application code, schemas, migrations, or UI. [VERIFIED: local `01-CONTEXT.md`; VERIFIED: local `.planning/ROADMAP.md`] The contract must be executable by later plans: one manual MVP acceptance path, one locked storage posture, one retention/deletion/logging matrix, one provider request boundary, and one set of learning-quality signal definitions. [VERIFIED: local `01-CONTEXT.md`; VERIFIED: local `.planning/REQUIREMENTS.md`]

The storage decision is no longer open: use web-first authenticated Supabase Auth/Postgres/RLS as the v1 system of record, with any local cache treated as secondary. [VERIFIED: local `01-CONTEXT.md`] Supabase RLS is the correct enforcement point for user-owned records because Supabase documents row policies using `auth.uid()` to restrict rows to the authenticated user. [CITED: https://supabase.com/docs/guides/database/postgres/row-level-security; VERIFIED: Context7 `/supabase/supabase` RLS query]

For the traditional MT provider, use DeepL API Pro as the recommended v1 provider behind a server-side adapter. [CITED: https://developers.deepl.com/api-reference/translate; CITED: https://www.deepl.com/en/pro-api] DeepL is a good fit for this contract because it has a dedicated translation endpoint, official Node client, documented `context` parameter, documented request size constraints, backend/CORS guidance, and API Pro data security language stating source texts are deleted after processing. [CITED: https://developers.deepl.com/api-reference/translate; CITED: https://developers.deepl.com/docs/learning-how-tos/examples-and-guides/how-to-use-context-parameter; CITED: https://developers.deepl.com/docs/best-practices/cors-requests; CITED: https://support.deepl.com/hc/en-us/articles/360020685940-DeepL-API-Free-vs-DeepL-API-Pro] Keep Google Cloud Translation Advanced and Azure AI Translator as documented fallback providers if the team rejects DeepL API Pro cost or account requirements. [CITED: https://cloud.google.com/translate/docs/data-usage; CITED: https://learn.microsoft.com/en-us/azure/ai-services/translator/text-translation/reference/data-limits; CITED: https://azure.microsoft.com/en-us/pricing/details/cognitive-services/translator/]

**Primary recommendation:** Plan one canonical contract file at `.planning/contracts/MVP-DATA-PRIVACY-CONTRACT.md` with four appendices: acceptance path, retention/deletion matrix, provider request contract, and learning-quality signal definitions. [VERIFIED: local `01-CONTEXT.md`; VERIFIED: local `.planning/ROADMAP.md`]

## Architectural Responsibility Map

| Capability | Primary Tier | Secondary Tier | Rationale |
|------------|--------------|----------------|-----------|
| MVP acceptance path | Planning / Product Contract | Browser, API, Database | Phase 1 owns the human-readable path; later phases map the same path to UI, API, and persistence behavior. [VERIFIED: local `01-CONTEXT.md`; VERIFIED: local `.planning/ROADMAP.md`] |
| Web-first storage posture | Database / Storage | API / Backend | Supabase Postgres/RLS owns user-owned records; backend code should use it through repositories or server actions rather than browser-side trust. [VERIFIED: local `01-CONTEXT.md`; CITED: https://supabase.com/docs/guides/database/postgres/row-level-security] |
| Provider request minimization | API / Backend | External providers | Provider credentials and request construction belong on the server; DeepL documents backend/proxy guidance for CORS and API key protection. [CITED: https://developers.deepl.com/docs/best-practices/cors-requests] |
| Long-term saved vocabulary context | Database / Storage | API / Backend | Only saved vocabulary context is retained long term, so persistence rules must distinguish saved context from short-lived translation sessions. [VERIFIED: local `01-CONTEXT.md`] |
| Translation session retention | API / Backend | Database / Storage | Ordinary translation sessions should be short-lived, clearable, or not retained beyond the current workflow. [VERIFIED: local `01-CONTEXT.md`] |
| Vocabulary deletion | Database / Storage | API / Backend | Deleting a vocabulary item must remove associated context and review data; Postgres supports declarative referential actions such as `ON DELETE CASCADE`. [VERIFIED: local `01-CONTEXT.md`; CITED: https://www.postgresql.org/docs/current/ddl-constraints.html] |
| Production logging and analytics | API / Backend | Observability platform | Production observability must emit text-free events; OWASP Logging guidance lists sensitive data categories that should not be logged. [VERIFIED: local `01-CONTEXT.md`; CITED: https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html] |
| Learning-quality metrics | API / Backend | Database / Storage | Review completion, overdue burden, lapse rate, edit-from-review frequency, and delete/pause rate are product signals derived from review and item-status events. [VERIFIED: local `01-CONTEXT.md`; VERIFIED: local `.planning/research/PITFALLS.md`] |

## Standard Stack

### Core

| Library / Service | Version | Purpose | Why Standard |
|-------------------|---------|---------|--------------|
| Markdown planning artifact | N/A | Phase 1 deliverable format | The repository currently contains planning and product-definition documents only, and this phase explicitly does not build UI or application code. [VERIFIED: local `01-CONTEXT.md`; VERIFIED: `rg --files --hidden -g 'src/**' -g 'app/**' -g 'package.json'` returned no app files] |
| Supabase Auth/Postgres/RLS | Future app packages: `@supabase/supabase-js@2.104.1`, `@supabase/ssr@0.10.2`; npm modified 2026-04-24 and 2026-04-23 respectively | Authenticated v1 system of record and row-level user data enforcement | Web-first Supabase is locked by user decision, and Supabase documents RLS policies based on `auth.uid()`. [VERIFIED: npm registry; VERIFIED: local `01-CONTEXT.md`; CITED: https://supabase.com/docs/guides/database/postgres/row-level-security] |
| DeepL API Pro + `deepl-node` | `deepl-node@1.26.0`; npm modified 2026-04-09 | v1 traditional machine translation provider behind one adapter | DeepL provides a dedicated translate endpoint, official Node client, documented context parameter, CORS/backend guidance, and API Pro data security posture. [VERIFIED: npm registry; CITED: https://developers.deepl.com/api-reference/translate; CITED: https://github.com/DeepLcom/deepl-node; CITED: https://support.deepl.com/hc/en-us/articles/360020685940-DeepL-API-Free-vs-DeepL-API-Pro] |
| OpenAI API + `openai` | `openai@6.34.0`; npm modified 2026-04-08 | Optional AI learning enhancement only, not default translation | User decisions allow AI for structured explanations and contextual notes, and OpenAI documents API data controls and Structured Outputs for typed outputs. [VERIFIED: npm registry; VERIFIED: local `01-CONTEXT.md`; CITED: https://platform.openai.com/docs/guides/your-data; CITED: https://platform.openai.com/docs/guides/structured-outputs] |
| Zod | `zod@4.3.6`; npm modified 2026-01-25 | Future runtime validation for provider responses and contract-backed schemas | Project research recommends Zod at external boundaries, and OpenAI Node docs include Zod parsing helpers for structured outputs. [VERIFIED: npm registry; VERIFIED: local `.planning/research/STACK.md`; VERIFIED: Context7 `/openai/openai-node` structured output query] |
| `ts-fsrs` | `ts-fsrs@5.3.2`; npm modified 2026-03-31 | Future spaced repetition scheduler | Project research recommends `ts-fsrs` so review scheduling is not hand-rolled after Phase 1 defines the review acceptance path and learning signals. [VERIFIED: npm registry; VERIFIED: local `.planning/research/STACK.md`] |

### Supporting

| Library / Standard | Version | Purpose | When to Use |
|--------------------|---------|---------|-------------|
| Google Cloud Translation Advanced + `@google-cloud/translate` | `@google-cloud/translate@9.3.0`; npm modified 2025-11-11 | Fallback traditional MT provider | Use if DeepL API Pro cost/account requirements are rejected; Google documents Translation API data usage and no training use for submitted content. [VERIFIED: npm registry; CITED: https://cloud.google.com/translate/docs/data-usage] |
| Azure AI Translator REST + `@azure-rest/ai-translation-text` | `@azure-rest/ai-translation-text@1.0.1`; npm modified 2026-02-11 | Fallback traditional MT provider | Use if Azure account fit is better; Microsoft documents Translator no-trace behavior and request limits. [VERIFIED: npm registry; CITED: https://learn.microsoft.com/en-us/azure/ai-services/translator/text-translation/reference/data-limits] |
| OWASP ASVS | Current official project page checked 2026-04-25 | Security control framing | Use as the security checklist for auth, session, access control, input validation, and cryptography categories. [CITED: https://owasp.org/www-project-application-security-verification-standard/] |
| OWASP Logging Cheat Sheet | Current official cheat sheet checked 2026-04-25 | Text-free production observability boundary | Use when defining what must never appear in logs, traces, analytics, crash reports, or debug events. [CITED: https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html] |

### Alternatives Considered

| Instead of | Could Use | Tradeoff |
|------------|-----------|----------|
| DeepL API Pro | Google Cloud Translation Advanced | Google has strong documented data-usage controls and regional options, but cloud project/service-account setup is heavier than DeepL's API-key flow for a small v1. [CITED: https://cloud.google.com/translate/docs/data-usage; CITED: https://cloud.google.com/translate/docs/reference/rest/v3/projects/translateText] |
| DeepL API Pro | Azure AI Translator | Azure documents no-trace behavior and generous entry pricing, but the REST/package surface is less aligned with the existing project research than a simple dedicated DeepL adapter. [CITED: https://learn.microsoft.com/en-us/azure/ai-services/translator/text-translation/reference/data-limits; VERIFIED: local `.planning/research/STACK.md`] |
| Traditional MT default | OpenAI translation | User decisions explicitly reject AI as the default definition of translation; OpenAI remains an optional learning enhancement. [VERIFIED: local `01-CONTEXT.md`] |
| Supabase system of record | Local-first single-user storage | User decisions explicitly lock web-first Supabase as the v1 default, so Phase 1 planning should not research local-first as an alternative. [VERIFIED: local `01-CONTEXT.md`] |

**Installation:**

```bash
# Phase 1 is docs-only: do not install runtime packages for this phase.
```

**Version verification:** Package versions above were verified with `npm view <package> version time.modified --json` on 2026-04-25. [VERIFIED: npm registry]

## Architecture Patterns

### System Architecture Diagram

```text
User enters current source text
  -> Browser translate surface
  -> API/backend builds traditional MT request
       - send current source text only
       - send source/target language only
       - do not send saved history or review data
  -> Traditional provider adapter (DeepL API Pro recommended)
  -> API/backend validates response shape
  -> Browser renders translation result and selectable text
       -> if user clears ordinary session:
            delete/expire session content
       -> if user selects term or phrase:
            create saved vocabulary item
            persist selected text + source context + translated context
            initialize review data
            emit text-free learning metrics
  -> Review queue uses saved vocabulary and review state
       -> deletion path:
            delete vocabulary item
            delete associated context and review data
            keep only anonymous non-reconstructive aggregates
  -> Logs / analytics / traces:
       receive IDs, counts, language pair, timings, and status only
       never receive raw source text, translated private text, saved context, or review-history content
```

This diagram is the contract the planner should make executable; it is derived from Phase 1 decisions and the roadmap success criteria. [VERIFIED: local `01-CONTEXT.md`; VERIFIED: local `.planning/ROADMAP.md`]

### Recommended Project Structure

```text
.planning/
|-- contracts/
|   `-- MVP-DATA-PRIVACY-CONTRACT.md   # canonical Phase 1 contract artifact
`-- phases/
    `-- 01-product-data-and-privacy-contract/
        |-- 01-CONTEXT.md
        `-- 01-RESEARCH.md
```

The planner should create one canonical contract file rather than scattering Phase 1 decisions across multiple docs, because the phase goal is to lock one product/data/privacy contract before implementation. [VERIFIED: local `.planning/ROADMAP.md`; VERIFIED: local `01-CONTEXT.md`]

### Pattern 1: Executable Acceptance Path

**What:** Write the MVP path as a manual script with preconditions, steps, expected user-visible result, and data/privacy assertions. [VERIFIED: local `01-CONTEXT.md`]

**When to use:** Use before Phase 2 creates scaffold or schemas, because the acceptance path defines the loop that later E2E coverage must mirror. [VERIFIED: local `.planning/REQUIREMENTS.md`; VERIFIED: local `01-CONTEXT.md`]

**Example:**

```markdown
<!-- Source: local 01-CONTEXT.md D-01/D-02 and REQUIREMENTS.md PROD-01 -->
### MVP Acceptance Path

Preconditions:
- User is authenticated.
- Target language is selected.
- At least one saved item can be made due for review by test setup.

Steps:
1. Translate a current source text.
2. Select one word or phrase from the rendered result.
3. Save the selected item with source context and translated context.
4. Find the saved item in the library.
5. Review the item when due.

Pass criteria:
- Saved item retains context.
- Review is available when due.
- No raw source text or private translated text appears in production observability.
```

### Pattern 2: Retention Matrix

**What:** Define each data class, whether it is long-lived, where it may be stored, deletion behavior, and whether it may appear in logs/analytics. [VERIFIED: local `01-CONTEXT.md`; CITED: https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html]

**When to use:** Use before schema planning so Phase 2 can create tables, relations, and RLS policies that match deletion and retention behavior. [VERIFIED: local `.planning/ROADMAP.md`; VERIFIED: local `01-CONTEXT.md`]

**Example:**

```markdown
<!-- Source: local 01-CONTEXT.md D-05..D-12 -->
| Data Class | Long-Term Retention | Delete Behavior | Observability Rule |
|------------|---------------------|-----------------|--------------------|
| Ordinary translation session | Short-lived or clearable | User can clear or expiration removes content | Text-free events only |
| Saved vocabulary context | Yes, only when user saves item | Delete with vocabulary item | Never log raw content |
| Review history content | Linked to saved item | Delete with vocabulary item | Aggregate only if non-reconstructive |
| Anonymous aggregate metrics | Yes, if non-reconstructive | May remain after deletion | No text, no user-identifying history |
```

### Pattern 3: Provider Request Contract

**What:** Declare provider request payload rules before provider implementation: traditional MT receives only current source text and language pair; AI enhancement receives only current sentence/paragraph and selected term/phrase. [VERIFIED: local `01-CONTEXT.md`]

**When to use:** Use when planning Phase 3 translation and any later AI enhancement so provider adapters cannot accidentally include vocabulary history, review history, or unrelated contexts. [VERIFIED: local `01-CONTEXT.md`; VERIFIED: local `.planning/ROADMAP.md`]

**Example:**

```typescript
// Source: local 01-CONTEXT.md D-17..D-19; DeepL translate API docs
type TraditionalTranslateRequest = {
  sourceText: string;          // current source text only
  sourceLanguage?: string;
  targetLanguage: string;
};

type AiEnhancementRequest = {
  currentSentenceOrParagraph: string;
  selectedTermOrPhrase: string;
  sourceLanguage?: string;
  targetLanguage: string;
};

// Explicitly forbidden:
// - saved vocabulary history
// - review history
// - unrelated translation sessions
// - broad user profile data
```

### Pattern 4: Delete by Ownership Graph

**What:** Define deletion in terms of user-owned graph roots: deleting a vocabulary item removes its saved context and review data, while non-reconstructive anonymous aggregates may remain. [VERIFIED: local `01-CONTEXT.md`]

**When to use:** Use before Phase 2 schema planning and Phase 6 deletion verification. [VERIFIED: local `.planning/ROADMAP.md`; VERIFIED: local `.planning/REQUIREMENTS.md`]

**Example:**

```sql
-- Source: local 01-CONTEXT.md D-08/D-09; PostgreSQL FK docs
-- Planner should require schema work to express this relationship explicitly.
-- Example shape only; table names remain Phase 2 discretion.
foreign key (vocabulary_item_id)
references vocabulary_items(id)
on delete cascade
```

### Anti-Patterns to Avoid

- **Re-opening local-first as a Phase 1 option:** Storage posture is already locked to web-first Supabase unless a later blocker is discovered. [VERIFIED: local `01-CONTEXT.md`]
- **Splitting the contract across invisible notes:** The planner needs one canonical artifact future phases can cite. [VERIFIED: local `.planning/ROADMAP.md`; VERIFIED: local `01-CONTEXT.md`]
- **Treating provider privacy policy as app retention policy:** Provider data handling does not define Echoloom's own database, logs, analytics, traces, or deletion behavior. [VERIFIED: local `01-CONTEXT.md`; CITED: https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html]
- **Using activity volume as learning success:** Translation count and saved-item count are insufficient because the locked validation signals are review completion, overdue burden, lapse rate, edit-from-review frequency, and delete/pause rate. [VERIFIED: local `01-CONTEXT.md`]
- **Planning OCR or browser/desktop capture in v1:** Advanced capture is explicitly post-v1 and gated on text-only loop validation. [VERIFIED: local `01-CONTEXT.md`; VERIFIED: local `.planning/ROADMAP.md`]

## Don't Hand-Roll

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| User-owned web storage | Custom auth/session/database ownership model | Supabase Auth/Postgres/RLS | Supabase RLS is already the locked v1 storage posture and documents row policies using authenticated user identity. [VERIFIED: local `01-CONTEXT.md`; CITED: https://supabase.com/docs/guides/database/postgres/row-level-security] |
| Traditional translation | Browser-side API calls or a custom translation engine | Server-side DeepL API Pro adapter | DeepL documents backend/CORS guidance and official API behavior; browser-side keys would violate provider key protection. [CITED: https://developers.deepl.com/docs/best-practices/cors-requests] |
| AI output parsing | Free-form JSON parsing for optional learning enhancement | OpenAI Structured Outputs with Zod-backed schemas | Project research recommends Structured Outputs; OpenAI documents structured output support and Node Zod helpers. [VERIFIED: local `.planning/research/STACK.md`; CITED: https://platform.openai.com/docs/guides/structured-outputs; VERIFIED: Context7 `/openai/openai-node`] |
| Review scheduling algorithm | Custom memory interval algorithm | `ts-fsrs` in later review implementation | Project research already selected `ts-fsrs` to avoid ad hoc SRS behavior. [VERIFIED: local `.planning/research/STACK.md`; VERIFIED: npm registry] |
| Deletion cleanup | Best-effort manual deletes scattered across code | Explicit ownership graph, FK cascades, and transactional deletion | PostgreSQL supports referential actions such as `ON DELETE CASCADE`, and Phase 1 requires item deletion to remove context and review data. [CITED: https://www.postgresql.org/docs/current/ddl-constraints.html; VERIFIED: local `01-CONTEXT.md`] |
| Raw-text observability filtering | Per-call developer discipline | Central text-free event schema and denylist/allowlist rules | OWASP Logging guidance warns against logging sensitive data, and local decisions prohibit raw source/private translated text in production observability. [CITED: https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html; VERIFIED: local `01-CONTEXT.md`] |

**Key insight:** The hard part of this phase is not framework setup; it is preventing later phases from accidentally storing, sending, logging, or retaining the wrong text. [VERIFIED: local `.planning/research/PITFALLS.md`; VERIFIED: local `01-CONTEXT.md`]

## Common Pitfalls

### Pitfall 1: Contract too vague to drive implementation

**What goes wrong:** Later plans cannot tell which data is long-lived, which data is short-lived, or what must be deleted. [VERIFIED: local `.planning/research/PITFALLS.md`; VERIFIED: local `01-CONTEXT.md`]

**Why it happens:** The phase can be mistaken for product prose rather than an implementation gate. [VERIFIED: local `.planning/ROADMAP.md`; VERIFIED: local `01-CONTEXT.md`]

**How to avoid:** Require a matrix for data class, storage location, retention, deletion, provider exposure, and observability rule. [VERIFIED: local `01-CONTEXT.md`; CITED: https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html]

**Warning signs:** The output says "respect privacy" but does not name source text, saved context, review data, provider request data, and production logs separately. [VERIFIED: local `.planning/REQUIREMENTS.md`; VERIFIED: local `01-CONTEXT.md`]

### Pitfall 2: Local-first sneaks back in through "temporary" cache design

**What goes wrong:** A local cache becomes the effective system of record and later conflicts with Supabase RLS and deletion rules. [VERIFIED: local `01-CONTEXT.md`]

**Why it happens:** Project-level architecture research previously assumed local-first, while Phase 1 discussion resolved v1 to web-first Supabase. [VERIFIED: local `.planning/research/ARCHITECTURE.md`; VERIFIED: local `01-CONTEXT.md`]

**How to avoid:** State that any local cache is secondary, derived, clearable, and not authoritative. [VERIFIED: local `01-CONTEXT.md`]

**Warning signs:** The contract treats IndexedDB/localStorage as the durable vocabulary or review source of truth. [VERIFIED: local `01-CONTEXT.md`; VERIFIED: local `.planning/research/PITFALLS.md`]

### Pitfall 3: Provider request scope expands beyond the current task

**What goes wrong:** Translation or AI enhancement requests include saved vocabulary history, review history, unrelated sessions, or profile data. [VERIFIED: local `01-CONTEXT.md`]

**Why it happens:** "Context-aware" can be misread as "send everything useful" rather than "send only current sentence/paragraph and selected term where allowed." [VERIFIED: local `01-CONTEXT.md`; CITED: https://developers.deepl.com/docs/learning-how-tos/examples-and-guides/how-to-use-context-parameter]

**How to avoid:** Define two request types in the contract and list forbidden fields explicitly. [VERIFIED: local `01-CONTEXT.md`]

**Warning signs:** Provider adapter examples include `userVocabulary`, `reviewHistory`, `pastSessions`, or full profile data. [VERIFIED: local `01-CONTEXT.md`]

### Pitfall 4: Raw text leaks through debugging or analytics

**What goes wrong:** Raw source text, private translated text, saved context, or review-history content appears in logs, traces, crash reports, analytics, or debug output. [VERIFIED: local `01-CONTEXT.md`]

**Why it happens:** Debug logging is useful during development, but the locked decision allows it only if disabled by default and scoped away from production defaults. [VERIFIED: local `01-CONTEXT.md`]

**How to avoid:** Define a text-free observability schema with IDs, lengths, language pair, provider status, timing, and error category only. [VERIFIED: local `01-CONTEXT.md`; CITED: https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html]

**Warning signs:** Error examples include the input text, translation output, selected phrase context, or full review prompt. [VERIFIED: local `01-CONTEXT.md`]

### Pitfall 5: Delete item removes the card but leaves context or review traces

**What goes wrong:** A deleted vocabulary item no longer appears in the library, but its saved context or review data remains queryable. [VERIFIED: local `01-CONTEXT.md`; VERIFIED: local `.planning/REQUIREMENTS.md`]

**Why it happens:** Deletion can be implemented as a UI hide or item status change instead of data removal for associated context/review records. [VERIFIED: local `.planning/research/PITFALLS.md`]

**How to avoid:** Plan deletion as a data ownership rule with FK cascades or transactional deletion and later QUAL-04 verification. [VERIFIED: local `.planning/REQUIREMENTS.md`; CITED: https://www.postgresql.org/docs/current/ddl-constraints.html]

**Warning signs:** The contract allows `deleted_at` on vocabulary items but does not say what happens to saved context and review logs. [VERIFIED: local `01-CONTEXT.md`]

### Pitfall 6: Metrics optimize translation volume instead of learning quality

**What goes wrong:** The product validates saved count or translation count while review burden and memory quality are unknown. [VERIFIED: local `01-CONTEXT.md`; VERIFIED: local `.planning/research/PITFALLS.md`]

**Why it happens:** Translation activity is easier to count than review completion, overdue burden, lapse rate, edit-from-review frequency, and delete/pause rate. [VERIFIED: local `01-CONTEXT.md`]

**How to avoid:** Define each learning-quality metric, numerator/denominator, event source, and privacy-safe payload now. [VERIFIED: local `01-CONTEXT.md`]

**Warning signs:** The contract lists `translation_count` and `saved_item_count` as success signals without due/review/lapse/edit/delete metrics. [VERIFIED: local `01-CONTEXT.md`]

## Code Examples

Verified patterns from official and local sources:

### Supabase RLS Ownership Pattern

```sql
-- Source: Supabase RLS docs and Context7 /supabase/supabase query.
-- Example only; exact table names remain Phase 2 discretion.
alter table vocabulary_items enable row level security;

create policy "Users can view their own vocabulary items"
on vocabulary_items
for select
using ((select auth.uid()) = user_id);

create policy "Users can create their own vocabulary items"
on vocabulary_items
for insert
with check ((select auth.uid()) = user_id);
```

### Provider Request Boundary

```typescript
// Source: local 01-CONTEXT.md D-17..D-19.
export type ProviderPayloadAudit = {
  allowedForTraditionalMt: [
    "currentSourceText",
    "sourceLanguage",
    "targetLanguage",
  ];
  allowedForAiEnhancement: [
    "currentSentenceOrParagraph",
    "selectedTermOrPhrase",
    "sourceLanguage",
    "targetLanguage",
  ];
  forbiddenForAllProviders: [
    "historicalVocabulary",
    "reviewHistory",
    "unrelatedSavedContexts",
    "unrelatedTranslationSessions",
    "broadUserData",
  ];
};
```

### Text-Free Observability Event

```typescript
// Source: local 01-CONTEXT.md D-10..D-12 and OWASP Logging Cheat Sheet.
type TranslationObservedEvent = {
  event: "translation.completed" | "translation.failed";
  userIdHash?: string;
  sourceLanguage?: string;
  targetLanguage: string;
  sourceLength: number;
  provider: "deepl";
  providerStatus: "ok" | "error";
  durationMs: number;
  errorCategory?: "rate_limit" | "provider_unavailable" | "validation_failed";
  // raw source text, translated private text, saved context, and review content are forbidden.
};
```

### Learning Signal Definition

```markdown
<!-- Source: local 01-CONTEXT.md D-22/D-23 -->
| Signal | Definition | Privacy Payload |
|--------|------------|-----------------|
| Review completion | completed review cards / due review cards in period | counts only |
| Overdue burden | active cards overdue at period end | counts and age buckets only |
| Lapse rate | "again" grades / completed reviews | counts only |
| Edit-from-review frequency | edits launched from review / completed reviews | counts only |
| Delete/pause rate | item deletions or pauses / active items | counts only |
```

## State of the Art

| Old Approach | Current Approach | When Changed | Impact |
|--------------|------------------|--------------|--------|
| Treat storage as unresolved web-first vs local-first choice | Web-first Supabase Auth/Postgres/RLS is locked for v1 | Phase discussion on 2026-04-25 | Planner should not produce local-first decision tasks unless a concrete blocker appears. [VERIFIED: local `01-CONTEXT.md`] |
| Treat AI as the default translation path | Traditional MT is first-class; AI is optional learning enhancement | Phase discussion on 2026-04-25 | Plan one traditional provider and keep AI payloads narrow. [VERIFIED: local `01-CONTEXT.md`] |
| Keep permanent translation history | Retain only saved vocabulary item context long term; ordinary sessions are short-lived or clearable | Phase discussion on 2026-04-25 | Schema planning must separate ordinary sessions from saved learning context. [VERIFIED: local `01-CONTEXT.md`] |
| Debug by logging raw text | Debug logging is opt-in and disabled by default; production observability is text-free | Phase discussion on 2026-04-25 | Planner must include redaction/debug controls in later implementation tasks. [VERIFIED: local `01-CONTEXT.md`; CITED: https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html] |
| Validate product by translation/saved counts | Validate by review completion, overdue burden, lapse rate, edit-from-review frequency, and delete/pause rate | Phase discussion on 2026-04-25 | Metrics tasks must measure learning loop health, not only usage volume. [VERIFIED: local `01-CONTEXT.md`] |

**Deprecated/outdated:**

- Local-first/offline-first as v1 default is outdated for this project because the user locked web-first Supabase for v1. [VERIFIED: local `01-CONTEXT.md`]
- AI-only translation is outdated for this project because the user locked traditional MT as a first-class v1 path. [VERIFIED: local `01-CONTEXT.md`]
- Full permanent translation history is out of v1 scope because the user locked long-term retention to saved vocabulary context. [VERIFIED: local `01-CONTEXT.md`]
- OCR/browser/desktop capture is post-v1 because advanced capture is gated until the text-only loop is validated. [VERIFIED: local `01-CONTEXT.md`; VERIFIED: local `.planning/ROADMAP.md`]

## Assumptions Log

> All factual claims in this research were verified against local planning artifacts, npm registry checks, Context7, or official documentation. Paid provider account creation and budget remain later implementation/user setup checks if needed. [VERIFIED: npm registry; VERIFIED: local `01-CONTEXT.md`; CITED: https://www.deepl.com/en/pro-api]

| # | Claim | Section | Risk if Wrong |
|---|-------|---------|---------------|
| - | No unverified factual assumptions recorded. | All sections | N/A |

## Open Questions (RESOLVED)

1. **Will the project accept DeepL API Pro cost and account setup for v1?** [CITED: https://www.deepl.com/en/pro-api]
   - What we know: DeepL API Pro has the strongest fit for this recommended contract because it documents translation API behavior, `context`, backend/CORS guidance, an official Node client, and API Pro data security. [CITED: https://developers.deepl.com/api-reference/translate; CITED: https://developers.deepl.com/docs/learning-how-tos/examples-and-guides/how-to-use-context-parameter; CITED: https://developers.deepl.com/docs/best-practices/cors-requests; CITED: https://support.deepl.com/hc/en-us/articles/360020685940-DeepL-API-Free-vs-DeepL-API-Pro]
   - RESOLVED: DeepL API Pro is accepted as the planning recommendation for the v1 traditional MT path behind a server-side adapter. Actual cost/account setup remains a later implementation/user setup check if needed. Google Cloud Translation Advanced and Azure AI Translator remain fallback adapter targets only, without changing the v1 one-provider adapter contract. [CITED: https://cloud.google.com/translate/docs/data-usage; CITED: https://learn.microsoft.com/en-us/azure/ai-services/translator/text-translation/reference/data-limits]

2. **What exact ordinary translation-session expiration should v1 use?** [VERIFIED: local `01-CONTEXT.md`]
   - What we know: Ordinary translation sessions should be short-lived, clearable, or not retained beyond the current workflow. [VERIFIED: local `01-CONTEXT.md`]
   - RESOLVED: Ordinary translation session expiration is resolved at the contract level as short-lived, clearable, or not retained beyond the current workflow. Exact TTL, schema fields, and provider/session lifecycle details belong to later schema/provider planning. [VERIFIED: local `01-CONTEXT.md`; VERIFIED: local `.planning/ROADMAP.md`]

3. **What provider context minimization boundary should v1 use?** [VERIFIED: local `01-CONTEXT.md`]
   - What we know: Traditional translation providers may receive only the current source text plus source and target language, and AI enhancement may receive only the current sentence or paragraph plus the selected term or phrase. [VERIFIED: local `01-CONTEXT.md`]
   - RESOLVED: Context minimization is resolved as: traditional MT receives only current source text plus source/target language; AI enhancement receives only current sentence or paragraph plus selected term or phrase. Providers must not receive historical vocabulary, review history, unrelated saved contexts, unrelated translation sessions, or broad user data. [VERIFIED: local `01-CONTEXT.md`]

## Environment Availability

| Dependency | Required By | Available | Version | Fallback |
|------------|-------------|-----------|---------|----------|
| Application runtime dependencies | Phase 1 execution | N/A | N/A | Phase 1 writes planning docs only. [VERIFIED: local `01-CONTEXT.md`] |
| npm registry access | Research version verification | Yes | npm command returned package metadata on 2026-04-25 | Use official docs if registry unavailable. [VERIFIED: npm registry] |
| Context7 CLI fallback | Documentation lookup | Yes after isolated npm cache retry | `ctx7@latest` via `npx` | Official docs were also checked. [VERIFIED: Context7 `/supabase/supabase`; VERIFIED: Context7 `/websites/developers_deepl`; VERIFIED: Context7 `/openai/openai-node`] |
| App source tree | Phase 1 implementation | None found | N/A | Planner should create docs, not code. [VERIFIED: `rg --files --hidden -g 'src/**' -g 'app/**' -g 'package.json'` returned no app files] |

**Missing dependencies with no fallback:**
- None for Phase 1, because execution is documentation-only. [VERIFIED: local `01-CONTEXT.md`; VERIFIED: local `.planning/ROADMAP.md`]

**Missing dependencies with fallback:**
- None for Phase 1 execution. [VERIFIED: local `01-CONTEXT.md`]

## Security Domain

### Applicable ASVS Categories

| ASVS Category | Applies | Standard Control |
|---------------|---------|------------------|
| V2 Authentication | yes | Supabase Auth must own authenticated user identity for v1 web storage. [VERIFIED: local `01-CONTEXT.md`; CITED: https://supabase.com/docs/guides/auth] |
| V3 Session Management | yes | Use Supabase SSR/server-side auth helpers in later Next.js implementation; keep Phase 1 to contract language. [VERIFIED: local `.planning/research/STACK.md`; CITED: https://supabase.com/docs/guides/auth/server-side/nextjs] |
| V4 Access Control | yes | RLS policies must restrict user-owned rows by authenticated user identity. [VERIFIED: local `01-CONTEXT.md`; CITED: https://supabase.com/docs/guides/database/postgres/row-level-security] |
| V5 Input Validation | yes | Later provider/session/save inputs should be validated with Zod or equivalent schemas. [VERIFIED: local `.planning/research/STACK.md`; VERIFIED: npm registry] |
| V6 Cryptography | yes | Do not hand-roll crypto; rely on managed provider TLS/auth storage and documented secret handling in later implementation. [CITED: https://owasp.org/www-project-application-security-verification-standard/; VERIFIED: local `.planning/research/STACK.md`] |

### Known Threat Patterns for Echoloom

| Pattern | STRIDE | Standard Mitigation |
|---------|--------|---------------------|
| Cross-user vocabulary/context access | Information Disclosure | Supabase RLS policies using authenticated `user_id`; test denied cross-user reads in later phases. [CITED: https://supabase.com/docs/guides/database/postgres/row-level-security; VERIFIED: local `.planning/REQUIREMENTS.md`] |
| Raw source text in logs/traces/analytics | Information Disclosure | Text-free observability schema; debug disabled by default; no raw source/private translation/saved context/review content. [VERIFIED: local `01-CONTEXT.md`; CITED: https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html] |
| Provider over-sharing | Information Disclosure | Provider payload allowlist and explicit forbidden fields. [VERIFIED: local `01-CONTEXT.md`] |
| Incomplete delete | Information Disclosure | FK cascade or transactional deletion for item-owned context and review data; QUAL-04 later verifies behavior. [VERIFIED: local `.planning/REQUIREMENTS.md`; CITED: https://www.postgresql.org/docs/current/ddl-constraints.html] |
| Unvalidated provider response | Tampering | Validate provider responses into structured fields before display/save in later translation phase. [VERIFIED: local `.planning/REQUIREMENTS.md`; VERIFIED: local `.planning/research/STACK.md`] |

## Sources

### Primary (HIGH confidence)

- Local `01-CONTEXT.md` - locked user decisions for Phase 1 scope, storage posture, provider boundaries, deletion, logging, and learning signals. [VERIFIED: local `.planning/phases/01-product-data-and-privacy-contract/01-CONTEXT.md`]
- Local `.planning/REQUIREMENTS.md` - PROD-01, PROD-02, PROD-03 and later QUAL references. [VERIFIED: local `.planning/REQUIREMENTS.md`]
- Local `.planning/ROADMAP.md` - Phase 1 goal, success criteria, and docs-only boundary. [VERIFIED: local `.planning/ROADMAP.md`]
- Local `AGENTS.md` - project response rules, product scope, current technical direction, and privacy rules. [VERIFIED: local `AGENTS.md`]
- Supabase RLS docs - row-level policy model and authenticated user ownership. [CITED: https://supabase.com/docs/guides/database/postgres/row-level-security]
- Supabase Auth server-side Next.js docs - later SSR auth pattern. [CITED: https://supabase.com/docs/guides/auth/server-side/nextjs]
- DeepL Translate API docs - translate endpoint behavior. [CITED: https://developers.deepl.com/api-reference/translate]
- DeepL context parameter docs - current-source context behavior and request-size notes. [CITED: https://developers.deepl.com/docs/learning-how-tos/examples-and-guides/how-to-use-context-parameter]
- DeepL CORS/backend guidance - API key protection and backend/proxy guidance. [CITED: https://developers.deepl.com/docs/best-practices/cors-requests]
- DeepL API Free vs API Pro help - API Pro data security distinction and source-text deletion after processing. [CITED: https://support.deepl.com/hc/en-us/articles/360020685940-DeepL-API-Free-vs-DeepL-API-Pro]
- OpenAI data controls - API data training/retention controls for optional AI enhancement. [CITED: https://platform.openai.com/docs/guides/your-data]
- OpenAI Structured Outputs - typed output pattern for optional AI enhancement. [CITED: https://platform.openai.com/docs/guides/structured-outputs]
- PostgreSQL constraints docs - referential actions including cascade behavior. [CITED: https://www.postgresql.org/docs/current/ddl-constraints.html]
- OWASP Logging Cheat Sheet - sensitive data exclusion and logging controls. [CITED: https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html]
- OWASP ASVS official project - security category framing. [CITED: https://owasp.org/www-project-application-security-verification-standard/]
- npm registry - package versions and modified dates for Supabase, DeepL, OpenAI, Zod, `ts-fsrs`, Google Translate, and Azure translation REST package. [VERIFIED: npm registry]
- Context7 `/supabase/supabase` - RLS policy examples with `auth.uid()`. [VERIFIED: Context7 CLI]
- Context7 `/websites/developers_deepl` - DeepL translate/context/CORS docs. [VERIFIED: Context7 CLI]
- Context7 `/openai/openai-node` - Structured output parsing helpers. [VERIFIED: Context7 CLI]

### Secondary (MEDIUM confidence)

- Local `.planning/research/SUMMARY.md` - synthesis of stack, architecture, pitfalls, and roadmap implications. [VERIFIED: local `.planning/research/SUMMARY.md`]
- Local `.planning/research/STACK.md` - current stack recommendations and package version context from prior project research. [VERIFIED: local `.planning/research/STACK.md`]
- Local `.planning/research/PITFALLS.md` - privacy, context, translation, review, and metrics pitfalls. [VERIFIED: local `.planning/research/PITFALLS.md`]
- Local `.planning/research/ARCHITECTURE.md` - useful boundary patterns but partially superseded by the locked web-first storage decision. [VERIFIED: local `.planning/research/ARCHITECTURE.md`; VERIFIED: local `01-CONTEXT.md`]
- Google Cloud Translation data usage docs - fallback provider privacy review. [CITED: https://cloud.google.com/translate/docs/data-usage]
- Google Cloud Translation REST docs - fallback provider request model. [CITED: https://cloud.google.com/translate/docs/reference/rest/v3/projects/translateText]
- Azure AI Translator docs and pricing - fallback provider limits and cost review. [CITED: https://learn.microsoft.com/en-us/azure/ai-services/translator/text-translation/reference/data-limits; CITED: https://azure.microsoft.com/en-us/pricing/details/cognitive-services/translator/]

### Tertiary (LOW confidence)

- None. [VERIFIED: all recorded sources above]

## Metadata

**Confidence breakdown:**
- Standard stack: HIGH - local decisions lock Supabase and traditional MT boundary; npm versions and official docs were verified on 2026-04-25. [VERIFIED: local `01-CONTEXT.md`; VERIFIED: npm registry]
- Architecture: HIGH - this phase maps a documentation contract to later runtime tiers, and all runtime ownership claims are grounded in local requirements or official docs. [VERIFIED: local `.planning/ROADMAP.md`; CITED: https://supabase.com/docs/guides/database/postgres/row-level-security]
- Provider recommendation: MEDIUM-HIGH - DeepL API Pro fits the documented request/privacy boundary and is accepted as the planning recommendation; actual paid account setup remains a later implementation/user setup check if needed. [CITED: https://www.deepl.com/en/pro-api; VERIFIED: local `01-CONTEXT.md`]
- Pitfalls: HIGH - pitfalls are directly drawn from local research and locked Phase 1 decisions. [VERIFIED: local `.planning/research/PITFALLS.md`; VERIFIED: local `01-CONTEXT.md`]
- Security/privacy: HIGH for the contract baseline; later implementation still needs tests for RLS, deletion, and logging. [VERIFIED: local `.planning/REQUIREMENTS.md`; CITED: https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html]

**Research date:** 2026-04-25 [VERIFIED: `date +%Y-%m-%d`]
**Valid until:** 2026-05-25 for local product contract; provider pricing/data policies should be rechecked before implementation provider setup. [CITED: https://www.deepl.com/en/pro-api; CITED: https://cloud.google.com/translate/docs/data-usage; CITED: https://azure.microsoft.com/en-us/pricing/details/cognitive-services/translator/]
