# Stack Research

**Project:** Echoloom
**Domain:** Web-first translation, vocabulary capture, and spaced repetition app
**Researched:** 2026-04-24
**Confidence:** HIGH for the web MVP stack; MEDIUM for deferred OCR, browser extension, and desktop/system-wide capture paths

## Executive Recommendation

Build the MVP as a single Next.js App Router application on Vercel, backed by Supabase Auth/Postgres/Storage, with OpenAI Responses API for translation plus structured vocabulary metadata, and `ts-fsrs` for spaced repetition scheduling.

Do not start with a separate backend, desktop app, browser extension, OCR pipeline, or monorepo. Keep translation, capture, and scheduling behind small service modules so the same backend can later serve a Chrome extension or Tauri companion.

The practical 2026 MVP stack is:

```text
Next.js 16 + React 19 + TypeScript 6
Tailwind CSS 4 + shadcn/ui/Radix + lucide-react
Supabase Auth + Postgres + Storage + RLS
OpenAI Responses API + Structured Outputs
ts-fsrs for review scheduling
Vercel for hosting
Vitest + Playwright for verification
```

## Assumptions

| Assumption | Impact |
|------------|--------|
| MVP is web-first and authenticated. | Use Supabase Auth/RLS and avoid anonymous local-only storage as the system of record. |
| Core loop is text translation -> select/save vocabulary -> preserve context -> review. | Do not build OCR, browser extension, or desktop capture in the first implementation. |
| Translation quality and learning metadata matter more than raw machine-translation throughput at MVP scale. | Start with an LLM translation/explanation provider using structured output; add DeepL/Google only after language-pair benchmarks justify it. |
| Future screenshot and system-wide selection capture are plausible but unvalidated. | Design backend API boundaries now; postpone native/browser capture clients until the core loop is stable. |

## Recommended Stack

### Core Technologies

| Technology | Version | Confidence | Purpose | Why Recommended |
|------------|---------|------------|---------|-----------------|
| Node.js | 24.x LTS | HIGH | Runtime for tooling, Next.js, tests, and scripts | Node 24 is Active LTS through 2028-04-30; it satisfies current package engine requirements such as Next `>=20.9.0`, Supabase JS `>=20.0.0`, and `ts-fsrs >=20.0.0`. |
| pnpm | 10.33.2 | HIGH | Package manager | Fast installs and clean dependency graph without forcing a monorepo yet. |
| Next.js App Router | 16.2.x | HIGH | Web app, server-rendered pages, route handlers, server actions | Current Next docs expose App Router, Server/Client Components, Route Handlers, forms, caching, and deployment as first-class patterns. This is enough backend for the MVP. |
| React | 19.2.x | HIGH | UI runtime | React 19 stabilizes Actions and Server Components support used by modern Next apps. |
| TypeScript | 6.0.3 | HIGH | Type safety across UI, API schemas, SRS logic, and provider adapters | The data model has many related entities; TypeScript prevents silent drift between translation outputs, vocabulary records, and review scheduling. |
| Tailwind CSS | 4.2.4 | HIGH | Styling system | Fast UI iteration with the current official Next setup. Good fit for a quiet, dense learning app. |
| shadcn/ui + Radix UI | shadcn 4.4.0; Radix primitives per component | MEDIUM-HIGH | Accessible UI components | shadcn gives copy-owned components over Radix primitives, avoiding a heavy design-system dependency while keeping dialogs, popovers, tabs, and forms accessible. |
| lucide-react | 1.9.0 | HIGH | Icons | Lightweight icon set that fits toolbars, actions, review controls, and management UI. |
| Supabase Cloud | Managed Postgres/Auth/Storage | HIGH | Auth, relational data, files for future screenshots, row-level security | Echoloom is relational: users, translation sessions, vocabulary items, source contexts, review cards, review logs, tags. Supabase keeps Auth, Postgres, generated APIs, Storage, and RLS in one MVP-friendly service. |
| `@supabase/supabase-js` | 2.104.1 | HIGH | Typed client for Supabase APIs | Use with generated database types. Keep user-scoped access under RLS. |
| `@supabase/ssr` | 0.10.2 | MEDIUM-HIGH | Supabase Auth in Next server/client contexts | Official SSR helper for cookie-based auth in Next.js. Supabase still labels the package beta/unstable, so keep the wrapper small. |
| Supabase CLI | 2.95.1 | HIGH | Local DB workflow, migrations, generated TypeScript types | Use SQL migrations and `supabase gen types typescript`; avoid duplicating schema in an ORM during MVP. |
| OpenAI Responses API | model: `gpt-5.4-mini` initially; SDK `openai@6.34.0` | MEDIUM-HIGH | Translation, context-aware explanation, candidate vocabulary metadata, structured JSON | One provider can translate text and return schema-bound fields like lemma, part of speech, concise meaning, example, difficulty hint, and phrase segmentation. Use `gpt-5.4` only when quality testing shows mini is insufficient. |
| `ts-fsrs` | 5.3.2 | HIGH | Spaced repetition scheduler | Purpose-built TypeScript FSRS scheduler. More defensible than hand-rolling memory intervals once reviews become user-visible. |
| Vercel | Current platform | HIGH | Hosting for Next.js | Zero-config Next deployment, preview URLs, environment variables, and serverless route handlers. Avoid platform engineering for the MVP. |

### Supporting Libraries

| Library | Version | Confidence | Purpose | When to Use |
|---------|---------|------------|---------|-------------|
| Zod | 4.3.6 | HIGH | Runtime validation, form schemas, OpenAI structured-output schemas | Use at every external boundary: server actions, route handlers, OpenAI responses, import/export. Zod 4 supports JSON Schema conversion. |
| date-fns | 4.1.0 | HIGH | Date display and light date math | Use for UI formatting and simple schedule display. Store canonical review times in Postgres `timestamptz`. |
| React Hook Form | 7.73.1 | MEDIUM | Complex client-side forms | Use only for vocabulary edit/settings screens if forms become interactive. For simple MVP forms, use React/Next Server Actions directly. |
| `@hookform/resolvers` | 5.2.2 | MEDIUM | Zod integration for React Hook Form | Use only if React Hook Form is added. Watch version compatibility with Zod 4. |
| TanStack Query | 5.100.1 | MEDIUM | Client-side server state | Do not make it global by default. Use for interactive review sessions, optimistic rating updates, or polling if Server Components become awkward. |
| `next-safe-action` | 8.5.2 | MEDIUM | Typed Server Action wrapper | Optional. Add only if Server Action validation/return-shape repetition becomes noisy. Zod alone is enough at first. |
| Sentry Next.js SDK | 10.50.0 | MEDIUM | Error monitoring | Add before private beta or any real user testing. Not needed for the first local prototype. |
| Vitest | 4.1.5 | HIGH | Unit tests | Test FSRS scheduling adapters, translation output parsing, and data-shaping functions. |
| Playwright | 1.59.1 | HIGH | End-to-end browser tests | Test translation -> save -> review queue flows. |
| ESLint | 10.2.1 | HIGH | Linting | Use the Next scaffold defaults first; avoid custom rules until codebase patterns settle. |
| Prettier | 3.8.3 | HIGH | Formatting | Add if the scaffold does not already enforce formatting. |

### Deferred Capability Stack

| Capability | Recommended Later Stack | Confidence | Notes |
|------------|-------------------------|------------|-------|
| User-initiated screenshot/upload OCR | Start with Google Cloud Vision OCR for production-quality text extraction; evaluate Tesseract.js 7.0.0 only for local/private/offline OCR | MEDIUM | Google Vision exposes `TEXT_DETECTION` and `DOCUMENT_TEXT_DETECTION`; Tesseract.js runs in browser/Node but quality and language-pack handling need validation. Keep OCR behind `ocrProvider.extractText(image)`. |
| Screenshot translation without bounding-box fidelity | OpenAI vision via Responses API | MEDIUM | Useful for translating/explaining visible image text, but do not rely on it as the primary OCR engine if exact bounding boxes or dense-document structure matter. |
| Browser-page selection translation | WXT 0.20.25 + Chrome/Firefox extension APIs + same Next API backend | MEDIUM | Use Manifest V3, context menu on selected text, `activeTab` where possible, and never ship OpenAI/Supabase service keys in the extension. |
| Visible tab screenshot capture in browser | Chrome `tabs.captureVisibleTab` from a Manifest V3 extension | MEDIUM | Chrome docs require `activeTab` or host permissions and note `captureVisibleTab` is expensive. This is extension work, not web-app work. |
| Web-only screen capture | Browser Screen Capture API via `navigator.mediaDevices.getDisplayMedia()` | MEDIUM | User-initiated capture only, HTTPS-only, limited browser support. Good for a later proof of concept, not system-wide passive capture. |
| Desktop/system-wide selection translation | Tauri 2.10.1 companion app + `@tauri-apps/plugin-global-shortcut` 2.3.1 + OS-specific native glue | MEDIUM-LOW | Global hotkeys are straightforward in Tauri v2. Reading selected text from arbitrary apps is not a normal web capability and may require clipboard/accessibility permissions per OS. Validate with a spike before roadmap commitment. |

## Installation

For a greenfield MVP scaffold:

```bash
# Runtime
nvm install 24
nvm use 24
corepack enable
corepack prepare pnpm@10.33.2 --activate

# App scaffold
pnpm create next-app@latest echoloom --typescript --eslint --app --src-dir
cd echoloom

# Core runtime dependencies
pnpm add @supabase/supabase-js @supabase/ssr openai zod ts-fsrs date-fns lucide-react

# UI components
pnpm dlx shadcn@latest init

# Dev dependencies
pnpm add -D supabase vitest playwright prettier @types/node tsx
pnpm exec playwright install
```

Optional, only when the need appears:

```bash
# Rich client forms
pnpm add react-hook-form @hookform/resolvers

# Client-heavy review interactions
pnpm add @tanstack/react-query

# Error monitoring before real users
pnpm add @sentry/nextjs

# Deferred OCR/browser/desktop experiments
pnpm add tesseract.js
pnpm create wxt@latest
pnpm add -D @tauri-apps/cli
pnpm add @tauri-apps/api @tauri-apps/plugin-global-shortcut
```

## MVP Architecture Implications

### Data and Auth

Use Supabase SQL migrations as the schema source of truth. Do not add Prisma or Drizzle for the MVP.

Recommended initial tables:

| Table | Purpose |
|-------|---------|
| `profiles` | App-level user preferences and target language defaults. |
| `translation_sessions` | Source text, target language, provider metadata, and created context. |
| `translation_segments` | Sentence/phrase segments if selection needs stable anchors. |
| `vocabulary_items` | Saved word/phrase, normalized form, meaning, notes, source context, status, difficulty. |
| `review_cards` | FSRS card state, due date, stability, difficulty, lapses. |
| `review_logs` | Every review answer and resulting schedule change. |
| `tags` / `vocabulary_tags` | Optional grouping without over-modeling folders early. |

Use RLS policies for user-owned records from day one. Supabase docs recommend Auth + RLS for restricting data based on the logged-in user.

### Translation Provider Boundary

Create a small server-only provider module:

```ts
type TranslateRequest = {
  sourceText: string
  sourceLanguage?: string
  targetLanguage: string
}

type TranslateResult = {
  translatedText: string
  detectedSourceLanguage?: string
  candidates: Array<{
    text: string
    lemma?: string
    partOfSpeech?: string
    conciseMeaning: string
    example?: string
  }>
}
```

Start with OpenAI Responses API + Structured Outputs. Keep the interface provider-agnostic so DeepL or Google Cloud Translation can be benchmarked later without changing UI/data flow.

### Review Scheduling Boundary

Store review state in Postgres, but keep scheduling logic in TypeScript:

```ts
type ReviewRating = "again" | "hard" | "good" | "easy"

type ScheduleReviewInput = {
  cardState: unknown
  rating: ReviewRating
  reviewedAt: Date
}
```

Use `ts-fsrs` to calculate next state and due date, then persist both `review_cards` and `review_logs` in one server action or SQL transaction/RPC.

## Alternatives Considered

| Recommended | Alternative | Why Not for MVP | When to Use Alternative |
|-------------|-------------|-----------------|-------------------------|
| Next.js App Router | SvelteKit | Excellent framework, but React/Next has stronger alignment with shadcn/Radix, Vercel, and current AI app examples. | Use if the project intentionally chooses Svelte and wants less React complexity. |
| Next.js App Router | Remix / React Router framework mode | Strong web fundamentals, but less standard for React Server Components and Vercel-native fullstack patterns in 2026. | Use if the team strongly prefers explicit loaders/actions over RSC/App Router. |
| Next.js App Router | Astro | Better for content-heavy sites than interactive authenticated learning workflows. | Use for a marketing/docs site, not the core app. |
| Supabase | Firebase/Firestore | Vocabulary, contexts, review logs, tags, and due queues are relational. Firestore adds query/model tradeoffs the MVP does not need. | Use if offline-first mobile becomes the main product. |
| Supabase | Neon Postgres + Clerk | Good modular stack, but more services and integration work. | Use if Supabase Auth/RLS constraints become limiting or if auth requirements outgrow Supabase. |
| Supabase JS + SQL migrations | Prisma | Prisma adds schema duplication and does not naturally enforce Supabase RLS client access patterns. | Use if a separate Node backend becomes the source of truth and RLS is no longer central. |
| Supabase JS + SQL migrations | Drizzle ORM | Drizzle is lightweight and good, but still extra schema/query machinery before the data model is proven. | Add later if typed SQL composition becomes a recurring pain. |
| OpenAI Responses API | DeepL API | DeepL is strong for pure MT, but MVP also needs explanations and structured vocabulary candidates. One LLM call can produce both. | Add after benchmarking specific language pairs where DeepL quality/cost is materially better. |
| OpenAI Responses API | Google Cloud Translation | More translation-specific controls and enterprise features, but more cloud setup and still separate from learning metadata generation. | Use for high-volume translation, glossaries, or adaptive translation workflows. |
| Vercel | Cloudflare Workers/OpenNext | More operational decisions and some framework feature differences. | Use if cost/edge placement becomes a real constraint. |
| Tauri later | Electron now | Electron is heavier and solves a deferred problem before the core loop is validated. | Use Electron only if Tauri cannot meet OS integration needs after a spike. |
| Google Vision later | Tesseract.js first | Tesseract is convenient but OCR quality/language-pack behavior can become the product bottleneck. | Use Tesseract if privacy/offline OCR matters more than accuracy. |

## What NOT to Use

| Avoid | Why | Use Instead |
|-------|-----|-------------|
| Separate Express/Nest backend for MVP | Next Route Handlers and Server Actions already cover the required backend surface. A second server adds deployment/auth complexity. | Next.js server code + Supabase. |
| ORM-first schema before Supabase schema is proven | Duplicates schema responsibility and can obscure RLS behavior. | Supabase SQL migrations + generated TypeScript database types. |
| Browser extension as the first product | It validates capture mechanics before proving the translation-to-memory loop. | In-app text input and selection first. |
| Desktop/Tauri app as the first product | System-wide selection capture is OS-specific and permission-heavy. | Web MVP first; Tauri companion after a spike. |
| OCR in MVP | OCR adds image capture, preprocessing, provider choice, language-pack, and bounding-box complexity before the core loop is proven. | Text-only translation first. |
| LocalStorage as the vocabulary source of truth | Users expect saved vocabulary and review history to persist across sessions/devices. | Supabase user-owned tables. |
| Raw JSON mode for AI output | JSON mode does not guarantee schema adherence. | OpenAI Structured Outputs with Zod/JSON Schema. |
| Custom SRS algorithm beyond a trivial prototype | Review scheduling becomes user-trust-critical quickly. | `ts-fsrs` with stored review logs. |
| Long-lived queues/workers for MVP reviews | Daily due cards can be queried directly by `due_at <= now()`. | Postgres queries; add jobs only for notifications/reminders later. |

## Stack Patterns by Variant

**MVP text-only web app:**
- Use one Next.js app.
- Use Supabase Auth/RLS and SQL migrations.
- Use OpenAI Responses API for translation and structured learning metadata.
- Use `ts-fsrs` for review scheduling.
- Verify with Vitest and Playwright.

**If translation quality becomes the differentiator:**
- Build a benchmark set from real user examples.
- Compare OpenAI `gpt-5.4-mini`, OpenAI `gpt-5.4`, DeepL, and Google Cloud Translation for target language pairs.
- Keep the provider interface stable; switch or route providers based on measured quality/cost, not preference.

**If OCR is added:**
- Add `capture_assets` metadata and Supabase Storage bucket.
- Start with upload or user-initiated screen capture, not background capture.
- Use Google Vision OCR first when accuracy matters; use Tesseract.js only when local/private OCR is a product requirement.

**If browser selection translation is added:**
- Add a separate extension client with WXT.
- Extension sends selected text to the same Next API.
- Use short-lived user auth/session flow; never place provider secrets in extension code.

**If desktop/system-wide selection translation is added:**
- Do a Tauri spike before roadmap commitment.
- Use Tauri for global shortcut and native capture bridge, not as a replacement for the web app.
- Prefer a small companion app that calls the web backend. Do not assume a full Next SSR app can be wrapped into Tauri without build/runtime tradeoffs.

## Version Compatibility Notes

| Package / Platform | Compatible With | Notes |
|--------------------|-----------------|-------|
| Node.js 24.x LTS | Next 16.2.x, Supabase JS 2.104.1, `ts-fsrs` 5.3.2 | Current engine checks from npm show Node >=20.9 for Next and >=20 for Supabase JS / `ts-fsrs`; Node 24 gives LTS headroom. |
| Next.js 16.2.x | React 19.2.x | Next docs list 16.2.2 as latest while npm reported 16.2.4 at research time; pin to the latest compatible patch during scaffold. |
| Tailwind CSS 4.2.x | Next.js App Router | Official Tailwind docs use `@tailwindcss/postcss` with Next; create-next-app can scaffold Tailwind directly. |
| `@supabase/ssr` 0.10.2 | Next.js SSR/App Router | Supabase docs recommend it for SSR but label the package beta/unstable. Keep usage behind `createClient` helpers. |
| Zod 4.3.6 | OpenAI Structured Outputs | Zod JSON Schema conversion helps keep AI response schemas and app validation aligned. |
| React Hook Form 7.73.1 + `@hookform/resolvers` 5.2.2 | Zod 4.x | Only add together; resolver/Zod version mismatches are a known source of TypeScript friction. |
| WXT 0.20.25 | Manifest V2/V3 extension builds | Use later for extension tooling; Chrome/Firefox APIs remain the authoritative behavior source. |
| Tauri 2.10.1 | Tauri v2 plugins | Global shortcut functionality is in a plugin with explicit permissions/capabilities. |

## Confidence Assessment

| Area | Confidence | Rationale |
|------|------------|-----------|
| Web framework | HIGH | Verified against current Next and React official docs plus npm versions. |
| UI stack | HIGH | Tailwind/shadcn/Radix are current, mainstream, and sufficient for a focused app UI. |
| Auth/database/storage | HIGH | Supabase directly matches user-owned relational records, RLS, and future screenshot storage. |
| Translation provider | MEDIUM-HIGH | OpenAI Responses + Structured Outputs is well suited for translation plus learning metadata, but specific language-pair quality needs product benchmarking. |
| SRS implementation | HIGH | `ts-fsrs` is a current TypeScript implementation of FSRS and avoids custom scheduling risk. |
| OCR path | MEDIUM | Provider capabilities are clear, but actual quality/cost depends on language, image source, and UX constraints. |
| Browser extension path | MEDIUM | MV3 APIs are documented, but extension UX/auth/permissions need their own phase. |
| Desktop/system selection path | MEDIUM-LOW | Tauri global shortcuts are documented; arbitrary cross-app selected-text capture is OS-specific and must be spiked. |

## Sources

- Next.js docs, latest App Router docs showing version 16.2.2 and current Server/Client Components, Route Handlers, and forms guidance: https://nextjs.org/docs/app/getting-started, https://nextjs.org/docs/app/getting-started/server-and-client-components, https://nextjs.org/docs/app/getting-started/route-handlers, https://nextjs.org/docs/app/guides/forms
- React 19 official release notes and Server Components/Actions guidance: https://react.dev/blog/2024/12/05/react-19
- Node.js official release schedule: https://github.com/nodejs/Release
- Tailwind CSS official Next.js setup: https://tailwindcss.com/docs/guides/nextjs
- shadcn/ui official Next.js installation: https://ui.shadcn.com/docs/installation/next
- Radix UI accessibility and primitives docs: https://www.radix-ui.com/primitives/docs/overview/accessibility
- Supabase SSR, Next.js, RLS, generated types, and Storage docs: https://supabase.com/docs/guides/auth/server-side, https://supabase.com/docs/guides/auth/server-side/nextjs, https://supabase.com/docs/guides/database/postgres/row-level-security, https://supabase.com/docs/guides/api/rest/generating-types, https://supabase.com/docs/guides/storage/quickstart
- OpenAI Models, Responses API, text generation, vision, and Structured Outputs docs: https://developers.openai.com/api/docs/models, https://developers.openai.com/api/docs/guides/text, https://developers.openai.com/api/docs/guides/structured-outputs, https://platform.openai.com/docs/guides/images-vision
- `ts-fsrs` official docs and repository: https://open-spaced-repetition.github.io/ts-fsrs/, https://github.com/open-spaced-repetition/ts-fsrs
- Vercel Next.js deployment docs: https://vercel.com/docs/frameworks/nextjs
- Tauri v2 docs and global shortcut plugin docs: https://v2.tauri.app/, https://v2.tauri.app/plugin/global-shortcut
- MDN Screen Capture API: https://developer.mozilla.org/en-US/docs/Web/API/Screen_Capture_API, https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/getDisplayMedia
- Chrome extension docs for Manifest V3, context menus, and tabs capture: https://developer.chrome.com/docs/extensions/develop/migrate/what-is-mv3, https://developer.chrome.com/docs/extensions/mv2/reference/contextMenus, https://developer.chrome.com/docs/extensions/reference/api/tabs
- WXT official docs: https://wxt.dev/, https://wxt.dev/guide/introduction
- Google Cloud Vision OCR docs: https://docs.cloud.google.com/vision/docs/ocr
- Tesseract.js official site: https://tesseract.projectnaptha.com/
- npm package registry checks on 2026-04-24 for package versions: `next`, `react`, `typescript`, `tailwindcss`, `@supabase/supabase-js`, `@supabase/ssr`, `supabase`, `openai`, `ts-fsrs`, `zod`, `date-fns`, `react-hook-form`, `@hookform/resolvers`, `@tanstack/react-query`, `vitest`, `playwright`, `wxt`, `@tauri-apps/api`, and Tauri plugins.

---
*Stack research for: Echoloom translation-first vocabulary learning MVP*
*Researched: 2026-04-24*
