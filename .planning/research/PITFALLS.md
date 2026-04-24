# Pitfalls Research

**Domain:** Translation-first vocabulary learning app with spaced repetition MVP
**Project:** Echoloom
**Researched:** 2026-04-24
**Confidence:** MEDIUM

Confidence is MEDIUM because the major risks are well-supported by official documentation and vocabulary-learning research, but the exact roadmap ordering is inferred from Echoloom's current product definition rather than validated user behavior.

## Suggested Phase Labels

These labels are recommendations for roadmap planning, not existing commitments:

| Phase | Purpose |
|-------|---------|
| Phase 0: Product Contract and Privacy Baseline | Lock the core learning loop, data retention rules, provider assumptions, and success metrics before implementation. |
| Phase 1: Translation and Context Capture | Build text translation, source/target language handling, source context capture, and saveable translation result structure. |
| Phase 2: Selection and Vocabulary Library | Build phrase-aware selection, duplicate handling, item editing, search, grouping, notes, status, and metadata. |
| Phase 3: Review Scheduler and Card UX | Build the first spaced review queue, card types, answer grading, due counts, daily caps, and recovery behavior. |
| Phase 4: Learning Quality Tuning | Add analytics for review burden, retention, leeches, confusing items, and translation/edit quality. |
| Phase 5: Advanced Capture | Add screenshot OCR and system-wide selection only after the core loop is demonstrably stable. |

## Critical Pitfalls

### Pitfall 1: Treating Source Context as Optional Metadata

**What goes wrong:**
Echoloom saves a target word plus a translation, but loses the original sentence, surrounding passage, language pair, selected range, provider output, and user edits. Review cards then become generic bilingual flashcards instead of memories tied to the reading moment where the word mattered.

**Why it happens:**
Vocabulary apps often model an item as `term -> meaning`, while translation apps model a request as disposable text. Echoloom sits between those models, so the data model has to preserve both the translation event and the learning item. If that is deferred, every later feature has to reconstruct context from incomplete records.

**How to avoid:**
Make context preservation part of the first capture schema, not a later library enhancement. Store at minimum: source text snippet, selected surface form, optional phrase span, translated result, source language, target language, capture timestamp, user-edited meaning, and review prompt fields derived from that context. Keep context editable and deletable, but never save an item without some source context unless the user explicitly creates a manual item.

**Warning signs:**
- The vocabulary table can be designed with only `term`, `translation`, and `created_at`.
- Review cards can be generated without opening the original sentence.
- Product demos show saved words but not the sentence or passage where they came from.
- "Context" is listed under notes or metadata rather than required capture data.

**Phase to address:**
Phase 0 defines this as a product contract; Phase 1 implements it in the translation result and save flow; Phase 3 verifies review cards actually use it.

---

### Pitfall 2: Letting Machine Translation Become the Learning Truth

**What goes wrong:**
The app treats one translation output as the definitive meaning of a word or phrase. Learners memorize a context-specific or incorrect sense, especially for short text, idioms, polysemy, named entities, domain terms, or phrases whose meaning changes with surrounding sentences.

**Why it happens:**
Translation APIs make one-shot translation feel authoritative. DeepL's API documentation explicitly notes that short, low-context texts benefit from additional context, and that multiple `text` entries are translated independently unless context is provided. Google Cloud Translation exposes glossaries because domain terms and named entities need consistent handling. For a learning app, these are not enterprise-only features; they point to a core product risk.

**How to avoid:**
Design translation output as a starting point that can be edited and contextualized. Use full source context when translating selected words or phrases. Preserve both the selected phrase and the sentence-level translation. Add an edit step for saved meanings before or after capture. In early MVP, prefer honest UI labels like "meaning in this context" over universal labels like "definition." Defer glossary automation until there is enough usage, but keep a glossary-compatible data model.

**Warning signs:**
- Selecting a word triggers a second isolated word translation without passing the sentence.
- The save button stores the raw API result with no edit path.
- The UI shows a single meaning for a polysemous word with no "in this context" framing.
- Named entities, product names, or borrowed words are translated when users expected them to remain stable.

**Phase to address:**
Phase 1 handles context-aware translation and editable capture; Phase 2 adds user corrections and duplicate/sense management; Phase 4 audits translation edits and confusing review items.

---

### Pitfall 3: Breaking Phrase Selection with Naive Word Boundaries

**What goes wrong:**
Users cannot reliably select useful vocabulary units. The app splits contractions, hyphenated terms, multiword expressions, Chinese/Japanese text, Thai/Lao/Khmer/Myanmar scripts, emoji-adjacent text, or combining marks incorrectly. Saved items become fragments that do not map cleanly back to the source text or translation.

**Why it happens:**
English whitespace splitting looks sufficient in a prototype. Unicode Standard Annex #29 warns that default segmentation cannot resolve all language ambiguity and that reliable boundaries for languages such as Thai, Lao, Chinese, and Japanese need more sophisticated mechanisms. MDN documents `Intl.Segmenter` as the browser-level locale-sensitive segmentation API, but even that should be treated as a baseline, not a complete phrase detector.

**How to avoid:**
Use locale-aware segmentation for default word suggestions, but let the user adjust the selected span manually. Store exact source offsets and selected text, not only a normalized token. Treat phrases as first-class saved items from the beginning. Test with mixed scripts, CJK, Thai-like no-space text, apostrophes, hyphens, diacritics, and emoji. Do not build the library schema around a single "word" field.

**Warning signs:**
- Selection code uses `split(" ")`, regular expressions for `\w+`, or ASCII assumptions.
- The data model calls every saved item a word and has no phrase span.
- CJK or Thai examples are absent from test data.
- Editing the selected phrase after save breaks the link to source context.

**Phase to address:**
Phase 1 stores exact source spans; Phase 2 builds phrase-aware selection and vocabulary item editing; Phase 4 adds multilingual regression tests based on observed failures.

---

### Pitfall 4: Creating Review Overload Before Users Trust the Loop

**What goes wrong:**
Early users save too many items from translation sessions, then abandon the app when daily reviews spike. The review queue feels punitive, and users blame spaced repetition rather than the capture policy and scheduler design.

**Why it happens:**
Saving words from translation is high-volume by nature. Anki's manual warns that consistently adding 20 new cards per day can lead to roughly 200 daily reviews, and that many users get excited, add many cards early, then become overwhelmed. It also documents that higher desired retention creates shorter intervals and more reviews, with workload rising quickly above 90%.

**How to avoid:**
Build daily intake and review caps into the MVP. Show the future review impact of saving many items. Default to a conservative scheduler and avoid promising perfect retention. Include suspend/archive and "not worth learning" actions. Keep the first algorithm simple, but record review history so a later FSRS-style scheduler can be adopted without losing data. Do not optimize for streaks or new-card count as primary success metrics.

**Warning signs:**
- Product metrics celebrate saved-item count without review completion or overdue burden.
- There is no daily new-item limit, no suspend action, and no due forecast.
- The roadmap says "basic spaced repetition" but not "review workload control."
- Users can import or save dozens of items with one click into active review.

**Phase to address:**
Phase 3 must ship with daily caps, review history, due counts, and suspend/archive; Phase 4 tunes retention and workload using real review data.

---

### Pitfall 5: Generating Bad Cards from Good Translations

**What goes wrong:**
The translation result is useful, but the review card is not. Cards ask for too much, mix several facts, test passive recognition only, or force users to memorize a whole translated sentence. Learners fail cards because prompts are ambiguous, not because they forgot the word.

**Why it happens:**
Turning a translation into a durable memory requires card formulation, not just scheduling. SuperMemo's learning guidance emphasizes simple, unambiguous items, minimum information, examples, prioritization, and interference reduction. Vocabulary learning research also shows that spacing can help explicit form-meaning mapping, but multiple contextual encounters are needed; a single bilingual lookup does not create complete word knowledge.

**How to avoid:**
Define a small set of card templates before implementation: recognition from context, recall of meaning in context, phrase meaning, and optional production later. One saved item should be able to generate one or more simple prompts, but MVP should start with the least ambiguous prompt. Keep the original sentence visible or revealable. Add an "ambiguous/confusing" answer path that sends an item back for editing instead of repeatedly failing it.

**Warning signs:**
- The review card front is a long paragraph or entire translation result.
- One card asks for spelling, meaning, pronunciation, and usage at once.
- Users fail the same item repeatedly but there is no edit or suspend workflow.
- Review success is measured only by button taps, not by leech/confusion rate.

**Phase to address:**
Phase 0 defines supported review modes; Phase 3 implements the first card templates and edit-from-review path; Phase 4 tracks leeches and prompt ambiguity.

---

### Pitfall 6: Building a Translator with a Word List Attached

**What goes wrong:**
The translation flow works and the vocabulary library works, but they feel like separate products. Users translate, move on, and rarely return for review. The app validates as a translator, not as a learning layer.

**Why it happens:**
Translation gives immediate value, while memory review gives delayed value. If the roadmap builds translation first and postpones review integration too long, product decisions drift toward translator convenience. Echoloom's own project principle says the learning loop is the product, so this drift would undermine the core value.

**How to avoid:**
Every translation phase should end with a saved-item and review implication. The MVP should have one end-to-end path before polishing translator features: translate text, select phrase, save with context, see it in the library, review it when due. Put review entry points near translation history and saved items. Avoid adding broad translator features until they strengthen capture or review.

**Warning signs:**
- Roadmap phases ship translation history, formatting, or provider switching before the first review queue.
- Saved vocabulary is accessible only from a separate page with no return path to the source translation.
- Users can use the app for days without seeing review prompts.
- Success criteria are translation latency and language coverage only.

**Phase to address:**
Phase 0 defines the end-to-end loop as the MVP acceptance test; Phases 1-3 should each preserve the path from translation to saved item to review.

---

### Pitfall 7: Mishandling Sensitive Text and Retention

**What goes wrong:**
Users paste private messages, work documents, medical/legal text, or personal notes into translation. The app stores full passages, screenshots, translations, and review history indefinitely without clear controls. Later privacy cleanup becomes difficult because context is already embedded across vocabulary, review, search, and analytics.

**Why it happens:**
Translation apps process arbitrary user text, and vocabulary apps intentionally retain learning history. Echoloom combines both. Google Cloud Translation's data usage FAQ says API text is used to provide the service and not to train Google Translation features, but regional processing depends on endpoint choice. DeepL's API materials also distinguish paid/API data handling from general product behavior. Provider policy does not remove Echoloom's responsibility for its own retention, deletion, logging, and display choices.

**How to avoid:**
Set a privacy baseline before choosing providers. Minimize stored context to the smallest useful snippet by default. Give users delete controls for translation history, saved context, and review data. Avoid logging raw source text in application logs or analytics. Track provider, region, and data-use assumptions in configuration and documentation. For screenshots/OCR, require a stricter retention and redaction pass before launch.

**Warning signs:**
- Raw translation requests appear in debug logs, analytics events, or error reports.
- There is no delete path for source context once it is attached to vocabulary.
- Provider choice is made only on quality/price, with no data-use review.
- Screenshot/OCR is planned without a separate privacy checkpoint.

**Phase to address:**
Phase 0 sets retention and logging rules; Phase 1 implements text minimization and delete paths; Phase 5 repeats the review before OCR/system-wide capture.

---

### Pitfall 8: Prematurely Adding Screenshot OCR or System-Wide Selection

**What goes wrong:**
The roadmap jumps from in-app text translation to screenshot capture, OCR, or global selection before the translation-to-review loop is stable. The team spends time on permissions, capture UX, OCR errors, platform differences, and layout extraction while the core learning behavior remains unvalidated.

**Why it happens:**
Screenshot and system-wide capture are attractive because they expand input sources. But they also multiply failure modes: image preprocessing, text recognition quality, language detection, coordinate mapping, privacy risk, and platform permissions. Echoloom's own scope already defers these features, and research supports focusing first on low-friction contextual learning rather than broad capture surfaces.

**How to avoid:**
Keep advanced capture explicitly out of the MVP. Gate it on evidence that users save items, return for reviews, and maintain tolerable review loads from plain text translation. When OCR begins, treat it as a new capture channel that must feed the same context schema and review queue, not as a separate translator feature.

**Warning signs:**
- OCR appears before review completion and retention metrics exist.
- Screenshot capture has its own saved-item model or history separate from text translation.
- The team debates platform permissions before phrase selection and review cards are working.
- OCR demos focus on extracting text, not saving and reviewing useful vocabulary.

**Phase to address:**
Phase 5 only, after Phase 4 shows the core loop is used and review burden is manageable.

---

### Pitfall 9: Letting the Vocabulary Library Become a Dumping Ground

**What goes wrong:**
The library accumulates duplicates, near-duplicates, inflected forms, phrases with overlapping meanings, items the user no longer cares about, and unreviewable fragments. Search and grouping exist, but the library does not help users decide what deserves memory work.

**Why it happens:**
Translation-first capture makes saving cheap. Without triage, every unknown word feels worth saving. SRS systems require prioritization because learners can create more items than they can retain. SuperMemo's guidance highlights prioritization and interference; those concerns show up quickly in language decks where similar meanings and forms collide.

**How to avoid:**
Make item status part of the library MVP: active, paused, learned, archived, needs edit. Add duplicate detection based on language pair, normalized form, surface form, phrase span, and context. Support merging and splitting. Let users save to library without immediately activating review if they are unsure. Add difficulty and notes, but avoid overbuilding taxonomy before the basic triage workflow works.

**Warning signs:**
- Every saved item automatically enters review.
- There is no archive, pause, merge, or duplicate warning.
- Search works, but users cannot tell which items are active or due.
- Similar words repeatedly interfere in review with no library-level cleanup path.

**Phase to address:**
Phase 2 establishes item status and duplicate handling; Phase 3 respects status in the scheduler; Phase 4 surfaces interference and leech clusters.

---

### Pitfall 10: Missing Learning Outcome Instrumentation

**What goes wrong:**
The product can report translations, saves, and reviews, but cannot answer whether Echoloom is helping memory. Roadmap decisions then optimize visible activity rather than retention, review sustainability, or card quality.

**Why it happens:**
MVPs often track product events before learning events. A spaced review app needs different signals: due count, overdue count, review completion, answer distribution, lapse rate, item age, suspended items, confusing prompts, and time-to-review. Without these, the team cannot distinguish bad translation quality from bad card formulation or bad scheduling.

**How to avoid:**
Define learning metrics before shipping review. Store review events with item id, card template, answer grade, due date, previous interval, next interval, and edit/suspend actions. Keep analytics privacy-safe by avoiding raw text payloads. Add simple dashboards or development reports before tuning the algorithm.

**Warning signs:**
- Events include `translation_created` and `item_saved`, but review events lack grade or interval data.
- There is no way to identify overdue backlogs or repeated failures.
- Product decisions rely on total saved words or streaks.
- Scheduler changes cannot be compared because historical review state is missing.

**Phase to address:**
Phase 3 records review history; Phase 4 uses it for learning quality tuning.

## Technical Debt Patterns

Shortcuts that seem reasonable but create long-term problems.

| Shortcut | Immediate Benefit | Long-term Cost | When Acceptable |
|----------|-------------------|----------------|-----------------|
| Save only `term` and `translation` | Fastest possible library schema | Contextual review, duplicate handling, phrase spans, and translation provenance require migration | Never for user-created learning items; acceptable only for throwaway UI prototypes |
| Use whitespace or ASCII regex tokenization | Quick English demo | Breaks selection for multilingual text and corrupts saved spans | Only for non-shipping mockups |
| Translate selected words independently from the sentence | Simple API call | Wrong senses are memorized; phrase meaning is lost | Only when the full source sentence is unavailable and UI marks it as low-context |
| Auto-activate every saved item into review | Simple mental model | Review overload and abandoned queues | Avoid by default; allow manual "activate all" only with warning |
| Implement an ad hoc scheduler without review history | Easy MVP | Later algorithm upgrade cannot use historical memory state | Acceptable only if every review event still stores enough data for migration |
| Store raw source text in logs for debugging | Faster troubleshooting | Privacy cleanup and user trust problems | Never; use redacted IDs and local-only debugging |
| Add OCR before capture schema stabilizes | Impressive demo | OCR creates a second, incompatible capture model | Never before Phase 5 |

## Integration Gotchas

Common mistakes when connecting translation, segmentation, and review services.

| Integration | Common Mistake | Correct Approach |
|-------------|----------------|------------------|
| Translation API | Sending selected words or phrases without surrounding source context | Send sentence-level or passage-level context when available; label saved meaning as context-specific |
| Translation API | Relying on automatic language detection for short snippets | Preserve user-selected or document-level source language and allow correction |
| Translation API | Ignoring glossary/domain term behavior | Keep provider output editable; design future glossary fields even if glossaries are deferred |
| Translation API billing | Counting only direct translation text and ignoring context/request limits | Track characters sent, context size, provider limits, and cost per saved item |
| Unicode/browser selection | Treating code units as characters or words | Use grapheme-aware and locale-aware segmentation; store exact offsets plus selected text |
| SRS scheduler | Changing intervals without preserving prior review events | Store answer grade, interval, due date, and card template for every review |
| Analytics | Logging raw source snippets or translations | Emit text-free events and keep content in user-controlled storage |

## Performance Traps

Patterns that work at small scale but fail as usage grows.

| Trap | Symptoms | Prevention | When It Breaks |
|------|----------|------------|----------------|
| Re-translating every saved item on display | Library and review screens slow down; provider cost rises | Store translation result and user edits; refresh only on demand | Hundreds of saved items or repeated review sessions |
| Loading full translation history into review/library views | Slow search, memory-heavy UI, privacy exposure | Query by item, source, status, due date; paginate history | Thousands of translations or long reading sessions |
| Scheduler computes due cards by scanning all items client-side | Slow startup and battery drain | Index by due date/status; compute daily queue incrementally | Thousands of active items |
| No cap on translation request size | Slow API calls, poor UX, high cost | Define MVP text limits and chunking rules | Long pasted articles |
| OCR pipeline shares the same synchronous path as text translation | UI blocks and retry behavior becomes complex | Treat OCR as asynchronous capture with reviewable output only after validation | First screenshot feature |

## Security and Privacy Mistakes

Domain-specific issues beyond general web security.

| Mistake | Risk | Prevention |
|---------|------|------------|
| Raw source text in logs, traces, crash reports, or analytics | Private user content leaks outside expected storage | Redact content by default; log IDs, lengths, language pair, and provider status only |
| No deletion model for context attached to vocabulary | User cannot remove sensitive source text without losing all learning records | Separate item identity, context snippet, and review history enough to delete or minimize context |
| Provider terms reviewed after implementation | Rework if selected API retention, region, or billing model does not fit | Complete provider data-use review in Phase 0 |
| Screenshot capture without consent boundaries | Accidental capture of sensitive on-screen content | Gate OCR behind explicit capture, preview, crop, and delete controls |
| Cross-device sync stores unencrypted learning content | Vocabulary and source passages become sensitive replicated data | Decide encryption/sync posture before production sync; MVP can stay local-only if needed |

## UX Pitfalls

Common user experience mistakes in this domain.

| Pitfall | User Impact | Better Approach |
|---------|-------------|-----------------|
| Save flow is too hidden | Translation remains one-time comprehension | Put save actions directly on selectable result spans and show context preview |
| Save flow is too aggressive | Library fills with low-value items | Let users stage items, edit meaning, and choose active vs paused |
| Review prompt hides context entirely | Learner memorizes isolated translations | Use context as prompt or revealable support, depending on card mode |
| Review prompt always shows context | Learner never practices recall | Start with recognition/context cards, then add recall mode when card quality is stable |
| No "this card is bad" path | Users repeatedly fail ambiguous prompts | Add edit, suspend, and "confusing" actions during review |
| Translation history and library are separate silos | Users cannot trace a word back to the reading moment | Link every saved item to its source context and translation event |
| Due count grows silently | Users return to an intimidating queue | Show manageable daily goals, caps, and backlog recovery options |

## "Looks Done But Isn't" Checklist

Things that appear complete but are missing critical pieces.

- [ ] **Text translation:** Works for pasted text, but verify language pair correction, provider error handling, context size limits, and no raw-text logging.
- [ ] **Word selection:** Works for English, but verify CJK, Thai/no-space scripts, apostrophes, hyphens, combining marks, emoji-adjacent text, and manual phrase adjustment.
- [ ] **Save to library:** Saves a term, but verify source sentence, selected span, translated meaning, user edit, language pair, status, and duplicate handling.
- [ ] **Vocabulary library:** Search works, but verify active/paused/archived status, duplicate merge, edit history, and source-context traceability.
- [ ] **Basic SRS:** Cards appear on schedule, but verify daily caps, overdue behavior, review history, answer grades, suspend/archive, and due forecast.
- [ ] **Review cards:** Prompt displays, but verify ambiguity, one learning target per card, context reveal, edit-from-review, and leech detection.
- [ ] **Analytics:** Events exist, but verify they measure review completion, answer distribution, lapse rate, overdue burden, and card edits without raw text.
- [ ] **OCR/system-wide capture:** Demo extracts text, but verify privacy controls, context schema compatibility, OCR confidence/editing, and review integration before shipping.

## Recovery Strategies

When pitfalls occur despite prevention, how to recover.

| Pitfall | Recovery Cost | Recovery Steps |
|---------|---------------|----------------|
| Context was not saved | HIGH | Freeze new schema changes; add context fields; preserve existing items as manual/decontextualized; prompt users to enrich high-value items rather than guessing missing context |
| Translation meanings are wrong or too broad | MEDIUM | Add edit/relabel flow; mark existing meanings as provider-generated; prioritize confusing or failed items for review/edit |
| Selection spans are corrupted | HIGH | Store raw selected text going forward; add migration only where offsets still match; add multilingual segmentation tests |
| Review overload | MEDIUM | Pause new-item activation; add daily caps, backlog recovery, suspend/archive; show users the reduced queue explicitly |
| Bad card templates | MEDIUM | Add edit-from-review and suspend; split complex cards; ship one reliable template before adding modes |
| Privacy issue in logs or analytics | HIGH | Stop logging raw content; rotate/delete affected logs where possible; document incident scope; add redaction tests |
| Library duplicates and interference | MEDIUM | Add merge/suspend/status; cluster similar items; prioritize leech cleanup in review |
| OCR added too early | HIGH | Stop expanding capture surfaces; route OCR output through the same save/review flow; complete privacy and quality gates before further work |

## Pitfall-to-Phase Mapping

How roadmap phases should address these pitfalls.

| Pitfall | Prevention Phase | Verification |
|---------|------------------|--------------|
| Treating source context as optional metadata | Phase 0, Phase 1 | Saved item fixture cannot be valid without source context or an explicit manual-item flag |
| Letting machine translation become the learning truth | Phase 1, Phase 2 | Selected phrase translation uses sentence context where available and saved meaning is editable |
| Breaking phrase selection with naive word boundaries | Phase 1, Phase 2 | Multilingual selection tests pass; manual span adjustment works; offsets remain traceable |
| Creating review overload before users trust the loop | Phase 3, Phase 4 | Daily caps, due forecast, suspend/archive, and review completion metrics exist |
| Generating bad cards from good translations | Phase 0, Phase 3, Phase 4 | Review cards have one target, context support, edit-from-review, and leech tracking |
| Building a translator with a word list attached | Phase 0 through Phase 3 | End-to-end UAT proves translate -> select -> save -> review in one loop |
| Mishandling sensitive text and retention | Phase 0, Phase 1, Phase 5 | Raw text is absent from logs; delete/minimize context works; provider data-use review exists |
| Prematurely adding screenshot OCR or system-wide selection | Phase 5 | OCR/system-wide work is blocked until core loop metrics are available |
| Letting the vocabulary library become a dumping ground | Phase 2, Phase 3, Phase 4 | Items have active/paused/archived states; duplicates can be merged; inactive items do not enter review |
| Missing learning outcome instrumentation | Phase 3, Phase 4 | Review events include grade, due date, interval, card template, and edit/suspend actions |

## Roadmap Implications

1. Build the data contract before UI polish. The first roadmap phase should lock what a saved vocabulary item must contain, especially context, language pair, span, provider output, user edits, and privacy rules.
2. Ship the core loop before advanced capture. The first usable MVP should prove text translation -> phrase selection -> context-rich save -> review, before screenshot OCR or system-wide selection.
3. Treat review workload as a product requirement, not a scheduler detail. Daily caps, pause/archive, and due visibility belong in the first review phase.
4. Keep translation editable and contextual. The app should teach "meaning in this context" first, not pretend every translation result is a complete dictionary entry.
5. Instrument learning quality early. Without review history and card-quality signals, later roadmap decisions will optimize activity rather than memory.

## Sources

- Local project context: `/Users/2black/workspace/y1sifer/echoloom/.planning/PROJECT.md` and `/Users/2black/workspace/y1sifer/echoloom/README.md` (HIGH for Echoloom scope and constraints).
- Anki Manual, "Deck Options" and FSRS/daily limits: https://docs.ankiweb.net/deck-options (HIGH for SRS workload and FSRS configuration behavior).
- SuperMemo, "Twenty rules of formulating knowledge": https://www.supermemo.com/en/blog/twenty-rules-of-formulating-knowledge (HIGH for long-standing SRS item formulation principles; older source, still foundational).
- DeepL API documentation, "Translate Text": https://developers.deepl.com/api-reference/translate (HIGH for context parameter and independent text translation behavior).
- Google Cloud Translation API overview: https://docs.cloud.google.com/translate/docs/api-overview (HIGH for glossary, model, and advanced translation features).
- Google Cloud Translation data usage FAQ: https://docs.cloud.google.com/translate/data-usage (HIGH for provider data-use and regional endpoint constraints; last updated 2026-04-22).
- Unicode Standard Annex #29, Unicode Text Segmentation, Version 17.0.0: https://www.unicode.org/reports/tr29/ (HIGH for segmentation limitations and tailoring requirements).
- MDN Web Docs, `Intl.Segmenter`: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/Segmenter (MEDIUM for browser API availability and usage; official-enough platform reference).
- Nakata and Elgort, "Effects of spacing on contextual vocabulary learning" (Second Language Research, 2021): https://journals.sagepub.com/doi/10.1177/0267658320927764 (MEDIUM-HIGH for spacing/contextual vocabulary learning evidence).
- Aydin et al., "Broccoli: Sprinkling Lightweight Vocabulary Learning into Everyday Information Diets" (The Web Conference 2020): https://arxiv.org/abs/2104.07941 (MEDIUM for embedded contextual vocabulary learning pattern; prototype research).

---
*Pitfalls research for: Echoloom translation-first vocabulary learning app*
*Researched: 2026-04-24*
