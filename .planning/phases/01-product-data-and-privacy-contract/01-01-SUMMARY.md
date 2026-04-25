---
phase: 01-product-data-and-privacy-contract
plan: 01
subsystem: product-data-privacy
tags: [contract, privacy, retention, deletion, provider-boundary, observability, learning-quality]

requires: []
provides:
  - Canonical MVP data and privacy contract
  - Locked web-first Supabase storage posture
  - Text-free logging and analytics boundary
  - Provider request minimization rules
  - Advanced capture gate and learning-quality signals
affects: [web-foundation, schema, translation-provider, vocabulary-capture, review, observability]

tech-stack:
  added: []
  patterns:
    - Canonical planning contract before implementation
    - Text-free observability allowlist
    - Provider payload allowlist and forbidden-field boundary
    - Deletion ownership rule for saved context and review data

key-files:
  created:
    - .planning/contracts/MVP-DATA-PRIVACY-CONTRACT.md
  modified: []

key-decisions:
  - "Echoloom v1 is web-first with Supabase Auth/Postgres/RLS as the system of record."
  - "Only user-saved vocabulary item context is retained long term; ordinary translation sessions are short-lived, clearable, or not retained beyond the current workflow."
  - "Production logs, analytics, traces, and error reports must remain free of raw source text, translated private text, saved context, vocabulary history, and review-history content."
  - "DeepL API Pro is the recommended first traditional translation provider for later implementation behind a server-side adapter."
  - "Screenshot/OCR, browser selection, and desktop/system-wide capture remain out of v1 until the text-only loop is complete and review burden is manageable."

patterns-established:
  - "Contract-first downstream planning: Phase 2 and later phases must cite the contract before implementing storage, provider, deletion, or observability behavior."
  - "Reviewable privacy gates: each sensitive boundary has grep-verifiable contract text and a checklist item."

requirements-completed:
  - PROD-01
  - PROD-02
  - PROD-03

duration: 4 min
completed: 2026-04-25
---

# Phase 01 Plan 01: Product, Data, and Privacy Contract Summary

**Canonical MVP contract locking the translation-to-memory acceptance path, web-first storage posture, text retention, provider payloads, deletion behavior, observability limits, and learning-quality gates.**

## Performance

- **Duration:** 4 min
- **Started:** 2026-04-25T07:55:10Z
- **Completed:** 2026-04-25T07:58:41Z
- **Tasks:** 3
- **Files modified:** 1

## Accomplishments

- Created `.planning/contracts/MVP-DATA-PRIVACY-CONTRACT.md` as the canonical contract artifact for downstream phases.
- Locked the manual MVP acceptance path: translate -> select word/phrase -> save with context -> find in library -> review when due.
- Locked web-first Supabase Auth/Postgres/RLS as the v1 system of record and local cache as secondary only.
- Defined retention, deletion, production observability, provider request, advanced capture, security threat, and learning-quality rules.

## Task Commits

Each task was committed atomically:

1. **Task 1: Create contract shell, traceability, acceptance path, and storage posture** - `f32df8f` (docs)
2. **Task 2: Add retention, deletion, logging, analytics, and provider request boundaries** - `d9affec` (docs)
3. **Task 3: Add advanced capture gate, learning signals, security notes, and review checklist** - `a7ef6fe` (docs)

**Plan metadata:** pending final metadata commit

## Files Created/Modified

- `.planning/contracts/MVP-DATA-PRIVACY-CONTRACT.md` - Canonical product, data, privacy, provider, deletion, observability, advanced-capture, and learning-quality contract.
- `.planning/phases/01-product-data-and-privacy-contract/01-01-SUMMARY.md` - Execution summary and self-check record.

## Verification

- Task 1 acceptance checks passed: contract file, required headings, PROD requirement references, exact manual steps, and storage posture lines.
- Task 2 acceptance checks passed: retention/deletion matrix strings, logging and analytics boundary, observability allowlist, and provider request boundary.
- Task 3 acceptance checks passed: advanced capture gate, learning-quality signals, security threat notes, and review checklist.
- Plan-level verification passed against all required `rg -F` checks from `01-01-PLAN.md`.
- Stub scan passed: no `TODO`, `FIXME`, `placeholder`, `coming soon`, `not available`, or hardcoded empty-value stub patterns in the contract.

## Decisions Made

- Followed the plan's locked web-first storage posture and did not reopen local-first as an option.
- Recorded DeepL API Pro only as the later implementation recommendation behind an adapter, not as an implemented provider.
- Kept Phase 1 docs-only: no app source, schema migration, package install, UI, runtime test, OCR, browser extension, desktop capture, or README change.

## Deviations from Plan

None - plan executed exactly as written.

## Issues Encountered

None.

## Authentication Gates

None.

## Known Stubs

None.

## User Setup Required

None - no external service configuration required.

## Next Phase Readiness

Ready for Phase 2 planning and implementation. Downstream foundation, schema, translation, vocabulary, review, observability, and deletion work should read `.planning/contracts/MVP-DATA-PRIVACY-CONTRACT.md` before implementation.

## Self-Check: PASSED

- Found contract file: `.planning/contracts/MVP-DATA-PRIVACY-CONTRACT.md`
- Found summary file: `.planning/phases/01-product-data-and-privacy-contract/01-01-SUMMARY.md`
- Found task commits: `f32df8f`, `d9affec`, `a7ef6fe`
- Rechecked representative plan-level contract requirements successfully.

---
*Phase: 01-product-data-and-privacy-contract*
*Completed: 2026-04-25*
