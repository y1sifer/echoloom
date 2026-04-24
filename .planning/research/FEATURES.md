# Feature Research

**Domain:** Translation-first vocabulary learning app
**Project:** Echoloom
**Researched:** 2026-04-24
**Confidence:** MEDIUM

## Feature Landscape

Echoloom should compete on one narrow loop: translate text, select useful words or phrases, save them with the source context, and review them before they fade. Current products around this space converge on click-to-translate, vocabulary capture, contextual examples, and spaced review. The clearest gap for Echoloom is not "more translator features"; it is making the translation moment reliably become a memory item without forcing users into a separate flashcard workflow.

For v1, treat the README MVP as the product boundary. Screenshot translation, system-wide selection, browser extensions, video subtitles, AI chat, rich gamification, and advanced SRS customization are useful in the broader ecosystem, but they add capture surfaces before the core loop is proven.

### Table Stakes (Users Expect These)

Features users assume exist. Missing these means the product feels incomplete for the MVP promise.

| Feature | Why Expected | Complexity | Notes |
|---------|--------------|------------|-------|
| Text translation input | The app starts from translation, so users must paste or type source text and get a usable translation. | MEDIUM | Include source/target language selection and a clear translated result. Auto-detect is useful but not essential for the first validation pass. |
| Word and phrase selection from translation results | Readlang, LingQ, Reverso, Migaku, and similar products reduce friction by letting users act on words in context instead of copying text elsewhere. | MEDIUM | Support multi-word phrase selection from the visible translation result. Avoid building system-wide selection in v1. |
| Save selected item to vocabulary library | The core learning moment is capture. If saving takes more than one obvious action, users will keep translating and forget to study. | MEDIUM | Save the term/phrase, meaning, language pair, and timestamp. Keep the save form minimal; allow later edit. |
| Preserve source context with each saved item | This is Echoloom's core value and is also a common pattern in context-first tools: flashcards are stronger when they retain the sentence or source where the word appeared. | MEDIUM | Store the original source text, source sentence if detectable, translated sentence/result, and selected span. This is not optional metadata. |
| Basic vocabulary library | Users need somewhere to find, correct, and prune saved items. Readlang provides a vocab manager; Reverso has favorites/history; LingQ has vocabulary pages and filters. | MEDIUM | v1 should include list, search, edit, delete, item status, difficulty, and notes. Keep grouping/tagging shallow. |
| Basic spaced review queue | Translation capture without scheduled review does not satisfy the memory promise. SRS appears across Readlang, LingQ, Reverso, FluentU, Migaku, Anki-style workflows, and research on digital flashcards. | MEDIUM | Use a simple scheduler with due dates and "remembered / forgot" or "easy / hard" feedback. Do not expose algorithm configuration in v1. |
| Context-first review card | Generic front/back flashcards weaken the product distinction. Users should review the saved item with its original sentence available. | MEDIUM | v1 can use recognition cards: show target term or context sentence, reveal meaning/context, then collect rating. Cloze recall can wait. |
| Edit/correct saved meanings | Machine translation and contextual examples can be wrong, ambiguous, or too broad. Users need to correct meanings and notes. | LOW | Add inline edit for term, meaning, context, note, and difficulty. Avoid complex dictionary editing. |
| Daily due count and review entry point | SRS only works if users know what to do next. | LOW | A simple "Review due: N" entry point is enough. Streaks, goals, and dashboards are not v1. |
| Duplicate prevention | Translation-first capture creates repeat lookups. Without duplicate handling, the library becomes noisy quickly. | MEDIUM | Detect same normalized term/phrase in same language pair; offer update/merge rather than creating silent duplicates. |
| Fast, quiet interaction design | The product principle says Echoloom should feel like a quiet learning layer over reading. Translation and saving must be low friction. | MEDIUM | Keep the main screen focused on input, translation, selectable result, saved items, and review. Do not start with a content feed. |

### Differentiators (Competitive Advantage)

These are valuable, but only the first two belong near v1 because they directly reinforce the MVP loop.

| Feature | Value Proposition | Complexity | Notes |
|---------|-------------------|------------|-------|
| Context-locked vocabulary items | Makes Echoloom different from a translator with a word list: every item remembers where it came from and why it mattered. | MEDIUM | Strong v1 differentiator. Store selected span, original source text, translated context, and optional note together as one item. |
| Phrase-first capture | Real learning often depends on chunks, idioms, collocations, and expressions, not isolated words. Reverso and Readlang both emphasize words and expressions/phrases. | MEDIUM | Strong v1 differentiator. Make phrase selection as easy as word selection; do not force token-level word cards only. |
| Context-aware explanation after save | Helps users understand why a term means that thing in this sentence. | HIGH | Defer until the basic save/review loop works. Add only if users are saving ambiguous items or editing meanings often. |
| Review modes by learning intent | Recognition, recall, and context understanding let users practice at different depths. LingQ supports multiple review activity types. | HIGH | Defer beyond v1. Start with one review mode; add cloze/context recall after review usage exists. |
| Usefulness triage | Readlang mentions prioritizing words by usefulness; learners do not want every translated word in review. | MEDIUM | v1 can approximate with manual save only. Later add "ignore", "known", or "low priority" states if users over-save. |
| Re-encounter surfacing | Showing previously saved words when they appear again reinforces contextual memory. | HIGH | Defer until there is enough reading/translation history. This becomes powerful after a user has a library. |
| Import/export bridge to Anki | Many serious learners already use Anki. Export is useful but can turn Echoloom into card-management tooling. | MEDIUM | Defer to v1.x. CSV export is enough before native Anki sync. |
| Language-specific helpers | Pronunciation, conjugation, morphology, transliteration, or inflection normalization can be highly valuable for specific languages. | HIGH | Defer until target languages and user needs are clear. Avoid building a universal grammar engine in v1. |
| Ability-matched content suggestions | Migaku and LingQ differentiate with content/import ecosystems and comprehension scoring. | HIGH | v2+ only. Echoloom should first prove translation-to-review, not become a reader/content platform. |

### Anti-Features (Commonly Requested, Often Problematic)

Features that seem attractive but would distract from a focused MVP.

| Feature | Why Requested | Why Problematic | Alternative |
|---------|---------------|-----------------|-------------|
| Screenshot translation and OCR in v1 | It feels like a natural capture surface for real-world reading. | OCR introduces image capture, text detection, layout cleanup, permissions, and error handling before the learning loop is validated. | Defer until text translation, selection, saving, and review retention are working. |
| System-wide selection translation in v1 | Users want to translate anywhere. | Browser/OS integration multiplies platform complexity and shifts focus from product learning behavior to capture plumbing. | Start with in-app text selection from translation results. Add extensions later if users keep pasting from other apps. |
| Video/subtitle learning in v1 | Competitors like LingQ, Language Reactor, Migaku, and FluentU use media effectively. | It requires subtitle alignment, playback, timestamps, audio snippets, and media-specific card formats. | Keep v1 text-only. Use saved source context rather than media context. |
| AI conversation tutor | It is popular in language apps and can produce lots of engagement. | It is a separate learning product and does not validate translation-first vocabulary memory. | Use AI later for contextual explanation or card cleanup, not chat. |
| Full dictionary/grammar suite | Reverso offers synonyms, conjugation, definitions, filters, and contextual examples. | Building a dictionary product expands data requirements and UI scope. | Store the translated meaning and allow manual correction. Link out or add lightweight details later. |
| Advanced SRS controls | Power users may ask for FSRS/SM-2 settings, deck options, leech handling, and custom intervals. | It turns the MVP into an Anki clone and raises support burden. | Ship a simple due-date scheduler with user feedback. Hide algorithm internals. |
| Heavy gamification and streak systems | LingQ, Duolingo-style products, and mobile learning apps use goals/streaks to drive habit. | It can optimize for app opens rather than durable recall, and it adds state that is not essential to the core loop. | Show due count and completion state only. Add goals after review engagement is proven. |
| Social/classroom features | LingQ for Schools shows classroom tracking and forums can exist in the category. | Echoloom is not yet validating teacher workflows, cohorts, or shared content. | Keep vocabulary private and personal in v1. |
| Rich content library/import platform | Competitors succeed with imported articles, videos, and lessons. | Content ingestion becomes the product. Echoloom's current concept is translation-first capture, not content hosting. | Accept pasted text first. Later add import only if users ask for repeated reading workflows. |
| Auto-saving every translated word | It promises effortless vocabulary building. | It floods review with low-value terms and makes the library noisy. | Require intentional save. Later add ignore/known/priority states if needed. |
| Permanent translation history as a primary feature | History feels useful for retrieval. | It can become a cluttered archive, privacy liability, and UI distraction from saved learning items. | Keep recent session state in v1; persist only saved vocabulary and its required context. |

## Feature Dependencies

```text
Text translation
    -> selectable translation result
        -> save selected word/phrase
            -> vocabulary item with source context
                -> vocabulary library management
                    -> basic spaced review queue

Vocabulary item schema
    -> duplicate prevention
    -> edit/correct meanings
    -> review scheduling

Review feedback
    -> next due date
    -> item status/difficulty

Saved context
    -> context-first review card
    -> later contextual explanation
    -> later cloze/context recall

Stable core loop
    -> screenshot/OCR
    -> system-wide selection
    -> browser extension
    -> media/subtitle learning
```

### Dependency Notes

- **Text translation must come first:** selection, saving, and review have no useful object without a translation result.
- **Selection requires stable result rendering:** users need predictable word/phrase boundaries and clear visual feedback before saving.
- **Vocabulary item schema gates everything:** store term, meaning, language pair, source context, translated context, note, difficulty/status, created date, and due date early; migrations around review data become painful later.
- **Review requires intentional capture:** auto-saving every token creates low-quality review debt.
- **Context preservation must be designed before review:** adding context later risks orphaned cards that cannot deliver the product promise.
- **Screenshot/OCR and system-wide selection depend on a proven core loop:** they should expand capture surfaces only after the product knows what to do with captured items.

## MVP Definition

### Launch With (v1)

Minimum viable product needed to validate the concept.

- [ ] Text translation input and output - proves users can start from the translation task.
- [ ] Word/phrase selection from the translation result - creates the capture moment without copy/paste.
- [ ] One-action save into vocabulary library - turns a translated item into a learning item.
- [ ] Source context preservation - stores the sentence/text that made the item meaningful.
- [ ] Vocabulary library list with search, edit, delete, status, difficulty, and notes - lets users maintain quality.
- [ ] Basic spaced review queue - brings saved items back using due dates.
- [ ] Simple context-first review card - tests whether saved translation context helps memory.
- [ ] Duplicate detection for saved items - keeps the library from becoming noisy.

### Add After Validation (v1.x)

Add only when users have saved enough vocabulary and completed enough reviews to expose real needs.

- [ ] Tags or lightweight grouping - add if search/status are not enough to manage larger libraries.
- [ ] Translation history for recent sessions - add if users need to recover unsaved items, but keep it secondary.
- [ ] Context-aware explanation - add if users frequently edit meanings or save ambiguous phrases.
- [ ] Cloze/context recall review mode - add if basic recognition review is too shallow.
- [ ] CSV/Anki export - add if early users already maintain external decks.
- [ ] Known/ignore/priority states - add if users over-save or need triage.
- [ ] Pronunciation/TTS for saved items - add when target languages and review mode justify audio.

### Future Consideration (v2+)

Defer until product-market fit for the translation-to-memory loop is established.

- [ ] Screenshot translation with OCR - valuable capture surface, high implementation and QA cost.
- [ ] System-wide selection translation or browser extension - useful once in-app capture behavior is proven.
- [ ] Article/web reader or content import - risks becoming a LingQ/Readlang competitor before the narrow loop is validated.
- [ ] Video/subtitle learning - powerful but requires media-specific architecture.
- [ ] AI tutor/conversation mode - separate product loop.
- [ ] Advanced SRS algorithm configuration - only needed for power users.
- [ ] Social/classroom features - not aligned with a personal MVP.

## Feature Prioritization Matrix

| Feature | User Value | Implementation Cost | Priority |
|---------|------------|---------------------|----------|
| Text translation input/output | HIGH | MEDIUM | P1 |
| Word/phrase selection from translation result | HIGH | MEDIUM | P1 |
| Save selected item to vocabulary library | HIGH | MEDIUM | P1 |
| Source context preservation | HIGH | MEDIUM | P1 |
| Vocabulary library list/search/edit/delete | HIGH | MEDIUM | P1 |
| Basic spaced review queue | HIGH | MEDIUM | P1 |
| Context-first review card | HIGH | MEDIUM | P1 |
| Duplicate prevention | MEDIUM | MEDIUM | P1 |
| Item status/difficulty/notes | MEDIUM | LOW | P1 |
| Tags/light grouping | MEDIUM | MEDIUM | P2 |
| Translation history | MEDIUM | MEDIUM | P2 |
| Context-aware explanation | MEDIUM | HIGH | P2 |
| Cloze/context recall review | MEDIUM | HIGH | P2 |
| CSV/Anki export | MEDIUM | MEDIUM | P2 |
| Pronunciation/TTS | MEDIUM | MEDIUM | P2 |
| Screenshot/OCR | MEDIUM | HIGH | P3 |
| System-wide/browser selection | MEDIUM | HIGH | P3 |
| Video/subtitle learning | MEDIUM | HIGH | P3 |
| AI conversation tutor | LOW for MVP | HIGH | P3 |
| Social/classroom features | LOW for MVP | HIGH | P3 |

**Priority key:**
- P1: Must have for launch validation
- P2: Should have after usage reveals need
- P3: Future consideration; avoid during focused MVP

## Competitor Feature Analysis

| Feature Area | Readlang | Reverso Context | LingQ | Migaku / FluentU | Echoloom Approach |
|--------------|----------|-----------------|-------|------------------|-------------------|
| Translation in context | Fast inline translation and AI context-aware explanations. | Contextual translation examples, filters, larger context, reverse direction checks. | Line-by-line translations inside lessons. | Click/hover lookup in websites, subtitles, or videos. | Start with text translation and preserve original context for saved items. |
| Vocabulary capture | Translated words become flashcards; vocab manager supports edit/delete/star/export. | Favorites and search history feed personalized learning activities. | Saved LingQs go to vocabulary pages with status and filters. | One-click cards from websites/subtitles with context sentence and media. | Save selected words/phrases intentionally from translation results. |
| Review | Flashcards with adaptive spaced repetition. | Quizzes, flashcards, games based on searches/favorites. | SRS due review plus multiple activity types. | SRS flashcards; FluentU uses "Ready for Review" based on a SuperMemo SM-2 variation. | Basic due queue and context-first review, no advanced controls in v1. |
| Content surface | eReader, web reader, video player. | Web, mobile, browser selection, speech/photo in app. | Content library, imports, reader/listening modes. | Browser/video/web learning environments. | In-app text translation only for v1. Add capture surfaces after core loop validation. |
| Library management | Look back over translated items, edit/delete/star/export. | History and favorites sync across devices. | Advanced vocabulary filtering, status, review modes. | Known word tracking and comprehension scoring. | Search/edit/delete/status/difficulty/notes; avoid deep dashboards until needed. |

## Product Recommendations

1. **Define v1 around saved vocabulary quality, not translation breadth.** Translation quality matters, but Echoloom's product value is that the looked-up item becomes reviewable memory with context.
2. **Make phrase capture first-class.** Translation-first learning should support expressions and collocations from day one; single-word-only cards will feel too limited.
3. **Use intentional saving instead of automatic mining.** This protects the review queue from noise and keeps user agency clear.
4. **Ship the simplest useful SRS.** A basic due-date scheduler is enough to validate retention behavior. Avoid algorithm debates until users are reviewing regularly.
5. **Defer every new capture surface.** Screenshot/OCR, browser selection, and media import are expansion layers; they should not compete with core loop implementation.

## Sources

- [HIGH] Readlang Features - fast inline translations, translated words to flashcards, adaptive spaced repetition, vocab manager, Anki export: https://readlang.com/features
- [HIGH] Reverso Context About - contextual examples, favorites/history, personalized learning, flashcards/quizzes/games, pronunciation, filters, larger context, error reporting: https://context.reverso.net/translation/about
- [HIGH] LingQ for Schools - content import, vocabulary in context, integrated dictionaries, SRS tools: https://www.lingq.com/en/schools/
- [HIGH] LingQ 5.0 announcement - line-by-line translation, cleaner reader, SRS due review, vocabulary list, sentence/page/lesson review, import improvements: https://www.lingq.com/blog/introducing-lingq-5-0/
- [HIGH] LingQ vocabulary review article - vocabulary page, due review, SRS based on knowledge and last review, multiple review activities: https://www.lingq.com/blog/reviewing-vocabulary/
- [HIGH] Migaku official site - click lookup, contextual explanations, card creation with sentence/audio/screenshot/definition, SRS, comprehension scoring: https://migaku.com/
- [HIGH] FluentU support - "Ready for Review" SRS and SM-2-based scheduling: https://support.fluentu.com/en/article/what-is-the-fluentu-srs-spaced-repetition-algorithm-7qjbc6/
- [MEDIUM] Frontiers in Education 2024 mini review - mobile-assisted vocabulary learning, digital flashcards, spaced repetition, learner autonomy, long-term retention: https://www.frontiersin.org/journals/education/articles/10.3389/feduc.2024.1496578/full
- [MEDIUM] Applied Psycholinguistics 2024/2025 article - repeated exposure, context, and incidental L2 vocabulary learning through reading: https://www.cambridge.org/core/journals/applied-psycholinguistics/article/effect-of-context-on-the-processing-and-learning-of-novel-L2-vocabulary-while-reading/50E889BFB43DF44DE276AC5F3ADE8B63

## Confidence Notes

- **HIGH confidence:** Translation-first products commonly include contextual lookup, intentional vocabulary capture, saved item management, and spaced review.
- **MEDIUM confidence:** Exact v1 boundaries for Echoloom depend on target user interviews; this research infers priorities from the README MVP and current competitor patterns.
- **LOW confidence:** Language-specific helpers and future capture surfaces should not be ranked until the initial target language pair and platform are selected.

---
*Feature research for: Echoloom translation-first vocabulary learning*
*Researched: 2026-04-24*
