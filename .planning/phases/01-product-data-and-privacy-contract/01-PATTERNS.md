# Phase 01: Product, Data, and Privacy Contract - Pattern Map

**Mapped:** 2026-04-25
**Files analyzed:** 1
**Analogs found:** 1 / 1

## File Classification

| New/Modified File | Role | Data Flow | Closest Analog | Match Quality |
|-------------------|------|-----------|----------------|---------------|
| `.planning/contracts/MVP-DATA-PRIVACY-CONTRACT.md` | config / documentation contract | transform, file-I/O | `.planning/phases/01-product-data-and-privacy-contract/01-RESEARCH.md` | role-match |

Notes:
- No application source, schema migration, runtime config, controller, component, service, or test file is implied for this phase.
- `.planning/contracts/` does not exist yet, so the planner should create the directory and one canonical contract file.
- Do not scatter this contract across `PROJECT.md`, `REQUIREMENTS.md`, `ROADMAP.md`, `STATE.md`, or phase notes.

## Pattern Assignments

### `.planning/contracts/MVP-DATA-PRIVACY-CONTRACT.md` (config / documentation contract, transform + file-I/O)

**Analog:** `.planning/phases/01-product-data-and-privacy-contract/01-RESEARCH.md`

**Why this analog:** The phase research explicitly recommends one canonical contract artifact and provides concrete contract sections. The existing repo has no app source and no existing `.planning/contracts/` directory, so this is the closest role match.

**File placement pattern** (`.planning/phases/01-product-data-and-privacy-contract/01-RESEARCH.md` lines 185-197):
```text
.planning/
|-- contracts/
|   `-- MVP-DATA-PRIVACY-CONTRACT.md   # canonical Phase 1 contract artifact
`-- phases/
    `-- 01-product-data-and-privacy-contract/
        |-- 01-CONTEXT.md
        `-- 01-RESEARCH.md
```

Pattern cue: create one canonical contract file rather than scattering Phase 1 decisions across multiple docs, because the phase goal is to lock one product/data/privacy contract before implementation.

**Core contract flow pattern** (`.planning/phases/01-product-data-and-privacy-contract/01-RESEARCH.md` lines 156-181):
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

**Acceptance path pattern** (`.planning/phases/01-product-data-and-privacy-contract/01-RESEARCH.md` lines 199-227):
```markdown
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

**Retention matrix pattern** (`.planning/phases/01-product-data-and-privacy-contract/01-RESEARCH.md` lines 229-245):
```markdown
| Data Class | Long-Term Retention | Delete Behavior | Observability Rule |
|------------|---------------------|-----------------|--------------------|
| Ordinary translation session | Short-lived or clearable | User can clear or expiration removes content | Text-free events only |
| Saved vocabulary context | Yes, only when user saves item | Delete with vocabulary item | Never log raw content |
| Review history content | Linked to saved item | Delete with vocabulary item | Aggregate only if non-reconstructive |
| Anonymous aggregate metrics | Yes, if non-reconstructive | May remain after deletion | No text, no user-identifying history |
```

**Provider request contract pattern** (`.planning/phases/01-product-data-and-privacy-contract/01-RESEARCH.md` lines 247-275):
```typescript
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

**Deletion ownership pattern** (`.planning/phases/01-product-data-and-privacy-contract/01-RESEARCH.md` lines 277-292):
```sql
-- Planner should require schema work to express this relationship explicitly.
-- Example shape only; table names remain Phase 2 discretion.
foreign key (vocabulary_item_id)
references vocabulary_items(id)
on delete cascade
```

**Learning signal pattern** (`.planning/phases/01-product-data-and-privacy-contract/01-RESEARCH.md` lines 443-454):
```markdown
| Signal | Definition | Privacy Payload |
|--------|------------|-----------------|
| Review completion | completed review cards / due review cards in period | counts only |
| Overdue burden | active cards overdue at period end | counts and age buckets only |
| Lapse rate | "again" grades / completed reviews | counts only |
| Edit-from-review frequency | edits launched from review / completed reviews | counts only |
| Delete/pause rate | item deletions or pauses / active items | counts only |
```

**Traceability pattern** (`.planning/REQUIREMENTS.md` lines 103-145):
```markdown
## Traceability

Which phases cover which requirements. Updated during roadmap creation.

| Requirement | Phase | Status |
|-------------|-------|--------|
| PROD-01 | Phase 1 | Pending |
| PROD-02 | Phase 1 | Pending |
| PROD-03 | Phase 1 | Pending |

**Coverage:**
- v1 requirements: 32 total
- Mapped to phases: 32
- Unmapped: 0
```

Use this pattern inside the contract as a compact "Requirements Covered" section for `PROD-01`, `PROD-02`, and `PROD-03`; do not update `REQUIREMENTS.md` in this phase mapper task.

**Success criteria pattern** (`.planning/ROADMAP.md` lines 24-32):
```markdown
### Phase 1: Product, Data, and Privacy Contract
**Goal**: The implementation has a locked MVP loop, storage posture, and privacy/deletion rules before schemas or user-data flows are built.
**Depends on**: Nothing (first phase)
**Requirements**: PROD-01, PROD-02, PROD-03
**Success Criteria** (what must be TRUE):
  1. A reviewer can follow a written MVP acceptance path covering translate, select, save with context, find in library, and review when due.
  2. The v1 storage posture is explicit: web-first authenticated storage or local-first single-user storage is chosen before implementation starts.
  3. Privacy and retention rules state what source text, saved context, provider request data, deletion behavior, and raw-text logging restrictions apply.
```

Use this pattern to make the contract falsifiable: each major section should map to a reviewer-checkable truth.

## Shared Patterns

### Product Scope Guard

**Source:** `.planning/PROJECT.md` lines 5-11
**Apply to:** Contract introduction, acceptance path, deferred scope section
```markdown
Echoloom is a translation-first vocabulary learning app for language learners who want the words they look up to become lasting memory. It connects translation, vocabulary capture, original context, and spaced review into one learning loop instead of treating translation and study as separate workflows.

The first product shape is a focused MVP: translate text, select useful words or phrases from the translation result, save them with context, and review them with a basic spaced repetition schedule.

Words and phrases discovered through translation must be captured with their original context and reliably brought back for review.
```

### Active Requirements

**Source:** `.planning/REQUIREMENTS.md` lines 10-15
**Apply to:** Contract "Requirements Covered" section
```markdown
### Product Contract

- [ ] **PROD-01**: The MVP acceptance path verifies that a user can translate text, save a selected word or phrase with context, find it in the library, and review it when due.
- [ ] **PROD-02**: The project defines whether v1 is web-first authenticated storage or local-first single-user storage before implementation begins.
- [ ] **PROD-03**: The project defines privacy and retention rules for source text, saved context, provider requests, deletion, and raw-text logging before implementation begins.
```

### Locked Retention, Deletion, And Logging Rules

**Source:** `.planning/phases/01-product-data-and-privacy-contract/01-CONTEXT.md` lines 24-36
**Apply to:** Retention matrix, deletion section, observability boundary
```markdown
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
```

### Provider Boundary

**Source:** `.planning/phases/01-product-data-and-privacy-contract/01-CONTEXT.md` lines 38-47
**Apply to:** Provider request contract, future translation adapter planning
```markdown
### Translation Provider Strategy
- **D-13:** Traditional machine translation is a first-class v1 translation path.
- **D-14:** AI translation is optional, not the default definition of translation. AI may be used as a learning enhancement for structured explanations, candidate vocabulary/phrase extraction, and contextual notes.
- **D-15:** v1 should implement one traditional translation provider and preserve a provider adapter boundary for future providers such as DeepL, Google, Microsoft, or OpenAI.
- **D-16:** v1 should not build multiple traditional providers unless research/planning identifies a concrete reason.

### Provider Request Context
- **D-17:** Traditional translation providers may receive only the current source text plus source and target language needed for that translation request.
- **D-18:** AI enhancement may receive only the current sentence or paragraph and the selected term or phrase needed for contextual explanation.
- **D-19:** Providers must not receive historical vocabulary, review history, unrelated saved contexts, unrelated translation sessions, or broad user data.
```

### Advanced Capture Gate

**Source:** `.planning/phases/01-product-data-and-privacy-contract/01-CONTEXT.md` lines 49-55
**Apply to:** Deferred scope section
```markdown
### Advanced Capture Gate
- **D-20:** Screenshot/OCR, browser selection, and desktop/system-wide selection stay out of v1.
- **D-21:** Advanced capture can be discussed only after the text-only loop is complete and review burden is manageable.

### Learning Quality Signals
- **D-22:** MVP validation should track review completion, overdue burden, lapse rate, edit-from-review frequency, and delete/pause rate.
- **D-23:** Usage volume alone, such as translation count or saved item count, is not enough to prove the learning loop works.
```

### Context-First Data Model

**Source:** `.planning/research/ARCHITECTURE.md` lines 98-169
**Apply to:** Saved vocabulary context and deletion ownership language
```typescript
type CaptureContext = {
  id: string;
  sourceType: CaptureSource;
  capturedText: string;
  surroundingText?: string;
  sourceLanguage?: string;
  targetLanguage?: string;
  documentTitle?: string;
  documentUrl?: string;
  textRange?: TextRange;
  imageRef?: string;
  createdAt: string;
};

type VocabularyItem = {
  id: string;
  headword: string;
  language: string;
  meanings: string[];
  contextIds: string[];
  notes?: string;
  tags: string[];
  status: "new" | "learning" | "known" | "suspended";
  difficulty?: "easy" | "normal" | "hard";
  createdAt: string;
  updatedAt: string;
};
```

Modeling cues to preserve:
- Store context snapshots separately from vocabulary items.
- Persist logical text offsets, not DOM `Range` objects or screen coordinates.
- Keep review state separate from vocabulary item data.

### Privacy Mistake Checklist

**Source:** `.planning/research/PITFALLS.md` lines 283-293
**Apply to:** Contract risk section and future verification requirements
```markdown
| Mistake | Risk | Prevention |
|---------|------|------------|
| Raw source text in logs, traces, crash reports, or analytics | Private user content leaks outside expected storage | Redact content by default; log IDs, lengths, language pair, and provider status only |
| No deletion model for context attached to vocabulary | User cannot remove sensitive source text without losing all learning records | Separate item identity, context snippet, and review history enough to delete or minimize context |
| Provider terms reviewed after implementation | Rework if selected API retention, region, or billing model does not fit | Complete provider data-use review in Phase 0 |
```

### Looks Done But Is Not Checklist

**Source:** `.planning/research/PITFALLS.md` lines 309-320
**Apply to:** Contract verification appendix
```markdown
- [ ] **Text translation:** Works for pasted text, but verify language pair correction, provider error handling, context size limits, and no raw-text logging.
- [ ] **Save to library:** Saves a term, but verify source sentence, selected span, translated meaning, user edit, language pair, status, and duplicate handling.
- [ ] **Basic SRS:** Cards appear on schedule, but verify daily caps, overdue behavior, review history, answer grades, suspend/archive, and due forecast.
- [ ] **Analytics:** Events exist, but verify they measure review completion, answer distribution, lapse rate, overdue burden, and card edits without raw text.
- [ ] **OCR/system-wide capture:** Demo extracts text, but verify privacy controls, context schema compatibility, OCR confidence/editing, and review integration before shipping.
```

## No Analog Found

None for the inferred file. There is no exact existing contract artifact, but `.planning/phases/01-product-data-and-privacy-contract/01-RESEARCH.md`, `.planning/REQUIREMENTS.md`, `.planning/ROADMAP.md`, `.planning/PROJECT.md`, `.planning/research/ARCHITECTURE.md`, and `.planning/research/PITFALLS.md` provide role-match analogs with concrete copyable structure.

## Metadata

**Analog search scope:** `.planning/**`, `.planning/research/**`, `.planning/phases/01-product-data-and-privacy-contract/**`

**Files scanned:** 13 planning files found by `rg --files --hidden .planning`

**No app source found:** Research and local scans indicate no `src/**`, `app/**`, or `package.json` runtime scaffold exists yet.

**Project instructions checked:** No root `CLAUDE.md`; no project-local `.claude/skills/` or `.agents/skills/` directory exists.

**Pattern extraction date:** 2026-04-25
