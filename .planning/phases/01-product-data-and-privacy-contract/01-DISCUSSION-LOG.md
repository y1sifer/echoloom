# Phase 1: Product, Data, and Privacy Contract - Discussion Log

> **Audit trail only.** Do not use as input to planning, research, or execution agents.
> Decisions are captured in CONTEXT.md — this log preserves the alternatives considered.

**Date:** 2026-04-25
**Phase:** 1-Product, Data, and Privacy Contract
**Areas discussed:** MVP acceptance path, Storage posture, Source text retention, Deletion rules, Logging and analytics boundary, Translation provider strategy, Provider request context, Advanced capture unlock conditions, Learning quality signals

---

## MVP Acceptance Path

| Option | Description | Selected |
|--------|-------------|----------|
| Manual end-to-end script | Cover translate, select, save, library, and due review in a concrete verification path. | ✓ |
| High-level checklist | Keep acceptance broad and avoid step-level detail. | |
| Manual script plus later automated E2E | Add explicit automatic E2E requirement in the same decision. | |

**User's choice:** Manual end-to-end script.
**Notes:** Automated E2E remains covered later by `QUAL-02`.

---

## Storage Posture

| Option | Description | Selected |
|--------|-------------|----------|
| Web-first Supabase | Supabase Auth/Postgres/RLS is the system of record. | ✓ |
| Local-first single-user | Store data locally first and defer cloud sync. | |
| Hybrid local cache plus cloud sync | Combine local-first cache and account sync in v1. | |

**User's choice:** Web-first Supabase.
**Notes:** Research recommended web-first unless offline/local-only is a hard requirement.

---

## Source Text Retention

| Option | Description | Selected |
|--------|-------------|----------|
| Long-term save only explicit vocabulary context | Ordinary translation sessions are short-lived or clearable. | ✓ |
| Persist full translation history | Keep a complete translation archive. | |
| Do not persist sessions by default | Store only vocabulary context and no session history. | |

**User's choice:** Long-term save only explicit vocabulary context.
**Notes:** This supports the product promise without turning translation history into a primary v1 feature.

---

## Deletion Rules

| Option | Description | Selected |
|--------|-------------|----------|
| Delete linked context and review data; keep anonymous aggregate metrics | Remove user-specific learning data while preserving non-reconstructive metrics. | ✓ |
| Soft delete with recovery period | Keep recoverable deleted records temporarily. | |
| Hard delete all related and derived records | Remove all related and aggregate traces. | |

**User's choice:** Delete linked context and review data; keep anonymous aggregate metrics.
**Notes:** Aggregate metrics must not reconstruct source text, saved context, or user identity.

---

## Logging and Analytics Boundary

| Option | Description | Selected |
|--------|-------------|----------|
| Never log raw source text, translated private text, or saved context | Strongest operational privacy boundary. | |
| Allow raw text in development logs | Permit raw text in local/dev logs. | |
| Allow configurable debug logging, disabled by default | Debug logging exists only when explicitly enabled. | ✓ |

**User's choice:** Configurable debug logging, disabled by default.
**Notes:** Production defaults must keep raw text and saved context out of logs, analytics, traces, and error reports.

---

## Translation Provider Strategy

| Option | Description | Selected |
|--------|-------------|----------|
| Traditional MT first-class; AI optional enhancement | Traditional translation is core; AI supports learning explanation/candidates/context. | ✓ |
| Traditional MT only; defer AI completely | Use only classic translation in v1. | |
| Traditional MT and AI translation both built in | Ship both provider types for user choice. | |
| Only define provider adapter, do not lock priority | Avoid deciding whether traditional MT or AI is primary. | |

**User's choice:** Traditional MT first-class; AI optional enhancement.
**Notes:** The user explicitly said AI translation is only one possible translation method and traditional translation should be introduced.

---

## Provider Count

| Option | Description | Selected |
|--------|-------------|----------|
| One traditional translation provider with future adapter boundary | Implement one provider and keep the interface replaceable. | ✓ |
| At least two traditional providers | Avoid vendor binding in v1. | |
| One traditional provider plus one AI provider | Build both provider categories immediately. | |
| Only lock strategy; provider choice later | Leave provider count undecided for planning. | |

**User's choice:** One traditional translation provider with future adapter boundary.
**Notes:** The exact provider is left to research/planning.

---

## Provider Request Context

| Option | Description | Selected |
|--------|-------------|----------|
| Current text/languages for traditional MT; current sentence/paragraph and selection only for AI enhancement | Keep provider requests scoped to the current task. | ✓ |
| All providers receive only selected text | Strong privacy but weak context quality. | |
| AI may receive wider context within current translation session | Better explanation quality but more exposure. | |
| User setting chooses privacy-first or quality-first | More control but more v1 complexity. | |

**User's choice:** Current text/languages for traditional MT; current sentence/paragraph and selection only for AI enhancement.
**Notes:** Providers must not receive historical vocabulary, review history, or unrelated user data.

---

## Advanced Capture Unlock Conditions

| Option | Description | Selected |
|--------|-------------|----------|
| Gate advanced capture on validated text loop and manageable review burden | OCR/system-wide selection only after the core loop works. | ✓ |
| Allow advanced capture spike once core features are usable | Permit earlier exploration. | |
| No explicit gate | Decide later by interest. | |

**User's choice:** Gate advanced capture on validated text loop and manageable review burden.
**Notes:** Matches the roadmap's post-v1 advanced-capture boundary.

---

## Learning Quality Signals

| Option | Description | Selected |
|--------|-------------|----------|
| Review completion, overdue burden, lapse rate, edit-from-review, delete/pause rate | Track quality and workload signals. | ✓ |
| Usage volume only | Track translation/saved/review counts. | |
| Define metrics after MVP | Defer metrics until implementation exists. | |

**User's choice:** Review completion, overdue burden, lapse rate, edit-from-review frequency, and delete/pause rate.
**Notes:** Saved-item or translation volume alone is not enough to prove the learning loop.

---

## the agent's Discretion

- Exact acceptance-contract wording and document structure.
- Exact Supabase schema/policy names.
- Exact traditional translation provider choice after research/planning.
- Exact implementation details for metrics collection.

## Deferred Ideas

- Multiple translation providers in v1.
- AI as the primary translation path.
- OCR, browser selection, and desktop/system-wide selection before text-loop validation.
