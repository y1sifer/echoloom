# Project Research Summary

**Project:** Echoloom
**Domain:** Translation-first vocabulary capture and spaced repetition learning app
**Researched:** 2026-04-24
**Confidence:** MEDIUM-HIGH for the text-only web MVP; MEDIUM for advanced capture paths

## Executive Summary

Echoloom is not a general translator with flashcards attached. It is a context-preserving learning loop: translate text, select useful words or phrases, save them with the original context, and review them before they fade. Research across comparable products and learning literature points to the same core: intentional capture, phrase support, editable meanings, vocabulary management, and spaced review. Echoloom's differentiator is making the translation moment reliably become a memory item without forcing users into a separate flashcard workflow.

The recommended implementation path is a focused web MVP using Next.js App Router on Vercel, Supabase Auth/Postgres/RLS/Storage, OpenAI Responses API with Structured Outputs, and `ts-fsrs` for review scheduling. Build it as a modular monolith: UI features call explicit use cases, use cases depend on domain modules, and infrastructure stays behind adapters. The architecture research assumed local-first storage, while stack research recommends web-first Supabase; roadmap planning should choose web-first Supabase unless offline/local-only use is a product requirement. Keep repository boundaries small so storage can change later if needed.

The highest risks are product-data risks, not framework risks: context can become optional, machine translation can be treated as learning truth, phrase selection can fail on real languages, and review workload can overwhelm users. Mitigate these by locking the saved-item data contract and privacy baseline first, preserving source snippets and exact selected spans, labeling saved meanings as context-specific and editable, using locale-aware selection with manual adjustment, recording review history, limiting review workload, and keeping screenshot/OCR/system-wide capture out of the MVP.

## Key Findings

### Recommended Stack

Use a single Next.js application for the MVP. Do not start with a separate backend, desktop app, browser extension, OCR pipeline, or monorepo. Supabase should be the system of record for authenticated user data, with SQL migrations and generated TypeScript types as the schema source of truth. Translation and scheduling should sit behind small service modules so later provider changes or capture clients do not rewrite the learning loop.

Critical version and dependency notes: use Node.js 24.x LTS, keep `@supabase/ssr` wrapped behind small helpers because the package is still labeled beta/unstable by Supabase, and use OpenAI Structured Outputs rather than raw JSON mode for provider responses. Avoid adding Prisma/Drizzle in the MVP unless direct Supabase SQL becomes a real recurring pain.

**Core technologies:**
- Node.js 24.x LTS + pnpm 10.33.2: runtime and package workflow with current engine headroom.
- Next.js 16.2.x + React 19.2.x + TypeScript 6.0.3: fullstack web app, typed UI/API boundaries, and App Router patterns.
- Tailwind CSS 4.2.x + shadcn/ui/Radix + lucide-react: accessible, quiet, copy-owned UI components and icons.
- Supabase Cloud + `@supabase/supabase-js` 2.104.1 + `@supabase/ssr` 0.10.2: Auth, Postgres, Storage, RLS, and generated database types.
- OpenAI Responses API + `openai` 6.34.0: translation plus structured vocabulary metadata such as lemma, part of speech, meaning, examples, and candidate phrases.
- `ts-fsrs` 5.3.2: defensible spaced repetition scheduler instead of a custom memory algorithm.
- Zod 4.3.6: runtime validation for server actions, route handlers, OpenAI outputs, and import/export boundaries.
- Vitest 4.1.5 + Playwright 1.59.1: unit tests for domain logic and end-to-end tests for translate -> save -> review.
- Vercel: straightforward deployment for the Next.js MVP.

### Expected Features

The MVP should validate one narrow loop: text translation, phrase-aware selection, context-rich vocabulary save, library management, and basic spaced review. The product should compete on saved vocabulary quality, not translation breadth.

**Must have (table stakes):**
- Text translation input/output with source and target language handling.
- Word and phrase selection from the rendered translation result.
- One-action save of the selected term or phrase into the vocabulary library.
- Required source context preservation: original source text or sentence, translated context, selected span, language pair, timestamp, and editable meaning.
- Vocabulary library with list, search, edit, delete, status, difficulty, notes, and lightweight metadata.
- Basic spaced review queue with due count, review entry point, answer grading, and context-first review card.
- Duplicate prevention for same normalized term or phrase in the same language pair.
- Quiet, fast interaction design that keeps translation connected to review.

**Should have (competitive, but gated by core-loop usage):**
- Context-locked vocabulary items where every saved item remembers where it came from.
- Phrase-first capture for expressions, collocations, idioms, and multiword terms.
- Editable context-specific meanings and notes, especially when provider output is ambiguous.
- Tags or lightweight grouping once search/status are insufficient.
- Context-aware explanation, cloze/context recall, CSV/Anki export, and pronunciation/TTS only after real usage exposes the need.

**Defer (v2+ or post-validation):**
- Screenshot translation and OCR.
- System-wide selection, browser extension, or desktop companion.
- Video/subtitle learning and article/content import platform.
- AI conversation tutor.
- Full dictionary/grammar suite.
- Advanced SRS controls and deck configuration.
- Heavy gamification, social/classroom features, and auto-saving every translated word.

### Architecture Approach

Use a modular monolith with stable domain contracts. The most important architecture decision is to normalize every future input into the same `CaptureContext` shape. The MVP should only implement manual text and translation-result selection, but the model should already allow later OCR, browser selection, and desktop selection to enter through the same ingest boundary.

**Major components:**
1. UI layer - Translate View, Selection UI, Library View, and Review UI. Screens should stay workflow-focused and thin.
2. Domain model - `CaptureContext`, `TranslationSession`, `VocabularyItem`, context snapshots, `ReviewState`, and review logs.
3. Application use cases - `createTranslationSession()`, `saveSelectedVocabularyItem()`, `getDueReviewQueue()`, and `submitReviewGrade()`.
4. Persistence boundary - Supabase repositories, SQL migrations, RLS policies, and generated database types. If local-first is chosen instead, this is the seam to replace.
5. Translation provider adapter - one server-only provider interface, initially OpenAI Responses API, with room for DeepL/Google benchmarking later.
6. Review scheduler boundary - a pure scheduling adapter around `ts-fsrs`, with review card state and review logs persisted transactionally.
7. Future capture adapters - OCR/browser/desktop capture producers that output `CaptureContext` and never write directly to vocabulary or review state.

**Key patterns to follow:**
- Capture adapter boundary: every input source produces `CaptureContext`.
- Translation provider port: provider SDK and credentials stay outside domain/use-case logic.
- Repository boundary: UI and use cases do not know Supabase query details.
- Pure scheduler contract: review scheduling is testable and not embedded in UI components.

### Critical Pitfalls

1. **Treating source context as optional metadata** - make context required for user-created learning items. A saved item should be invalid unless it has source context or an explicit manual-item flag.
2. **Letting machine translation become the learning truth** - label saved meanings as "meaning in this context," pass sentence/passage context when available, and provide edit paths before and after save.
3. **Breaking phrase selection with naive word boundaries** - avoid whitespace or ASCII tokenization. Store exact selected text and logical offsets, use locale-aware segmentation as a baseline, and allow manual span adjustment.
4. **Creating review overload or bad cards** - ship daily caps, due counts, suspend/archive, review history, and one-target context-first card templates before adding richer review modes.
5. **Mishandling sensitive pasted text** - define retention, deletion, provider, and logging rules before implementation. Do not send raw source text to logs, analytics, crash reports, or traces.

## Implications for Roadmap

Based on research, suggested phase structure:

### Phase 0: Product, Data, and Privacy Contract

**Rationale:** Context preservation, storage posture, privacy rules, and success metrics shape every later implementation decision. These must be explicit before schema or UI work starts.

**Delivers:** MVP acceptance loop, saved-item required fields, context deletion/minimization rules, raw-text logging ban, provider data-use assumptions, review success metrics, and final choice between web-first Supabase versus local-first storage.

**Addresses:** Source context preservation, product principle that memory is the product, privacy baseline, and deferred advanced capture.

**Avoids:** Optional context, translator-with-word-list drift, raw-text privacy leaks, and premature OCR/system-wide capture.

### Phase 1: Web Foundation and Core Data Model

**Rationale:** Translation, vocabulary, and review all depend on the same user-owned schema, RLS model, domain types, and repository/use-case boundaries.

**Delivers:** Next.js app scaffold, Supabase project/migrations/RLS, generated database types, domain models, repository wrappers, Zod schemas, test setup, and baseline app navigation.

**Addresses:** Stack foundation, core architecture boundaries, authenticated persistence, and context-first data model.

**Avoids:** Separate backend complexity, schema duplication through a premature ORM, UI-coupled data access, and context migrations later.

### Phase 2: Text Translation and Structured Capture

**Rationale:** Selection and vocabulary saving need a stable translation session with source text, translated text, language pair, provider metadata, and capture context.

**Delivers:** Text input, source/target language handling, translation provider adapter, OpenAI Structured Outputs parsing, request limits, provider error handling, persisted translation sessions, and text-free operational events.

**Addresses:** Text translation input/output and context-aware translation foundations.

**Avoids:** Isolated word translation without sentence context, raw JSON provider drift, raw-text logging, and provider details leaking into UI/domain code.

### Phase 3: Phrase Selection and Vocabulary Library

**Rationale:** The core product value appears when users turn translated text into intentional, context-rich vocabulary items.

**Delivers:** Phrase-aware selection UI, selected text and logical offsets, one-action save, duplicate detection, context snapshots, editable meanings/notes, library list/search/edit/delete, status, difficulty, and inactive/archived states.

**Addresses:** Word/phrase selection, source context preservation, vocabulary management, duplicate prevention, phrase-first capture, and editable meanings.

**Avoids:** Naive tokenization, word-only schemas, noisy libraries, uneditable provider output, and every saved item automatically entering active review.

### Phase 4: Review Scheduler and Context-First Cards

**Rationale:** Echoloom does not satisfy its memory promise until saved vocabulary reliably returns for review.

**Delivers:** Due queue, daily due count, `ts-fsrs` scheduling adapter, `review_cards`, `review_logs`, context-first review card, answer grading, due date updates, daily caps, suspend/archive from review, and edit-from-review for confusing cards.

**Addresses:** Basic spaced review queue, context-first cards, review workload control, and item status integration.

**Avoids:** Custom scheduler risk, invisible overdue buildup, UI-coupled scheduling, ambiguous multi-target cards, and review abandonment from overload.

### Phase 5: Learning Quality and MVP Hardening

**Rationale:** Before adding new capture surfaces, the team needs evidence that the text-only loop is used, reviewable, and not creating unmanageable debt.

**Delivers:** Learning metrics, review completion reports, answer distribution, lapse/leech signals, overdue burden, translation edit rate, confusing-card workflow, privacy deletion verification, Playwright end-to-end coverage, and error monitoring before real user testing.

**Addresses:** Missing learning outcome instrumentation, card quality tuning, backlog recovery, and MVP validation readiness.

**Avoids:** Optimizing for saved-item counts or streaks instead of retention, review sustainability, and card quality.

### Phase 6: Advanced Capture Spike

**Rationale:** OCR, browser selection, and desktop/system-wide selection are expansion layers. They should only start after the core translation-to-review loop has measurable usage and tolerable review burden.

**Delivers:** One chosen advanced capture prototype, routed through the existing `CaptureContext` ingest path. Candidate paths are WXT browser extension selection, Tauri desktop shortcut/selection bridge, or user-initiated OCR with Google Vision/Tesseract evaluation.

**Addresses:** Deferred screenshot/OCR or system-wide capture only after validation.

**Avoids:** Parallel save/review pipelines, permission-heavy work before product validation, and privacy failures from screenshots or broad capture.

### Phase Ordering Rationale

- Data and privacy come first because context preservation and raw-text handling cannot be safely bolted on later.
- Foundation precedes translation because Auth/RLS/schema/provider boundaries are cheaper to establish before real user records exist.
- Translation precedes selection because users need a stable rendered result and persisted session to select from.
- Vocabulary precedes review because review needs high-quality saved items, context, status, and duplicate handling.
- Review precedes advanced capture because broader capture surfaces multiply review load and privacy risk.
- Learning instrumentation precedes expansion because roadmap decisions should be based on retention, review burden, edit rates, and card quality rather than translation volume.

### Research Flags

Phases likely needing deeper research during planning:
- **Phase 0:** Resolve storage/auth posture. Stack research recommends web-first Supabase; architecture research assumed local-first single-user. Choose explicitly before migrations.
- **Phase 0:** Define privacy and provider data-use rules, especially retention, deletion, regional processing, and raw-text logging.
- **Phase 2:** Benchmark translation providers if target language pairs, cost, or quality become product differentiators. OpenAI is the recommended starting point, not a permanently proven best provider.
- **Phase 3:** Research/test segmentation for target languages if CJK, Thai/Lao/Khmer/Myanmar, heavy diacritics, or mixed-script text are in scope.
- **Phase 6:** Run dedicated research for browser extension permissions/auth, Tauri global selection feasibility, and OCR accuracy/privacy before roadmap commitment.

Phases with standard patterns where research-phase can likely be skipped:
- **Phase 1:** Next.js + Supabase + Zod + Vitest/Playwright scaffolding follows well-documented official patterns once the storage decision is made.
- **Phase 4:** Persisted review logs plus `ts-fsrs` scheduling is a standard implementation pattern; use UAT for card UX rather than broad technical research.
- **Phase 5:** Basic event instrumentation and end-to-end flow coverage are standard. The important decision is which learning metrics to track, not the mechanics.

## Confidence Assessment

| Area | Confidence | Notes |
|------|------------|-------|
| Stack | HIGH for web MVP; MEDIUM for deferred OCR/extension/desktop | Official docs and current package versions strongly support Next.js, Supabase, OpenAI, and `ts-fsrs`. Advanced capture needs later platform-specific validation. |
| Features | MEDIUM | Competitor patterns strongly support translation, capture, library, and SRS. Exact v1 boundaries still need user validation and target-language clarity. |
| Architecture | MEDIUM-HIGH | Modular monolith, capture context, provider ports, repositories, and scheduler boundary are solid. The only material tension is local-first versus web-first persistence. |
| Pitfalls | MEDIUM | Risks are well-supported by docs and learning research, but roadmap timing is inferred from product definition rather than observed Echoloom users. |

**Overall confidence:** MEDIUM-HIGH for a text-only, context-first web MVP. MEDIUM for future platform expansion and language-specific learning helpers.

### Gaps to Address

- **Storage/auth posture:** Decide whether Echoloom v1 is web-first authenticated Supabase or local-first single-user. Recommendation: choose web-first Supabase unless offline/local-only is a hard product requirement.
- **Target languages:** Selection, segmentation, provider quality, pronunciation, and morphology depend heavily on initial language pairs. Require representative test fixtures before shipping selection.
- **Translation quality:** OpenAI Structured Outputs are a strong starting point, but provider quality/cost should be benchmarked on real user examples before optimizing.
- **Privacy and retention:** Define how much source context is stored, how users delete or minimize it, which provider terms apply, and what never enters logs or analytics.
- **Review workload thresholds:** Decide daily caps, inactive/paused behavior, overdue recovery, and metrics for review abandonment before expanding capture.
- **Card quality validation:** Test whether context-first cards are unambiguous and useful; add edit/suspend/confusing paths before adding more review modes.

## Sources

### Local Research Artifacts

- [STACK.md](./STACK.md) - recommended technologies, versions, alternatives, and deferred capability stack.
- [FEATURES.md](./FEATURES.md) - table-stakes features, differentiators, anti-features, dependencies, and competitor analysis.
- [ARCHITECTURE.md](./ARCHITECTURE.md) - modular monolith, `CaptureContext`, use-case boundaries, data flow, and deferred integration model.
- [PITFALLS.md](./PITFALLS.md) - critical pitfalls, technical debt patterns, integration gotchas, privacy issues, and roadmap implications.
- [PROJECT.md](../PROJECT.md) - Echoloom product definition, active requirements, scope, and constraints.

### Primary (HIGH confidence)

- Next.js App Router docs - https://nextjs.org/docs/app/getting-started
- React 19 release notes - https://react.dev/blog/2024/12/05/react-19
- Node.js release schedule - https://github.com/nodejs/Release
- Tailwind CSS Next.js setup - https://tailwindcss.com/docs/guides/nextjs
- shadcn/ui Next.js installation - https://ui.shadcn.com/docs/installation/next
- Radix UI accessibility docs - https://www.radix-ui.com/primitives/docs/overview/accessibility
- Supabase Auth, SSR, RLS, generated types, and Storage docs - https://supabase.com/docs
- OpenAI Responses API and Structured Outputs docs - https://platform.openai.com/docs
- `ts-fsrs` docs - https://open-spaced-repetition.github.io/ts-fsrs/
- Vercel Next.js deployment docs - https://vercel.com/docs/frameworks/nextjs
- Unicode Text Segmentation UAX #29 - https://www.unicode.org/reports/tr29/
- MDN `Intl.Segmenter` and Selection API - https://developer.mozilla.org/
- Anki manual deck options and review workload guidance - https://docs.ankiweb.net/deck-options
- SuperMemo "Twenty rules of formulating knowledge" - https://www.supermemo.com/en/blog/twenty-rules-of-formulating-knowledge
- DeepL Translate API docs - https://developers.deepl.com/api-reference/translate
- Google Cloud Translation API and data usage docs - https://docs.cloud.google.com/translate

### Secondary (MEDIUM confidence)

- Readlang, Reverso Context, LingQ, Migaku, and FluentU feature documentation - competitor feature patterns for contextual lookup, vocabulary capture, and SRS.
- Frontiers in Education 2024 mini review - mobile-assisted vocabulary learning, digital flashcards, spaced repetition, and learner autonomy.
- Applied Psycholinguistics vocabulary learning article - context and repeated exposure in L2 vocabulary learning.
- Nakata and Elgort, "Effects of spacing on contextual vocabulary learning" - spacing and contextual vocabulary evidence.
- Aydin et al., "Broccoli: Sprinkling Lightweight Vocabulary Learning into Everyday Information Diets" - embedded contextual vocabulary learning prototype pattern.

### Deferred Capability Sources

- Chrome extension Manifest V3, context menu, messaging, service worker, and tab capture docs - browser capture feasibility.
- WXT docs - extension tooling candidate.
- Tauri v2, global shortcut, and clipboard docs - desktop companion feasibility.
- Google Cloud Vision OCR, Tesseract.js, Google ML Kit text recognition, and Windows App SDK text recognition docs - OCR options for future capture.

---
*Research completed: 2026-04-24*
*Ready for roadmap: yes*
