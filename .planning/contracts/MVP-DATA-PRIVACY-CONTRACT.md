# MVP Data and Privacy Contract

## Status

Contract status: Locked for v1 planning.
This contract is the source of truth for the text-only translation-to-memory MVP until a later planning artifact explicitly supersedes it.

This contract locks the product loop, storage posture, retention boundaries, provider request scope, deletion behavior, observability rules, advanced-capture gate, and learning-quality signals for Echoloom v1. It is a planning contract only; it does not create app source, schemas, migrations, providers, or UI.

## Requirements Covered

| Requirement | Meaning | Contract Coverage |
|-------------|---------|-------------------|
| PROD-01 | The MVP acceptance path verifies that a user can translate text, save a selected word or phrase with context, find it in the library, and review it when due. | `MVP Manual Acceptance Path` defines the manual script and future automation mapping. |
| PROD-02 | The project defines whether v1 is web-first authenticated storage or local-first single-user storage before implementation begins. | `Storage Posture` locks web-first Supabase Auth/Postgres/RLS as the v1 system of record. |
| PROD-03 | The project defines privacy and retention rules for source text, saved context, provider requests, deletion, and raw-text logging before implementation begins. | Privacy, retention, deletion, provider, logging, security, and metrics sections define the rules downstream plans must preserve. |

## MVP Manual Acceptance Path

MVP manual acceptance path: translate -> select word/phrase -> save with context -> find in library -> review when due.

### Preconditions

- User is authenticated in the web-first v1 app.
- User can enter current source text and choose a target language.
- Test setup can make at least one saved item eligible for review when due.
- Production observability for the path is text-free under this contract.

### Steps

1. Translate a current source text.
2. Select one word or phrase from the rendered translation result.
3. Save the selected item with source context and translated context.
4. Find the saved item in the library.
5. Review the item when due.

### Pass Criteria

- The saved vocabulary item retains source context, translated context, language pair, and review eligibility.
- The selected word or phrase can be found from the library after save.
- The review queue can present the saved item when it is due.
- The flow does not require OCR, browser selection, desktop capture, or permanent full translation history.

### Future Automation Mapping

Later `QUAL-02` Playwright coverage should mirror this manual script.
Future E2E coverage should assert the same user-visible path: translate, select, save with context, find in library, and review when due.

## Storage Posture

Storage decision: Echoloom v1 is web-first.
System of record: Supabase Auth/Postgres/RLS.
Local cache rule: Any local cache is secondary, derived, clearable, and not authoritative.
Local-first/offline-first is not the v1 default.

Downstream schema and UI plans must treat authenticated Supabase storage as the authority for user-owned saved vocabulary, saved context, review state, and review history. Local browser state may improve responsiveness, but it must be rebuildable or clearable without becoming the canonical record.
