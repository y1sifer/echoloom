# Architecture Research

**Domain:** Translation-first vocabulary learning app
**Researched:** 2026-04-24
**Confidence:** MEDIUM-HIGH

## Recommendation

Build the MVP as a small modular monolith: one application owns the full learning loop, with local persistent data, a thin translation provider adapter, and explicit domain use cases for capture, vocabulary saving, and review scheduling. Do not build OCR, browser extensions, global shortcuts, accounts, sync, or platform daemons in the MVP.

The key architectural move is to normalize every future input into the same `CaptureContext` shape. The MVP only implements `manual_text` and `translation_result` capture sources, but the model should already allow later sources such as `ocr_image`, `browser_selection`, and `desktop_selection` without creating a second translation or vocabulary pipeline.

## Assumptions

- MVP is single-user and local-first.
- Technical stack is not selected yet; these boundaries apply whether the UI is web, desktop-web, or mobile-web.
- Translation provider selection is a stack/product decision; architecture should hide it behind a provider adapter.
- "System-wide selection" may later mean a browser extension, a desktop global shortcut/clipboard bridge, or both. The MVP should not choose that platform now.
- No account sync is needed until the core translation-to-review loop is proven.

## Standard Architecture

### System Overview

```
+--------------------------------------------------------------+
|                         UI Layer                             |
|  Translate View | Selection UI | Library View | Review UI     |
+-----------------------------+--------------------------------+
                              |
+-----------------------------v--------------------------------+
|                     Application Use Cases                    |
|  createTranslationSession()                                  |
|  saveSelectedVocabularyItem()                                |
|  getVocabularyLibrary()                                      |
|  getDueReviewQueue() / submitReviewGrade()                   |
+-----------------------------+--------------------------------+
                              |
+-----------------------------v--------------------------------+
|                       Domain Modules                         |
|  Capture Context | Translation | Vocabulary | Review Scheduler|
+-----------------------------+--------------------------------+
                              |
+-----------------------------v--------------------------------+
|                  Infrastructure / Adapters                   |
|  Local DB Repository | Translation Provider | Future Capture In|
+--------------------------------------------------------------+
```

### Component Responsibilities

| Component | Responsibility | MVP Implementation |
|-----------|----------------|--------------------|
| App shell | Routing, top-level layout, user-visible state transitions | Simple client app shell; no workflow engine |
| Capture module | Normalize input text and context into `CaptureContext` | Manual text input and in-app translation result selection only |
| Translation module | Create translation sessions and call provider adapter | One `TranslationProvider` port with one real or mocked adapter |
| Selection module | Convert UI selection into stable selected text plus source offsets | Logical text offsets; do not persist DOM `Range` objects |
| Vocabulary module | Deduplicate saved terms, attach context snapshots, expose search/filter data | Local repository and simple domain functions |
| Review scheduler | Compute due queue and next review state after a grade | Simple Leitner or SM-2-style scheduling state |
| Local persistence | Store sessions, vocabulary items, contexts, review states, and events/history | IndexedDB for browser-like app or SQLite for desktop/mobile; hidden behind repositories |
| Translation proxy, optional | Protect provider credentials if the provider cannot be called safely from the client | Minimal API route/function only if required by stack/provider |
| Future capture adapters | Convert OCR/browser/desktop selected text into `CaptureContext` | Deferred; no implementation in MVP |

## Recommended Project Structure

Use feature folders with a small shared domain layer. If the chosen stack is TypeScript, keep the structure close to this:

```
src/
|-- app/                         # app bootstrap, routes, layout
|-- features/
|   |-- translate/               # translate view and translation session UI
|   |-- vocabulary/              # library UI, item detail, search/filter controls
|   `-- review/                  # due queue and review session UI
|-- domain/
|   |-- capture.ts               # CaptureContext, CaptureSource, text range types
|   |-- translation.ts           # TranslationSession, TranslationResult
|   |-- vocabulary.ts            # VocabularyItem, ContextSnapshot
|   `-- review.ts                # ReviewState, ReviewGrade, scheduling contract
|-- use-cases/
|   |-- createTranslationSession.ts
|   |-- saveSelectedVocabularyItem.ts
|   |-- getDueReviewQueue.ts
|   `-- submitReviewGrade.ts
`-- infrastructure/
    |-- db/                      # repository implementations and schema migrations
    |-- translation/             # provider adapter(s)
    `-- capture/                 # future browser/desktop/OCR adapters, initially empty
```

### Structure Rationale

- **`features/`:** keeps UI code near user workflows without letting UI own business rules.
- **`domain/`:** gives OCR and system-wide selection a stable target data model later.
- **`use-cases/`:** keeps the MVP workflow explicit and testable without introducing a large service layer.
- **`infrastructure/`:** isolates storage and provider decisions so the stack can change without rewriting the learning loop.

## Core Domain Model

Keep the persisted model boring and context-first:

```typescript
type CaptureSource =
  | "manual_text"
  | "translation_result"
  | "ocr_image"
  | "browser_selection"
  | "desktop_selection";

type TextRange = {
  start: number;
  end: number;
};

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

type TranslationSession = {
  id: string;
  inputContextId: string;
  sourceText: string;
  translatedText: string;
  sourceLanguage?: string;
  targetLanguage: string;
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

type ReviewState = {
  itemId: string;
  dueAt: string;
  intervalDays: number;
  ease: number;
  reviewCount: number;
  lapseCount: number;
  lastReviewedAt?: string;
};
```

Important modeling choices:

- Store context snapshots separately from vocabulary items so the same word can accumulate multiple discovery contexts.
- Persist logical text offsets, not DOM `Range` objects or screen coordinates. OCR and native selection can later supply their own coordinates as secondary metadata.
- Make `CaptureSource` broader than the MVP, but do not implement inactive adapters.
- Keep review state separate from the vocabulary item so scheduling can evolve without corrupting lexical data.

## Data Flow

### 1. MVP Translation Flow

```
User enters text
  ->
Translate View creates CaptureContext(sourceType="manual_text")
  ->
createTranslationSession()
  ->
TranslationProvider.translate(sourceText, languages)
  ->
TranslationSession persisted with inputContextId
  ->
UI renders source text, translated text, and selectable segments
```

### 2. MVP Vocabulary Capture Flow

```
User selects word/phrase from translation result
  ->
Selection module returns selected text + logical range + surrounding text
  ->
saveSelectedVocabularyItem()
  ->
Vocabulary module normalizes/deduplicates headword
  ->
ContextSnapshot/CaptureContext is persisted
  ->
VocabularyItem.contextIds adds the snapshot
  ->
ReviewState is initialized if item is new
```

### 3. MVP Review Flow

```
Review UI requests due queue
  ->
getDueReviewQueue(now)
  ->
Repository loads VocabularyItem + ReviewState + latest useful context
  ->
User grades recall
  ->
submitReviewGrade(itemId, grade)
  ->
Review scheduler updates dueAt, intervalDays, ease/review counters
```

### 4. Later OCR Flow, Deferred

```
Screenshot/image capture
  ->
OCR adapter extracts text + optional bounding boxes/confidence
  ->
CaptureContext(sourceType="ocr_image", capturedText, imageRef, regions)
  ->
Same createTranslationSession() flow
  ->
Same saveSelectedVocabularyItem() flow
```

The OCR adapter should be a producer of `CaptureContext`, not a separate OCR vocabulary subsystem.

### 5. Later System-Wide Selection Flow, Deferred

```
Browser extension context menu OR desktop global shortcut
  ->
Selection bridge reads selected text and available page/app metadata
  ->
CaptureContext(sourceType="browser_selection" or "desktop_selection")
  ->
Same createTranslationSession() flow
  ->
Same saveSelectedVocabularyItem() flow
```

The system-wide feature should not write directly to the vocabulary database. It should call one ingest boundary that accepts `CaptureContext`.

## Architectural Patterns

### Pattern 1: Capture Adapter Boundary

**What:** Every input source produces the same domain object.

**When to use:** Use immediately for manual text and translation-result selection; add new adapters only when OCR or system-wide selection enters scope.

**Trade-off:** Slightly more explicit modeling now, but it prevents future duplicated pipelines.

```typescript
interface CaptureAdapter<Input> {
  capture(input: Input): Promise<CaptureContext>;
}
```

### Pattern 2: Translation Provider Port

**What:** Application use cases depend on a small translation interface, not a concrete provider SDK.

**When to use:** Use from the first real translation call.

**Trade-off:** One small adapter is justified because provider choice and credential handling are likely to change.

```typescript
interface TranslationProvider {
  translate(request: {
    text: string;
    sourceLanguage?: string;
    targetLanguage: string;
  }): Promise<{
    translatedText: string;
    detectedSourceLanguage?: string;
    providerMetadata?: Record<string, unknown>;
  }>;
}
```

### Pattern 3: Repository Boundary Around Local Persistence

**What:** UI and use cases call repositories instead of directly opening IndexedDB/SQLite tables.

**When to use:** Use from the first persisted data.

**Trade-off:** Repositories are extra files, but they keep schema migration and storage API quirks out of product workflows.

### Pattern 4: Simple Scheduler Contract

**What:** The review algorithm is a pure function over `ReviewState`, grade, and current time.

**When to use:** Use for the first review loop.

**Trade-off:** Avoids overfitting to a complex SRS algorithm before there is user data.

```typescript
function scheduleNextReview(
  state: ReviewState,
  grade: "again" | "hard" | "good" | "easy",
  reviewedAt: Date
): ReviewState;
```

## Integration Points

### External Services

| Service | Integration Pattern | MVP Stance |
|---------|---------------------|------------|
| Translation provider | `TranslationProvider` adapter; optional credential-protecting proxy | Required for real translation, but keep provider-specific logic outside domain/use cases |
| OCR engine | `OcrAdapter` that outputs `CaptureContext` plus optional regions/confidence | Deferred |
| Browser extension | Context-menu/content-script bridge that sends selected text and page metadata to capture ingest | Deferred |
| Desktop selection bridge | Global shortcut/clipboard/native bridge that sends selected text to capture ingest | Deferred |
| Sync/auth backend | Repository replacement or sync layer around local DB | Deferred until multi-device/account need is validated |

### Internal Boundaries

| Boundary | Communication | Notes |
|----------|---------------|-------|
| UI -> use cases | Direct function calls/actions | Keep screens thin and workflow-specific |
| Use cases -> domain | Direct pure functions | Avoid event bus for MVP |
| Use cases -> repositories | Repository interfaces | Supports local DB now, sync later |
| Use cases -> translation provider | Provider interface | Provider changes should not touch vocabulary/review code |
| Future adapters -> capture ingest | `CaptureContext` input | The only supported way for OCR/system selection to enter the app |

## Suggested Build Order

1. **Domain model and local persistence** - define `CaptureContext`, `TranslationSession`, `VocabularyItem`, and `ReviewState`; verify create/read/update flows locally.
2. **Manual text translation** - implement text input, translation provider adapter/mock, and persisted translation sessions.
3. **In-app selection and vocabulary save** - implement selection from translation result, context snapshot storage, dedupe, and item detail.
4. **Vocabulary management** - add search, grouping tags/status/difficulty/notes only after basic save works.
5. **Review loop** - add due queue, review card UI, grade submission, and scheduler updates.
6. **Deferred integration spike** - after the loop is stable, choose exactly one external capture path first: browser extension selection or desktop global selection. OCR should follow only after text capture is reliable.

## Anti-Patterns to Avoid

### Anti-Pattern 1: Saving Word + Meaning Only

**What people do:** Store a vocabulary row with only the selected term and translated meaning.

**Why it is wrong:** Echoloom's core value is context retention. Without source context, it becomes a generic flashcard list.

**Do this instead:** Require every saved item to reference at least one `CaptureContext`.

### Anti-Pattern 2: Separate Pipelines for OCR and Selection

**What people do:** Build OCR save logic, browser selection save logic, and manual translation save logic as independent features.

**Why it is wrong:** Review, dedupe, and context display drift apart.

**Do this instead:** Route all capture sources through the same `CaptureContext -> TranslationSession -> VocabularyItem -> ReviewState` path.

### Anti-Pattern 3: Persisting Platform-Specific Selection Objects

**What people do:** Store DOM `Range`, screenshot coordinates, or native app selection metadata as the main source of truth.

**Why it is wrong:** These objects are fragile and cannot be replayed across browsers, app restarts, OCR engines, or desktop platforms.

**Do this instead:** Persist selected text, surrounding text, logical offsets, and optional source metadata. Keep coordinates as secondary evidence only.

### Anti-Pattern 4: Building a Backend Before the Learning Loop

**What people do:** Add auth, sync, queues, and account infrastructure before proving users want the loop.

**Why it is wrong:** It adds product drag while the riskiest assumption is still whether translation discoveries become useful reviews.

**Do this instead:** Keep MVP local-first. Add a minimal translation proxy only when API credentials require it.

### Anti-Pattern 5: Coupling Scheduler to UI

**What people do:** Compute next review dates inside review components.

**Why it is wrong:** Later review modes and algorithm changes will require UI rewrites.

**Do this instead:** Keep scheduling as a pure domain function and store only algorithm-neutral review state where possible.

## Scaling Considerations

| Scale | Architecture Adjustments |
|-------|--------------------------|
| 0-1k users | Local-first modular monolith; optional translation proxy; no sync |
| 1k-100k users | Add accounts/sync only if retention or multi-device usage demands it; cache translation calls; track provider cost |
| 100k+ users | Split translation proxy, sync API, and analytics/event processing; keep capture/vocabulary/review domain contracts stable |

The first likely bottleneck is translation provider cost/latency, not local vocabulary storage. The second likely bottleneck is sync conflict handling if multi-device is added. Neither should shape the MVP beyond keeping provider and repository boundaries clean.

## Deferred Integration Notes

### OCR

- OCR engines expose text plus layout/region metadata; the app should store extracted text as primary context and optional bounding boxes/confidence as metadata.
- Web-based OCR can run in browser/Node with libraries such as Tesseract.js, while mobile/desktop stacks have native OCR APIs. This reinforces using an adapter instead of baking OCR into the vocabulary module.
- Do not store screenshots by default in MVP-era data models; keep `imageRef` optional until privacy/storage policy is decided.

### Browser Selection

- Browser extensions can attach context menu items to selected text and receive `selectionText`.
- Extension service workers are event handlers and may go dormant, so later extension work should send compact capture messages to the app rather than owning learning state.
- Extension messaging supports one-time requests and long-lived connections. A one-time capture request is enough for the first browser-selection version.

### Desktop System-Wide Selection

- Desktop global selection usually requires native permissions and platform-specific behavior. If a Tauri-style desktop shell is chosen later, global shortcuts and clipboard access are plugin/permission-scoped capabilities.
- Treat desktop selection as an external capture producer. It should never bypass `saveSelectedVocabularyItem()`.

## Confidence Assessment

| Area | Confidence | Notes |
|------|------------|-------|
| MVP component boundaries | HIGH | Directly follows project scope and standard modular-monolith practice |
| Local-first persistence boundary | HIGH | Fits single-user MVP and keeps sync out of scope |
| Capture adapter model | HIGH | Strongly supports OCR/browser/desktop selection without implementing them |
| Specific platform integration details | MEDIUM | Official docs confirm feasibility, but stack and target platforms are undecided |
| Review scheduler choice | MEDIUM | Basic scheduler is enough architecturally; algorithm can be selected later |

## Sources

- Local project context: `/Users/2black/workspace/y1sifer/echoloom/.planning/PROJECT.md`
- Local product concept: `/Users/2black/workspace/y1sifer/echoloom/README.md`
- MDN Selection API: https://developer.mozilla.org/en-US/docs/Web/API/Selection
- MDN IndexedDB API: https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API
- Chrome Extensions `contextMenus` API: https://developer.chrome.com/docs/extensions/reference/api/contextMenus
- Chrome Extensions messaging: https://developer.chrome.com/docs/extensions/develop/concepts/messaging
- Chrome extension service workers: https://developer.chrome.com/docs/extensions/develop/concepts/service-workers
- Tauri global shortcut reference: https://tauri.app/reference/javascript/global-shortcut/
- Tauri clipboard plugin: https://v2.tauri.app/plugin/clipboard/
- Tesseract.js OCR docs: https://tesseract.projectnaptha.com/
- Google ML Kit text recognition: https://developers.google.com/ml-kit/vision/text-recognition/v2/android
- Microsoft Windows App SDK text recognition: https://learn.microsoft.com/en-us/windows/ai/apis/text-recognition
- SuperMemo history and SM-2 description: https://www.supermemo.com/en/blog/the-true-history-of-spaced-repetition

---
*Architecture research for: Echoloom*
*Researched: 2026-04-24*
