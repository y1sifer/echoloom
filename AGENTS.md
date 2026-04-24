# Echoloom Agent Guide

## Response Rules

- Always respond in Chinese.
- End every response with a final line containing `🐰`.

## Project Context

Echoloom is a translation-first vocabulary learning app. The core loop is:

1. Translate text.
2. Select useful words or phrases.
3. Save them with their original context.
4. Review them through spaced repetition.

The product should stay focused on turning translation moments into durable memory. Do not expand the MVP into a general translator, content platform, OCR tool, or AI tutor unless planning docs explicitly move that scope into active work.

## Planning Artifacts

Read these before making product or implementation decisions:

- `.planning/PROJECT.md` - product definition, core value, active scope, and decisions.
- `.planning/REQUIREMENTS.md` - v1 requirements and traceability.
- `.planning/ROADMAP.md` - execution structure and success criteria.
- `.planning/STATE.md` - current project position and blockers.
- `.planning/research/SUMMARY.md` - research conclusions and roadmap implications.

## Current Technical Direction

Research currently recommends a web-first MVP:

- Next.js App Router, React, TypeScript.
- Tailwind CSS, shadcn/ui/Radix, lucide-react.
- Supabase Auth/Postgres/Storage/RLS.
- OpenAI Responses API with Structured Outputs.
- `ts-fsrs` for spaced repetition scheduling.
- Vitest and Playwright for verification.

Treat this as the current default unless newer planning work changes it.

## Engineering Rules

- Keep changes surgical and tied to the requested goal.
- Prefer the simplest code that satisfies the requirement.
- Do not add speculative flexibility, extra product scope, or unrequested abstractions.
- Preserve source context as core product data, not optional metadata.
- Avoid raw source text or translated private text in logs, analytics, traces, or error reports.
- Defer screenshot/OCR, browser extension selection, and desktop/system-wide capture until the core text-only learning loop is validated.

## Git Rules

- Do not use internal GSD workflow labels in commit messages, PR titles/descriptions, changelogs, or non-planning summaries.
- Describe commits by concrete feature, bug fix, behavior change, or subsystem impact.
- Group changes by functional outcome.
- Do not revert user changes unless explicitly asked.
