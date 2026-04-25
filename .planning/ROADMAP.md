# Roadmap: Echoloom

## Overview

Echoloom v1 moves from a locked product/data/privacy contract into a web-first translation-to-memory loop: authenticated storage, typed context-first schemas, text translation, phrase selection, vocabulary management, spaced review, and MVP hardening. Advanced capture surfaces such as OCR, browser selection, and desktop/system-wide selection remain post-v1 candidates until the text-only loop is validated.

## Phases

**Phase Numbering:**
- Integer phases (1, 2, 3): Planned milestone work
- Decimal phases (2.1, 2.2): Urgent insertions (marked with INSERTED)

Decimal phases appear between their surrounding integers in numeric order.

- [x] **Phase 1: Product, Data, and Privacy Contract** - Lock the MVP acceptance path, storage posture, and source-text privacy rules before implementation.
- [ ] **Phase 2: Web Foundation and Core Schema** - Establish the authenticated web app, navigation, typed schemas, and domain/use-case boundaries.
- [ ] **Phase 3: Text Translation and Structured Sessions** - Let users translate text into validated, persisted sessions without private raw-text logging.
- [ ] **Phase 4: Phrase Selection and Vocabulary Library** - Let users save selected words or phrases with context and manage them in a searchable library.
- [ ] **Phase 5: Review Scheduler and Context Cards** - Bring saved vocabulary back through due counts, context-first cards, grading, and workload controls.
- [ ] **Phase 6: Learning Quality and MVP Hardening** - Verify the full loop, learning-quality signals, scheduler behavior, duplicate handling, and privacy deletion rules.

## Phase Details

### Phase 1: Product, Data, and Privacy Contract
**Goal**: The implementation has a locked MVP loop, storage posture, and privacy/deletion rules before schemas or user-data flows are built.
**Depends on**: Nothing (first phase)
**Requirements**: PROD-01, PROD-02, PROD-03
**Success Criteria** (what must be TRUE):
  1. A reviewer can follow a written MVP acceptance path covering translate, select, save with context, find in library, and review when due.
  2. The v1 storage posture is explicit: web-first authenticated storage or local-first single-user storage is chosen before implementation starts.
  3. Privacy and retention rules state what source text, saved context, provider request data, deletion behavior, and raw-text logging restrictions apply.
**Plans**: 1/1 complete
**UI hint**: no

### Phase 2: Web Foundation and Core Schema
**Goal**: Users can access a web app with authenticated, typed, context-first data foundations for translation, vocabulary, capture, and review.
**Depends on**: Phase 1
**Requirements**: FOUND-01, FOUND-02, FOUND-03, FOUND-04
**Success Criteria** (what must be TRUE):
  1. User can open the web app and navigate between Translate, Library, and Review areas.
  2. User-owned data is persisted behind authenticated access, and cross-user access is rejected by access controls.
  3. Translation sessions, capture contexts, vocabulary items, review cards, and review logs exist as typed persisted records.
  4. Translation, vocabulary, capture, and review behavior is reachable through explicit domain/use-case boundaries instead of direct UI-to-storage coupling.
**Plans**: TBD
**UI hint**: yes

### Phase 3: Text Translation and Structured Sessions
**Goal**: Users can translate text into a validated session that preserves language metadata and avoids private raw-text operational leaks.
**Depends on**: Phase 2
**Requirements**: TRAN-01, TRAN-02, TRAN-03, TRAN-04
**Success Criteria** (what must be TRUE):
  1. User can enter source text, choose a target language, and receive a translated result.
  2. User can inspect the translation session's source language, target language, provider metadata, and timestamp.
  3. Translation responses are validated into structured fields before they are shown or saved.
  4. Raw source text and translated private text are absent from logs, analytics, traces, and error reports.
**Plans**: TBD
**UI hint**: yes

### Phase 4: Phrase Selection and Vocabulary Library
**Goal**: Users can turn translation results into editable, context-rich vocabulary items and manage them in a searchable library.
**Depends on**: Phase 3
**Requirements**: CAPT-01, CAPT-02, CAPT-03, CAPT-04, CAPT-05, LIBR-01, LIBR-02, LIBR-03, LIBR-04, LIBR-05
**Success Criteria** (what must be TRUE):
  1. User can select an exact word or multi-word phrase from the rendered translation result and save it with one primary action.
  2. Saved vocabulary preserves selected text, source context, translated context, language pair, logical offsets when available, and capture timestamp.
  3. User is warned about duplicate or near-duplicate saved items in the same language pair and can choose an update or merge path.
  4. User can view and search saved vocabulary items in the library.
  5. User can edit or delete a saved item, including meaning, note, difficulty, status, stored context, and context-specific details.
**Plans**: TBD
**UI hint**: yes

### Phase 5: Review Scheduler and Context Cards
**Goal**: Users can review saved vocabulary when due through a repeatable, context-first spaced repetition loop with workload controls.
**Depends on**: Phase 4
**Requirements**: REVIEW-01, REVIEW-02, REVIEW-03, REVIEW-04, REVIEW-05, REVIEW-06, REVIEW-07
**Success Criteria** (what must be TRUE):
  1. User can see how many saved vocabulary items are due and start a review session from that queue.
  2. User can review a card that shows or reveals the original source context and context-specific meaning.
  3. User can grade each answer with a small fixed rating set, and each grade records an attempt and updates the next due date repeatably.
  4. Daily workload limits or recovery behavior prevent overdue review from growing without bounds.
  5. User can pause, archive, or edit a confusing item during review, and the change affects future review eligibility.
**Plans**: TBD
**UI hint**: yes

### Phase 6: Learning Quality and MVP Hardening
**Goal**: The complete MVP loop is verified, observable, and privacy-safe enough for validation before any advanced capture expansion.
**Depends on**: Phase 5
**Requirements**: QUAL-01, QUAL-02, QUAL-03, QUAL-04
**Success Criteria** (what must be TRUE):
  1. Automated tests cover translation response validation, vocabulary duplicate handling, and review scheduling behavior.
  2. End-to-end verification completes the translate, select, save, library, and review loop from a user perspective.
  3. MVP learning-quality signals are tracked for review completion, overdue burden, lapse rate, and edit-from-review frequency.
  4. Deletion verification confirms saved vocabulary context and review data are removed according to the retention rules.
**Plans**: TBD
**UI hint**: no

## Progress

**Execution Order:**
Phases execute in numeric order: 1 -> 2 -> 3 -> 4 -> 5 -> 6

| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. Product, Data, and Privacy Contract | 1/1 | Complete | 2026-04-25 |
| 2. Web Foundation and Core Schema | 0/TBD | Not started | - |
| 3. Text Translation and Structured Sessions | 0/TBD | Not started | - |
| 4. Phrase Selection and Vocabulary Library | 0/TBD | Not started | - |
| 5. Review Scheduler and Context Cards | 0/TBD | Not started | - |
| 6. Learning Quality and MVP Hardening | 0/TBD | Not started | - |

## Requirement Coverage

| Requirement Range | Phase |
|-------------------|-------|
| PROD-01..PROD-03 | Phase 1 |
| FOUND-01..FOUND-04 | Phase 2 |
| TRAN-01..TRAN-04 | Phase 3 |
| CAPT-01..CAPT-05, LIBR-01..LIBR-05 | Phase 4 |
| REVIEW-01..REVIEW-07 | Phase 5 |
| QUAL-01..QUAL-04 | Phase 6 |

Coverage: 32/32 v1 requirements mapped. No orphaned v1 requirements.

## Post-v1 Note

Advanced capture remains out of v1. Screenshot/OCR, browser selection, and desktop/system-wide selection should be treated as post-v1 spikes that reuse the existing capture context pipeline after the core text-only loop shows acceptable learning quality and review burden.
