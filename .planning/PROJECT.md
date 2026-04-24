# Echoloom

## What This Is

Echoloom is a translation-first vocabulary learning app for language learners who want the words they look up to become lasting memory. It connects translation, vocabulary capture, original context, and spaced review into one learning loop instead of treating translation and study as separate workflows.

The first product shape is a focused MVP: translate text, select useful words or phrases from the translation result, save them with context, and review them with a basic spaced repetition schedule.

## Core Value

Words and phrases discovered through translation must be captured with their original context and reliably brought back for review.

## Requirements

### Validated

(None yet - ship to validate)

### Active

- [ ] User can translate text.
- [ ] User can select words or phrases from translation results.
- [ ] User can add selected words or phrases to a vocabulary library.
- [ ] User can preserve source context with saved vocabulary items.
- [ ] User can review saved items with a basic spaced repetition schedule.
- [ ] User can manage saved vocabulary items through search, grouping, status, difficulty, notes, and metadata.

### Out of Scope

- Screenshot translation with OCR - deferred until the core translation-to-review loop is stable.
- System-wide selection translation - deferred until the in-app selection and saving flow works reliably.
- Production release readiness - the project is still in early product definition.
- Open contribution workflow - contributions are not open yet.

## Context

Most translation tools help users understand something once, while most vocabulary apps ask users to study words outside the context where they found them. Echoloom is based on the idea that translated words are often the words the learner actually needs to remember.

The product should feel like a quiet, fast learning layer over everyday reading. It should not become a general translator with a word list attached; the learning loop is the product.

The current repository contains product definition only. No technical stack has been selected, and no production-ready implementation exists yet.

## Constraints

- **Scope**: Build the smallest useful loop first - text translation, selection, vocabulary capture, and basic review are the MVP.
- **Product principle**: Keep translation connected to memory - saved items should retain the source context where they were discovered.
- **Sequencing**: Add screenshot translation and system-wide selection translation only after the core loop is stable.
- **Technical stack**: Not selected yet - choose after project research and roadmap planning.

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Start with a translation-to-vocabulary-to-review loop | This is the smallest loop that proves Echoloom's core value. | - Pending |
| Defer screenshot translation and OCR | OCR adds capture complexity before the learning loop is proven. | - Pending |
| Defer system-wide selection translation | In-app selection is enough to validate saving and review behavior first. | - Pending |
| Treat context preservation as core, not metadata polish | The product promise depends on remembering where a word was discovered. | - Pending |

## Evolution

This document evolves at phase transitions and milestone boundaries.

**After each phase transition** (via `$gsd-transition`):
1. Requirements invalidated? -> Move to Out of Scope with reason
2. Requirements validated? -> Move to Validated with phase reference
3. New requirements emerged? -> Add to Active
4. Decisions to log? -> Add to Key Decisions
5. "What This Is" still accurate? -> Update if drifted

**After each milestone** (via `$gsd-complete-milestone`):
1. Full review of all sections
2. Core Value check - still the right priority?
3. Audit Out of Scope - reasons still valid?
4. Update Context with current state

---
*Last updated: 2026-04-24 after initialization*
