# INU v3

> INU ("The You Within") — A self-understanding based goal management web app.
> Currently in **documentation-only phase** (no source code yet). All specs live in `docs/`.

## Project Documentation (References)

All implementation is based on the docs below. **Always read the relevant docs before writing code.**

- @docs/1-PLAN.md — Project overview and document index
- @docs/4-ARCHITECTURE.md — **Code architecture (must read)**: folder structure, component hierarchy, Server Actions, Hooks, types
- @docs/3-DESIGN-GUIDE.md — Design system: colors, typography, glassmorphism, dark mode, motion, icons
- @docs/5-DATABASE.md — DB schema, RLS policies, migration SQL, key queries
- @docs/2-WIREFRAMES.md — 14 screen wireframes overview (details: `docs/wireframes/`)
- @docs/TODO.md — **Development roadmap (12 Phases)**: implementation order and per-phase references
- @docs/plan/P-FEATURES.md — Core feature specs (§3.1–3.11)
- @docs/plan/P-FLOWS.md — 7 user flows
- @docs/plan/P-CATEGORY.md — Category system, WHY options, phase template structure
- @docs/plan/P-MVP.md — MVP scope (P0 = Must Have, P1 = Should Have)
- @docs/plan/P-IA.md — Information architecture, URL structure, navigation
- @docs/data/PHASE-TEMPLATES.md — 32 phase template datasets

## Commands

```bash
pnpm dev                # Next.js dev server
pnpm build              # Production build
pnpm lint               # ESLint
pnpm type-check         # tsc --noEmit

# Supabase (use pnpm dlx, NOT npx)
pnpm dlx supabase start
pnpm dlx supabase db push
pnpm dlx supabase gen types typescript --local > src/types/database.ts
```

## Tech Stack

- **Framework**: Next.js 16.1.x (App Router) + React 19.2.x
- **Styling**: Tailwind CSS 4.x + CSS custom properties (`globals.css`)
- **State**: Zustand 5.x (UI state only — Supabase is the source of truth for server data)
- **Tree View**: React Flow (`@xyflow/react`)
- **BaaS**: Supabase (PostgreSQL + Auth + Realtime) / `@supabase/ssr`
- **AI**: Gemini 3 API (P1 feature)
- **Icons**: Lucide React (1.5px stroke, rounded)
- **Fonts**: Pretendard (body text) + JetBrains Mono (numbers/data)
- **Package Manager**: pnpm 10.x — **never use npm/yarn**
- **Deploy**: Vercel

## Architecture Rules

### Server-First Component Strategy
- **Default to Server Components**. Only add `"use client"` where needed
- `"use client"` required for: useState/useEffect, Zustand, event handlers, browser APIs, React Flow, modals, forms
- **page.tsx is always a Server Component** — fetch data then pass to client children as props

### Data Flow
- **Server Actions** (`src/actions/`) for all mutations. **Do NOT create API Routes** (exception: `/api/ai/route.ts` for external Gemini calls)
- Server Actions return `{ data, error }` pattern. Never throw
- Client → Server Action → Supabase (RLS enforced) → `revalidatePath()`
- Realtime subscriptions on: `goals`, `phases`, `actions`, `phase_actions` tables

### State Management Boundaries
- **Zustand** = ephemeral UI state only (modals, view toggles, goal creation steps). 3 stores: `ui-store`, `goal-creation-store`, `theme-store`
- **Supabase** = all persisted data
- **localStorage** = theme only (`inu-theme` key)

### Phase State Machine
```
upcoming → in_progress → completed
```
- On goal creation: starting phase determined by `currentStatus`. That phase = `in_progress`, rest = `upcoming`
- All actions in a phase completed → phase auto-completes, `days_taken` calculated → next phase becomes `in_progress`
- All phases completed → goal marked `completed`

## Coding Conventions

- Filenames: **kebab-case** (`goal-card.tsx`). Component names: **PascalCase** (`GoalCard`)
- Export: `export default function ComponentName()`
- Props interface: `ComponentNameProps`, defined above the component. One component per file
- Use CSS custom properties for semantic tokens (`var(--text-primary)`, etc.)
- Glassmorphism 4 levels: `.glass-1`(nav) `.glass-2`(cards) `.glass-3`(modals) `.glass-4`(dropdowns)
- Dark mode: `.dark` class-based, CSS variables auto-switch
- **Korean line-break**: `word-break: keep-all` applied globally
- **9 fixed categories**: exercise, study, career, finance, relationships, hobbies, habits, reading, other
- **Allowed motion**: opacity, background/border/color transitions, box-shadow, width (progress bar), skeleton shimmer
- **Forbidden motion**: transform scale/translateY, spring/bounce, confetti, transitions > 300ms
- Loading: skeleton shimmer (no spinners). Empty state: seal emoji + message + CTA. Errors: toast
- Optimistic updates: action checkbox → immediate UI update → revert + error toast on failure

## Database Essentials

13 tables: `profiles`, `goals`, `whys`, `goal_whys`, `phases`, `actions`, `phase_actions`, `cross_goal_links`, `cross_goal_connections`, `change_history`, `goal_journals`, `daily_journals`, `weekly_journals`

**Key relationships (common mistake areas):**
- **`actions` belongs to user** (not goal). Linked to phases via `phase_actions` N:M. A single action can be shared across multiple phases
- **`whys` is self-referencing** (parent_why_id, max depth 5). Multiple goals can share the same WHY → automatic cross-goal connection
- `daily_journals`: one per user per day (journal_date UNIQUE, upsert)
- RLS enabled on all tables. Full schema → @docs/5-DATABASE.md

## Gotchas

- **pnpm required**. Supabase CLI via `pnpm dlx supabase`. Never use npm/npx
- `.env.local` already exists — gitignored, do not overwrite
- **Goal creation only in Tree View** — clicking "add" in Card View navigates to Tree View
- **Modals have no URLs** — managed via Zustand `activeModal` state (not routing)
- Use `@supabase/ssr` (not the deprecated `@supabase/auth-helpers-nextjs`)
- Glass blur performance: switch to opaque bg when tree view zoomed out (< 50%), reduce mobile blur 20px → 12px
- On AI failure: 5s timeout → rule-based fallback (no error UI shown)

## Workflow — Actively Use Sub-Agents

**IMPORTANT: This project has very detailed documentation, making sub-agent delegation and parallel processing highly efficient.**

### When Starting a New Phase/Feature
1. Check @docs/TODO.md for that phase's items and references
2. Delegate independent tasks to sub-agents for **parallel processing**
3. Pass relevant `@docs/` paths explicitly to each sub-agent

### Sub-Agent Delegation Patterns

**Component implementation:**
```
"Implement [component-name]. Refs: @docs/4-ARCHITECTURE.md, @docs/wireframes/[screen].md, @docs/3-DESIGN-GUIDE.md"
```

**Server Action implementation:**
```
"Implement [action-name] Server Action. Refs: @docs/4-ARCHITECTURE.md §7, @docs/5-DATABASE.md"
```

**Design system work:**
```
"Implement [component] UI primitive. Refs: @docs/3-DESIGN-GUIDE.md, @docs/4-ARCHITECTURE.md §4.4"
```

### Recommended Parallel Processing Combinations
- **UI primitives** (`src/components/ui/`) — each component independent, fully parallelizable
- **Server Actions** (`src/actions/`) — independent per table, parallelizable
- **Custom Hooks** (`src/hooks/`) — after Server Actions are done, each hook independent and parallelizable
- **Static Data** (`src/data/`) — all 5 files independent, fully parallelizable
- **Pages by wireframe** — pages without dependencies can be parallelized

### Pre-Coding Checklist
1. Read the relevant wireframe doc (`docs/wireframes/`)
2. Check Server/Client Component designation in Architecture doc
3. Review related Server Action and Custom Hook signatures
4. Check current phase progress in TODO.md
