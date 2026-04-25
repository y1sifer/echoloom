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

## Retention and Deletion Rules

Only learning data intentionally saved by the user becomes long-term product data. Ordinary translation activity is part of the current workflow, not a permanent archive.

| Data Class | Long-Term Retention | Delete Behavior | Provider Exposure | Observability Rule |
|------------|---------------------|-----------------|-------------------|--------------------|
| Ordinary translation session | Short-lived, clearable, or not retained beyond the current workflow | User clear action or expiration removes session content when retained at all | Traditional MT may receive only the current source text plus source and target language for that request | Text-free events only; no raw source text or translated private text |
| Saved vocabulary context | Only user-saved vocabulary item context is retained long term | Deleting a vocabulary item deletes associated stored context and review data | Not sent to providers except as the current sentence or paragraph for an explicit AI enhancement request tied to the selected term or phrase | Never appears in logs, analytics, traces, or error reports |
| Review data linked to a saved vocabulary item | Retained only while needed to schedule and audit that saved item | Deleted with the vocabulary item it belongs to | Never sent to translation providers or AI enhancement providers | Aggregate only if anonymous, non-reconstructive, and non-user-identifying |
| Full permanent translation history is out of v1 | No long-term permanent translation-history archive in v1 | No deletion flow is required for an archive that v1 must not create | Must not be provided as provider context | Must not appear in production observability |
| Anonymous aggregate metrics | May remain after item deletion only if they cannot reconstruct source text, translated private text, saved context, vocabulary history, or review-history content | Anonymous aggregate metrics may remain only if non-reconstructive and non-user-identifying | Never provider payload | Counts, rates, buckets, and timings only |

## Logging and Analytics Boundary

Production logs, analytics, traces, and error reports must not contain raw source text, translated private text, saved context, vocabulary history, or review-history content.
Configurable debug logging is allowed only when disabled by default, explicitly opted in, environment-scoped, and not a production default.

Text-free observability allowlist:

- Allowed observability field: IDs
- Allowed observability field: counts
- Allowed observability field: language pair
- Allowed observability field: lengths
- Allowed observability field: timings
- Allowed observability field: provider status
- Allowed observability field: error category

Any future implementation that needs additional observability fields must prove that the field cannot reconstruct raw source text, translated private text, saved context, vocabulary history, or review-history content.

## Provider Strategy and Request Boundary

Traditional machine translation is first-class for v1.
AI enhancement is optional and not the default definition of translation.
DeepL API Pro is the recommended first traditional provider for later implementation, behind a server-side adapter.
v1 implements one traditional translation provider behind a provider adapter boundary.

Traditional provider request payload: current source text plus source and target language only.
AI enhancement request payload: current sentence or paragraph plus selected term or phrase only.
Forbidden provider fields: historical vocabulary, review history, unrelated saved contexts, unrelated translation sessions, broad user data.

Google, Microsoft, and OpenAI may be considered later adapter targets or optional enhancement surfaces only through the same boundary. They are not additional active v1 traditional provider implementations in this contract.

## Advanced Capture Gate

Screenshot/OCR, browser selection, and desktop/system-wide selection stay out of v1.
Advanced capture may be reconsidered only after the text-only loop is complete and review burden is manageable.

Any later advanced capture plan must reuse the same context, retention, deletion, provider, and observability boundaries defined in this contract. Advanced capture must not create a parallel save/review pipeline or broaden provider payloads by default.

## Learning Quality Signals

Usage volume alone is insufficient to prove the learning loop works.

| Signal | Definition | Privacy Payload | Why It Matters |
|--------|------------|-----------------|----------------|
| Review completion | Completed review cards divided by due review cards in a period. | Counts only. | Proves saved items return to actual review instead of stopping at capture. |
| Overdue burden | Active cards overdue at the end of a period, optionally grouped by age bucket. | Counts and age buckets only. | Shows whether the loop creates manageable review debt. |
| Lapse rate | Low-grade or failed review outcomes divided by completed reviews. | Counts only. | Flags card quality, scheduling difficulty, and memory weakness. |
| Edit-from-review frequency | Edits launched from review divided by completed reviews. | Counts only. | Measures how often cards are confusing enough to require correction in context. |
| Delete/pause rate | Deleted or paused items divided by active or reviewed items. | Counts only. | Signals low-value captures, overload, or poor item quality. |

These signals must remain text-free: they can use IDs, counts, rates, buckets, timings, language pair, and status, but not raw source text, translated private text, saved context, vocabulary history, or review-history content.

## Security Threat Notes

- Raw source text leakage into logs, analytics, traces, or error reports
  - Contract mitigation: `Logging and Analytics Boundary` bans raw source text, translated private text, saved context, vocabulary history, and review-history content from production observability, and provides a text-free allowlist.
- Provider over-sharing beyond current source text, current sentence or paragraph, or selected term or phrase
  - Contract mitigation: `Provider Strategy and Request Boundary` allows traditional MT payloads to contain only current source text plus languages, allows AI enhancement payloads to contain only the current sentence or paragraph plus selected term or phrase, and forbids history, unrelated contexts, and broad user data.
- Deletion gaps leaving saved context or review data behind
  - Contract mitigation: `Retention and Deletion Rules` states that deleting a vocabulary item deletes associated stored context and review data.
- Anonymous aggregate metrics becoming reconstructive or user-identifying
  - Contract mitigation: `Retention and Deletion Rules` allows anonymous aggregate metrics to remain only if non-reconstructive and non-user-identifying, and `Learning Quality Signals` limits metric payloads to text-free values.
- Debug logging accidentally enabled in production
  - Contract mitigation: `Logging and Analytics Boundary` permits configurable debug logging only when disabled by default, explicitly opted in, environment-scoped, and not a production default.

## Review Checklist

- [ ] PROD-01 acceptance path exists and covers translate -> select -> save -> library -> review.
- [ ] PROD-02 storage posture names Supabase Auth/Postgres/RLS as system of record.
- [ ] PROD-03 retention rules distinguish ordinary sessions, saved context, provider requests, deletion, and raw-text logging.
- [ ] Provider payloads exclude history, unrelated contexts, review data, and broad user data.
- [ ] Production observability excludes raw source text, translated private text, saved context, vocabulary history, and review-history content.
- [ ] Deleting vocabulary removes associated context and review data.
- [ ] Aggregate metrics are anonymous, non-reconstructive, and non-user-identifying.
- [ ] Advanced capture remains out of v1 until the text-only loop is complete and review burden is manageable.
