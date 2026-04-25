# Requirements: Echoloom

**Defined:** 2026-04-24
**Core Value:** Words and phrases discovered through translation must be captured with their original context and reliably brought back for review.

## v1 Requirements

Requirements for the first release. These define the smallest useful translation-to-memory loop.

### Product Contract

- [x] **PROD-01**: The MVP acceptance path verifies that a user can translate text, save a selected word or phrase with context, find it in the library, and review it when due.
- [x] **PROD-02**: The project defines whether v1 is web-first authenticated storage or local-first single-user storage before implementation begins.
- [x] **PROD-03**: The project defines privacy and retention rules for source text, saved context, provider requests, deletion, and raw-text logging before implementation begins.

### Foundation

- [ ] **FOUND-01**: User can access a web-first application scaffold with translate, library, and review navigation.
- [ ] **FOUND-02**: User-owned data is persisted behind authenticated access and row-level access controls.
- [ ] **FOUND-03**: The app stores translation sessions, capture contexts, vocabulary items, review cards, and review logs using typed schemas.
- [ ] **FOUND-04**: Translation, vocabulary, capture, and review logic are implemented behind explicit domain/use-case boundaries.

### Translation

- [ ] **TRAN-01**: User can enter source text and choose a target language for translation.
- [ ] **TRAN-02**: User can receive a translated result with source language, target language, provider metadata, and timestamp preserved.
- [ ] **TRAN-03**: Translation responses are validated into structured fields before they are shown or saved.
- [ ] **TRAN-04**: The app avoids writing raw source text or translated private text to logs, analytics, traces, or error reports.

### Vocabulary Capture

- [ ] **CAPT-01**: User can select a word or multi-word phrase from the rendered translation result.
- [ ] **CAPT-02**: The selected item stores exact selected text, source context, translated context, language pair, logical offsets when available, and capture timestamp.
- [ ] **CAPT-03**: User can save a selected item to the vocabulary library with one primary action.
- [ ] **CAPT-04**: User can edit the saved item meaning, note, difficulty, status, and context-specific details.
- [ ] **CAPT-05**: The app detects duplicate or near-duplicate saved items in the same language pair and offers an update or merge path.

### Vocabulary Library

- [ ] **LIBR-01**: User can view saved vocabulary items in a library list.
- [ ] **LIBR-02**: User can search saved vocabulary items.
- [ ] **LIBR-03**: User can edit or delete saved vocabulary items and their stored context.
- [ ] **LIBR-04**: User can mark vocabulary items as active, paused, learned, archived, or needs edit.
- [ ] **LIBR-05**: User can record difficulty and notes for a vocabulary item.

### Review

- [ ] **REVIEW-01**: User can see how many saved vocabulary items are due for review.
- [ ] **REVIEW-02**: User can start a review session from due vocabulary items.
- [ ] **REVIEW-03**: User can review a context-first card that shows or reveals the original source context and context-specific meaning.
- [ ] **REVIEW-04**: User can grade each review answer with a small fixed rating set.
- [ ] **REVIEW-05**: The app records every review attempt and updates the next due date using a repeatable spaced repetition scheduler.
- [ ] **REVIEW-06**: User can pause, archive, or edit a confusing item during review.
- [ ] **REVIEW-07**: The review system applies daily workload limits or recovery behavior to prevent review overload.

### Quality and Verification

- [ ] **QUAL-01**: Automated tests cover translation response validation, vocabulary duplicate handling, and review scheduling.
- [ ] **QUAL-02**: End-to-end verification covers the translate -> select -> save -> library -> review loop.
- [ ] **QUAL-03**: The app tracks MVP learning-quality signals such as review completion, overdue burden, lapse rate, and edit-from-review frequency.
- [ ] **QUAL-04**: The app verifies that deletion removes saved vocabulary context and review data according to the retention rules.

## v2 Requirements

Deferred to future releases. These are valuable only after the core text-only learning loop is validated.

### Advanced Capture

- **ADV-01**: User can capture a screenshot, extract text with OCR, and route extracted text through the existing capture context pipeline.
- **ADV-02**: User can translate selected text from a browser extension and save vocabulary through the existing library pipeline.
- **ADV-03**: User can translate selected text from a desktop/system-wide capture surface after a platform feasibility spike.

### Learning Enhancements

- **LEARN-01**: User can review saved items with cloze or context-recall modes.
- **LEARN-02**: User can export vocabulary and review data to CSV or Anki-compatible formats.
- **LEARN-03**: User can hear pronunciation or TTS for saved vocabulary items.
- **LEARN-04**: User can receive context-aware explanations for ambiguous saved words or phrases.
- **LEARN-05**: User can group vocabulary items with tags or lightweight collections.

### Content and Platform

- **PLAT-01**: User can import articles or web pages as reading material.
- **PLAT-02**: User can learn from subtitles or video contexts.
- **PLAT-03**: User can use mobile-native or desktop-native clients if the web MVP proves the loop.

## Out of Scope

Explicit exclusions for the initial release.

| Feature | Reason |
|---------|--------|
| Screenshot translation and OCR in v1 | Adds image capture, OCR accuracy, layout, permissions, and privacy risk before the core learning loop is validated. |
| System-wide selection translation in v1 | Browser and OS integration multiply platform complexity before in-app selection is proven. |
| Video/subtitle learning | Requires media-specific architecture and shifts Echoloom toward a content platform. |
| AI conversation tutor | Separate learning loop that does not validate translation-first vocabulary memory. |
| Full dictionary or grammar suite | Expands the product into reference tooling instead of context-first vocabulary capture. |
| Advanced SRS configuration | Turns the MVP into deck-management software before users have reviewed enough items. |
| Heavy gamification, social, or classroom features | Not needed to prove personal translation-to-memory value. |
| Auto-saving every translated word | Creates noisy review debt and weakens intentional vocabulary capture. |
| Permanent translation history as a primary feature | Increases privacy and archive complexity; v1 should persist saved learning items and required context only. |

## Traceability

Which phases cover which requirements. Updated during roadmap creation.

| Requirement | Phase | Status |
|-------------|-------|--------|
| PROD-01 | Phase 1 | Complete |
| PROD-02 | Phase 1 | Complete |
| PROD-03 | Phase 1 | Complete |
| FOUND-01 | Phase 2 | Pending |
| FOUND-02 | Phase 2 | Pending |
| FOUND-03 | Phase 2 | Pending |
| FOUND-04 | Phase 2 | Pending |
| TRAN-01 | Phase 3 | Pending |
| TRAN-02 | Phase 3 | Pending |
| TRAN-03 | Phase 3 | Pending |
| TRAN-04 | Phase 3 | Pending |
| CAPT-01 | Phase 4 | Pending |
| CAPT-02 | Phase 4 | Pending |
| CAPT-03 | Phase 4 | Pending |
| CAPT-04 | Phase 4 | Pending |
| CAPT-05 | Phase 4 | Pending |
| LIBR-01 | Phase 4 | Pending |
| LIBR-02 | Phase 4 | Pending |
| LIBR-03 | Phase 4 | Pending |
| LIBR-04 | Phase 4 | Pending |
| LIBR-05 | Phase 4 | Pending |
| REVIEW-01 | Phase 5 | Pending |
| REVIEW-02 | Phase 5 | Pending |
| REVIEW-03 | Phase 5 | Pending |
| REVIEW-04 | Phase 5 | Pending |
| REVIEW-05 | Phase 5 | Pending |
| REVIEW-06 | Phase 5 | Pending |
| REVIEW-07 | Phase 5 | Pending |
| QUAL-01 | Phase 6 | Pending |
| QUAL-02 | Phase 6 | Pending |
| QUAL-03 | Phase 6 | Pending |
| QUAL-04 | Phase 6 | Pending |

**Coverage:**
- v1 requirements: 32 total
- Mapped to phases: 32
- Unmapped: 0

---
*Requirements defined: 2026-04-24*
*Last updated: 2026-04-25 after Phase 1 contract execution*
