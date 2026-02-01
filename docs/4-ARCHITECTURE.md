# INU — Code Architecture

> Folder structure, component hierarchy, routing, state management, data fetching, and conventions

- Feature Spec → [1-PLAN.md](1-PLAN.md)
- Screen Design → [2-WIREFRAMES.md](2-WIREFRAMES.md)
- Design Guide → [3-DESIGN-GUIDE.md](3-DESIGN-GUIDE.md)
- Database Schema → [5-DATABASE.md](5-DATABASE.md)

---

## 1. Tech Stack

| Layer | Technology | Version | Purpose |
| ----- | ---------- | ------- | ------- |
| Framework | Next.js (App Router) | 16.1.x | React SSR/SSG |
| UI | React | 19.2.x | UI library |
| Styling | Tailwind CSS | 4.x | Utility-first CSS |
| State | Zustand | 5.x | Client state management |
| Tree View | React Flow (`@xyflow/react`) | latest | Node-based graph visualization |
| Icons | Lucide React | latest | UI icons (1.5px stroke, rounded) |
| Font | Pretendard | latest | Primary Korean + Latin font |
| Font (mono) | JetBrains Mono | latest | Progress percentages, data values |
| BaaS | Supabase | latest | Database, Auth, Realtime |
| Supabase SSR | `@supabase/ssr` | latest | Server-side Supabase client |
| AI | Gemini 3 API | latest | AI recommendations (P1) |
| Package Manager | pnpm | 10.x | Dependency management |

### pnpm Notes

- Supabase CLI: `pnpm dlx supabase` instead of `npx supabase`
- `.npmrc`: `strict-peer-dependencies=false`
- Vercel auto-detects `pnpm-lock.yaml`

---

## 2. Folder Structure

```
inu-v3/
├── docs/                              # Documentation (existing)
├── public/                            # Static assets
│   └── fonts/                         # Pretendard, JetBrains Mono (self-hosted)
│
├── src/
│   ├── app/                           # ── Next.js App Router ──
│   │   ├── layout.tsx                 # Root layout (font, theme, providers)
│   │   ├── page.tsx                   # `/` → Landing (unauth) | Card View (auth)
│   │   ├── not-found.tsx              # 404 (S-404)
│   │   ├── globals.css                # CSS custom properties, glassmorphism, animations
│   │   │
│   │   ├── auth/
│   │   │   ├── page.tsx              # `/auth` → Login (S-AUTH)
│   │   │   └── callback/
│   │   │       └── route.ts          # OAuth callback (GET handler)
│   │   │
│   │   ├── tree/
│   │   │   └── page.tsx              # `/tree` → Tree View (S-TREE)
│   │   │
│   │   ├── retro/
│   │   │   └── page.tsx              # `/retro` → Retrospective (S-RETRO)
│   │   │
│   │   ├── calendar/
│   │   │   └── page.tsx              # `/calendar` → Calendar (S-CAL, P1)
│   │   │
│   │   ├── settings/
│   │   │   └── page.tsx              # `/settings` → Settings (S-SET)
│   │   │
│   │   └── api/
│   │       └── ai/
│   │           └── route.ts          # Route Handler for Gemini API (P1)
│   │
│   ├── components/                    # ── UI Components ──
│   │   ├── layout/                    # App shell
│   │   │   ├── header.tsx
│   │   │   ├── main-tabs.tsx
│   │   │   └── goals-sub-toggle.tsx
│   │   │
│   │   ├── landing/                   # S-LND
│   │   │   ├── hero-section.tsx
│   │   │   └── feature-cards.tsx
│   │   │
│   │   ├── card-view/                 # S-CARD
│   │   │   ├── card-view-layout.tsx
│   │   │   ├── goal-card.tsx
│   │   │   ├── goal-card-list.tsx
│   │   │   ├── completed-card.tsx
│   │   │   ├── add-goal-card.tsx
│   │   │   ├── inline-action.tsx
│   │   │   ├── inline-action-input.tsx
│   │   │   ├── weekly-panel.tsx
│   │   │   ├── weekly-strip.tsx
│   │   │   ├── daily-actions.tsx
│   │   │   └── mobile-summary-bar.tsx
│   │   │
│   │   ├── tree-view/                 # S-TREE
│   │   │   ├── tree-canvas.tsx
│   │   │   ├── tree-toolbar.tsx
│   │   │   ├── tree-zoom-controls.tsx
│   │   │   ├── tree-legend.tsx
│   │   │   ├── tree-minimap.tsx
│   │   │   ├── nodes/
│   │   │   │   ├── category-node.tsx
│   │   │   │   ├── what-node.tsx
│   │   │   │   ├── why-node.tsx
│   │   │   │   ├── phase-node.tsx
│   │   │   │   └── creating-node.tsx
│   │   │   ├── edges/
│   │   │   │   ├── solid-edge.tsx
│   │   │   │   └── dashed-edge.tsx
│   │   │   └── goal-creation/
│   │   │       ├── creation-flow.tsx
│   │   │       ├── step-category.tsx
│   │   │       ├── step-what.tsx
│   │   │       ├── step-why.tsx
│   │   │       └── step-details.tsx
│   │   │
│   │   ├── modals/                    # All modals
│   │   │   ├── modal-overlay.tsx
│   │   │   ├── welcome-modal.tsx          # S-WEL
│   │   │   ├── goal-detail/               # S-DETAIL
│   │   │   │   ├── goal-detail-modal.tsx
│   │   │   │   ├── info-tab.tsx
│   │   │   │   ├── reflection-tab.tsx
│   │   │   │   ├── history-tab.tsx
│   │   │   │   ├── phase-stepper.tsx
│   │   │   │   ├── phase-section.tsx
│   │   │   │   ├── cross-goal-section.tsx
│   │   │   │   ├── activity-summary.tsx
│   │   │   │   └── action-edit.tsx
│   │   │   ├── refine-modal.tsx           # S-REFINE
│   │   │   ├── phase-complete-modal.tsx   # S-PHASE
│   │   │   ├── change-history-modal.tsx   # S-CHANGE
│   │   │   ├── goal-complete-modal.tsx    # S-COMPLETE
│   │   │   ├── delete-confirm-modal.tsx
│   │   │   └── shared-action-dialog.tsx
│   │   │
│   │   ├── retro/                     # S-RETRO
│   │   │   ├── retro-layout.tsx
│   │   │   ├── daily-retro.tsx
│   │   │   ├── weekly-retro.tsx
│   │   │   ├── emotion-selector.tsx
│   │   │   ├── goal-tile.tsx
│   │   │   ├── emotion-chart.tsx
│   │   │   └── unified-timeline.tsx
│   │   │
│   │   ├── settings/                  # S-SET
│   │   │   └── settings-form.tsx
│   │   │
│   │   └── ui/                        # Shared primitives
│   │       ├── button.tsx
│   │       ├── glass-card.tsx
│   │       ├── input.tsx
│   │       ├── textarea.tsx
│   │       ├── checkbox.tsx
│   │       ├── progress-bar.tsx
│   │       ├── badge.tsx
│   │       ├── tabs.tsx
│   │       ├── toast.tsx
│   │       ├── coaching-bubble.tsx
│   │       ├── skeleton.tsx
│   │       ├── empty-state.tsx
│   │       ├── error-state.tsx
│   │       └── dropdown.tsx
│   │
│   ├── actions/                       # ── Next.js Server Actions ──
│   │   ├── auth.ts
│   │   ├── goals.ts
│   │   ├── phases.ts
│   │   ├── actions.ts
│   │   ├── whys.ts
│   │   ├── journals.ts
│   │   ├── weekly-journals.ts
│   │   ├── change-history.ts
│   │   ├── cross-goals.ts
│   │   ├── profile.ts
│   │   └── ai.ts
│   │
│   ├── hooks/                         # ── Custom React Hooks ──
│   │   ├── use-goals.ts
│   │   ├── use-phases.ts
│   │   ├── use-actions.ts
│   │   ├── use-journals.ts
│   │   ├── use-retro-nudge.ts
│   │   ├── use-weekly-schedule.ts
│   │   ├── use-cross-goals.ts
│   │   ├── use-activity-summary.ts
│   │   └── use-coaching-message.ts
│   │
│   ├── stores/                        # ── Zustand Stores ──
│   │   ├── ui-store.ts
│   │   ├── goal-creation-store.ts
│   │   └── theme-store.ts
│   │
│   ├── types/                         # ── TypeScript Types ──
│   │   ├── database.ts
│   │   ├── goal.ts
│   │   ├── journal.ts
│   │   ├── category.ts
│   │   └── ui.ts
│   │
│   ├── data/                          # ── Static Data (Rule-Based) ──
│   │   ├── categories.ts
│   │   ├── why-options.ts
│   │   ├── phase-templates.ts
│   │   ├── coaching-messages.ts
│   │   └── emotions.ts
│   │
│   ├── lib/                           # ── Core Utilities ──
│   │   ├── supabase/
│   │   │   ├── client.ts
│   │   │   ├── server.ts
│   │   │   ├── middleware.ts
│   │   │   └── admin.ts
│   │   ├── utils.ts
│   │   └── constants.ts
│   │
│   └── middleware.ts                  # Next.js middleware (auth routing)
│
├── supabase/                          # Supabase local dev
│   └── migrations/
│       └── 001_initial_schema.sql
│
├── .env.local                         # Environment variables (gitignored)
├── .env.local.example                 # Template for env vars
├── .npmrc                             # pnpm config
├── .gitignore
├── next.config.ts
├── tailwind.config.ts
├── postcss.config.mjs
├── tsconfig.json
├── package.json
└── README.md
```

---

## 3. Routing & Authentication

### 3.1 URL → Screen Mapping

| URL | Unauth | Auth | Screen ID |
| --- | ------ | ---- | --------- |
| `/` | Landing page | Card View (default) | S-LND / S-CARD |
| `/auth` | Login page | Redirect → `/` | S-AUTH |
| `/auth/callback` | OAuth callback | OAuth callback | — |
| `/tree` | Redirect → `/auth` | Tree View | S-TREE |
| `/retro` | Redirect → `/auth` | Retrospective | S-RETRO |
| `/calendar` | Redirect → `/auth` | Calendar (P1) | S-CAL |
| `/settings` | Redirect → `/auth` | Settings | S-SET |

Modals (S-WEL, S-GOAL, S-DETAIL, S-REFINE, S-PHASE, S-CHANGE, S-COMPLETE) are managed via Zustand state — no URLs.

### 3.2 Middleware (`src/middleware.ts`)

```
Request
  │
  ├─ Is `/auth/callback`? → Pass through (OAuth redirect handler)
  │
  ├─ Is public route (`/`, `/auth`)? → Refresh session, pass through
  │
  ├─ Is protected route (`/tree`, `/retro`, `/calendar`, `/settings`)?
  │    ├─ Has valid session? → Pass through
  │    └─ No session? → Redirect to `/auth`
  │
  └─ Fallback → Pass through (static assets, API, etc.)
```

- Uses `@supabase/ssr` to create a server client within middleware
- Refreshes session token on every request
- The `/` page itself handles conditional rendering (landing vs card view) via server component auth check

### 3.3 Auth Flow (Google OAuth)

```
1. User clicks [Google 로그인] on S-AUTH
2. Server Action calls supabase.auth.signInWithOAuth({ provider: 'google' })
3. User completes Google OAuth consent
4. Redirect to /auth/callback (Route Handler)
5. Route Handler exchanges code for session via supabase.auth.exchangeCodeForSession()
6. Redirect to / (Card View)
7. Root page server component checks auth → renders Card View
8. If profile.onboarding_completed === false → show Welcome Modal (S-WEL)
```

---

## 4. Component Architecture

### 4.1 Conventions

| Convention | Rule |
| ---------- | ---- |
| File naming | kebab-case (`goal-card.tsx`) |
| Component naming | PascalCase (`GoalCard`) |
| Export style | `export default function ComponentName()` |
| One component per file | Co-locate local types/helpers if small |
| Server vs Client | Server Components by default; add `"use client"` only when needed |
| Props interface | Name as `ComponentNameProps`, defined above the component |

### 4.2 `"use client"` Boundary Rules

**Client Components** (`"use client"` required):

- Any component using `useState`, `useEffect`, `useRef`
- Any component using Zustand stores
- Any component with event handlers (`onClick`, `onChange`, `onSubmit`)
- Any component using browser APIs (`localStorage`, `window`)
- React Flow canvas and all custom nodes/edges
- All modal components (overlay click, animation, state)
- Form components (inputs, selects, checkboxes)
- Toast notification system

**Server Components** (no directive needed):

- Page components (`page.tsx`) — fetch data on server, pass to client children
- Root layout (`layout.tsx`)
- Landing page sections (static content)
- Settings page container (server-side data fetch)

### 4.3 Component Hierarchy

```
RootLayout (server)
├── ThemeScript (inline <script> for flash prevention)
├── SupabaseProvider (client — provides Supabase client via context)
├── ToastProvider (client — global toast state)
│
├── [Unauthenticated]
│   ├── LandingPage (server)
│   │   ├── HeroSection
│   │   └── FeatureCards
│   └── AuthPage (server)
│       └── GoogleLoginButton (client)
│
├── [Authenticated]
│   ├── Header (client)
│   │   ├── Logo
│   │   ├── MainTabs (회고 / 목표 / 캘린더)
│   │   │   └── RetroNudgeDot (client — checks daily journal)
│   │   └── UserAvatar + Dropdown
│   │
│   ├── GoalsSubToggle (client — 📋 카드 / 🌳 트리)
│   │
│   ├── CardViewLayout (client)
│   │   ├── GoalCardList
│   │   │   ├── AddGoalCard
│   │   │   └── GoalCard[] (client)
│   │   │       ├── InlineAction[]
│   │   │       └── InlineActionInput
│   │   └── WeeklyPanel
│   │       ├── WeeklyStrip
│   │       └── DailyActions
│   │
│   ├── TreeCanvas (client — React Flow)
│   │   ├── TreeToolbar
│   │   ├── TreeZoomControls
│   │   ├── TreeLegend
│   │   ├── TreeMinimap
│   │   ├── CategoryNode / WhatNode / WhyNode / PhaseNode / CreatingNode
│   │   ├── SolidEdge / DashedEdge
│   │   └── CreationFlow (inline goal creation)
│   │       ├── StepCategory
│   │       ├── StepWhat
│   │       ├── StepWhy
│   │       └── StepDetails
│   │
│   ├── RetroLayout (client)
│   │   ├── DailyRetro
│   │   │   ├── EmotionSelector
│   │   │   └── GoalTile[]
│   │   ├── WeeklyRetro (P1)
│   │   ├── EmotionChart
│   │   └── UnifiedTimeline
│   │
│   └── SettingsForm (client)
│
├── [Modals] (client — rendered at root level via Zustand)
│   ├── ModalOverlay
│   ├── WelcomeModal (S-WEL)
│   ├── GoalDetailModal (S-DETAIL)
│   │   ├── InfoTab
│   │   │   ├── PhaseStepper
│   │   │   ├── PhaseSection[]
│   │   │   ├── CrossGoalSection
│   │   │   └── ActivitySummary
│   │   ├── ReflectionTab
│   │   └── HistoryTab
│   ├── RefineModal (S-REFINE)
│   ├── PhaseCompleteModal (S-PHASE)
│   ├── ChangeHistoryModal (S-CHANGE)
│   ├── GoalCompleteModal (S-COMPLETE)
│   ├── DeleteConfirmModal
│   └── SharedActionDialog
│
└── [Shared UI Primitives]
    ├── Button (primary / secondary / ghost / danger / icon / dashed)
    ├── GlassCard (level 1–4)
    ├── Input / Textarea
    ├── Checkbox (with 🔗 shared / 📅 scheduled badges)
    ├── ProgressBar (Ocean → Sunrise gradient)
    ├── Badge (category tag / priority / connection)
    ├── Tabs (underline style)
    ├── Toast (success / info / warning / error)
    ├── CoachingBubble (🦭 ◁ speech bubble)
    ├── Skeleton (shimmer loading)
    ├── EmptyState (🦭 + message + CTA)
    ├── ErrorState (⚠️ + retry)
    └── Dropdown (Glass-4 context menu)
```

### 4.4 UI Primitive Specs

#### Button

| Variant | Background | Border | Text |
| ------- | ---------- | ------ | ---- |
| `primary` | `Sunrise-500` | none | white, weight 600 |
| `secondary` | transparent | `1px solid var(--border-default)` | `var(--text-primary)` |
| `ghost` | transparent | none | `var(--text-secondary)` |
| `danger` | transparent | `1px solid Error` | Error color |
| `icon` | transparent | none | icon only, 8px padding |
| `dashed` | transparent | `1.5px dashed var(--border-default)` | `var(--text-secondary)` |

Sizes: `sm` (28px), `md` (36px), `lg` (44px). All with `radius: 8px`, `cursor: pointer`.

#### GlassCard

| Level | Usage | Light BG Opacity | Dark BG Opacity | Blur |
| ----- | ----- | ---------------- | --------------- | ---- |
| 1 | Nav bar, weekly strip | 0.60 | 0.50 | 16px |
| 2 | Goal cards, schedule panel | 0.72 | 0.65 | 20px |
| 3 | Modals, coaching bubbles | 0.85 | 0.78 | 24px |
| 4 | Dropdowns, tooltips | 0.92 | 0.88 | 12px |

#### Tabs

All tabs use **underline style**: active = `font-weight: 600` + 2px `Sunrise-500` underline + `var(--text-primary)`. Inactive = `font-weight: 400` + no underline + `var(--text-secondary)`. Transition 150ms ease.

---

## 5. State Management

### 5.1 Zustand Stores

Zustand manages **ephemeral UI state only**. Server data is the source of truth.

#### `ui-store.ts`

```ts
interface UIState {
  // Active view within Goals section
  goalsView: 'card' | 'tree'
  setGoalsView: (view: 'card' | 'tree') => void

  // Card view tab
  cardTab: 'in_progress' | 'completed'
  setCardTab: (tab: 'in_progress' | 'completed') => void

  // Retro sub-tab
  retroTab: 'daily' | 'weekly'
  setRetroTab: (tab: 'daily' | 'weekly') => void

  // Selected goal (for detail modal)
  selectedGoalId: string | null
  setSelectedGoalId: (id: string | null) => void

  // Modal states
  activeModal: ModalType | null
  openModal: (modal: ModalType) => void
  closeModal: () => void

  // Weekly panel selected date
  selectedDate: Date
  setSelectedDate: (date: Date) => void

  // Mobile summary bar
  summaryBarExpanded: boolean
  toggleSummaryBar: () => void
}

type ModalType =
  | 'welcome'
  | 'goal-detail'
  | 'refine'
  | 'phase-complete'
  | 'change-history'
  | 'goal-complete'
  | 'delete-confirm'
```

#### `goal-creation-store.ts`

```ts
interface GoalCreationState {
  isCreating: boolean
  currentStep: 'category' | 'what' | 'why' | 'details' | null

  // Accumulated selections
  selectedCategory: Category | null
  selectedWhats: string[]         // Multiple selection allowed
  selectedWhys: string[]          // Multiple selection allowed
  how: string | null              // Optional
  priority: 'essential' | 'optional' | null
  currentStatus: 'just_starting' | 'some_experience' | 'actively_doing' | null

  // Actions
  startCreation: () => void
  setStep: (step: GoalCreationState['currentStep']) => void
  setCategory: (category: Category) => void
  addWhat: (what: string) => void
  removeWhat: (what: string) => void
  addWhy: (why: string) => void
  removeWhy: (why: string) => void
  setHow: (how: string | null) => void
  setPriority: (priority: 'essential' | 'optional') => void
  setCurrentStatus: (status: GoalCreationState['currentStatus']) => void
  reset: () => void
}
```

#### `theme-store.ts`

```ts
interface ThemeState {
  theme: 'system' | 'light' | 'dark'
  resolvedTheme: 'light' | 'dark'  // Computed from system preference
  setTheme: (theme: 'system' | 'light' | 'dark') => void
}
```

- Persisted to `localStorage` key `inu-theme`
- Synced to Supabase `profiles.theme_preference` on change
- Flash prevention: inline `<script>` in `<head>` reads `localStorage` and sets `class="dark"` before first paint

### 5.2 What Goes Where

| Data | Location | Reason |
| ---- | -------- | ------ |
| Goals, Phases, Actions, WHYs | Supabase (server fetch + realtime) | Source of truth, persisted |
| Journals, Change History | Supabase (server fetch) | Source of truth, persisted |
| User profile | Supabase (server fetch) | Source of truth, persisted |
| Active modal, selected goal | Zustand `ui-store` | Ephemeral, no persistence needed |
| Goal creation steps | Zustand `goal-creation-store` | Multi-step flow, reset on completion |
| Theme preference | Zustand `theme-store` + localStorage | Needs instant access before hydration |
| Goals view (card/tree) | Zustand `ui-store` | Ephemeral toggle state |

---

## 6. Data Fetching Patterns

### 6.1 Server Components (Initial Load)

Pages use server components to fetch initial data, then pass it to client children.

```
page.tsx (server)
  │
  ├─ createServerClient() from @supabase/ssr
  ├─ Fetch data: supabase.from('goals').select('*')
  ├─ Pass data as props to client components
  └─ <CardViewLayout initialGoals={goals} />
```

### 6.2 Client Components (Realtime Updates)

Client components subscribe to Supabase Realtime for live updates after initial hydration.

```
useGoals() hook (client)
  │
  ├─ Receives initialGoals as parameter
  ├─ Sets up Supabase Realtime subscription
  │     supabase.channel('goals').on('postgres_changes', ...)
  ├─ Merges realtime updates with local state
  └─ Returns { goals, isLoading, error }
```

### 6.3 Server Actions (Mutations)

All data mutations go through Server Actions. Server Actions validate input, call Supabase, and revalidate paths.

```
"use server"
export async function createGoal(data: CreateGoalInput) {
  const supabase = await createServerClient()
  const { data: { user } } = await supabase.auth.getUser()

  // Insert goal
  const { data: goal, error } = await supabase
    .from('goals')
    .insert({ ...data, user_id: user.id })
    .select()
    .single()

  // Generate phases from templates
  await generatePhasesFromTemplate(goal.id, data.category, data.what, data.currentStatus)

  revalidatePath('/')
  return goal
}
```

### 6.4 Route Handlers (External APIs)

Only for external API calls that require server-side secrets.

| Route | Method | Purpose |
| ----- | ------ | ------- |
| `/api/ai` | POST | Call Gemini 3 API for AI recommendations (P1) |

### 6.5 Data Flow Summary

```
┌─────────────────────────────────────────────────────────┐
│                        Client                           │
│                                                         │
│  ┌───────────┐    ┌──────────────┐   ┌──────────────┐  │
│  │  Zustand   │    │   React      │   │  Supabase    │  │
│  │  (UI state)│    │   Components │   │  Realtime    │  │
│  │            │◄──►│              │◄──│  Subscription│  │
│  └───────────┘    └──────┬───────┘   └──────────────┘  │
│                          │ call                          │
│                          ▼                               │
│                   Server Actions                         │
│                          │                               │
└──────────────────────────┼───────────────────────────────┘
                           │
                           ▼
                    ┌──────────────┐
                    │   Supabase   │
                    │  PostgreSQL  │
                    │  (RLS)       │
                    └──────────────┘
```

---

## 7. Server Actions Reference

### 7.1 `actions/auth.ts`

| Function | Parameters | Returns | Description |
| -------- | ---------- | ------- | ----------- |
| `signInWithGoogle` | — | `{ url: string }` | Initiate Google OAuth flow via Supabase |
| `signOut` | — | `void` | Sign out and redirect to `/` |

### 7.2 `actions/goals.ts`

| Function | Parameters | Returns | Description |
| -------- | ---------- | ------- | ----------- |
| `createGoal` | `{ category, title, whyIds, how?, priority, currentStatus }` | `Goal` | Create goal + auto-generate phases from templates |
| `updateGoal` | `{ goalId, fields }` | `Goal` | Update goal fields (triggers change history if WHY/category/priority changes) |
| `deleteGoal` | `{ goalId }` | `void` | Soft or hard delete + handle shared action cleanup |
| `reorderGoals` | `{ goalIds: string[] }` | `void` | Update display_order for drag-reorder |
| `completeGoal` | `{ goalId }` | `Goal` | Mark goal as completed |

### 7.3 `actions/phases.ts`

| Function | Parameters | Returns | Description |
| -------- | ---------- | ------- | ----------- |
| `completePhase` | `{ phaseId }` | `Phase` | Mark phase as completed, calculate days_taken, advance next phase to in_progress |
| `updatePhaseStatus` | `{ phaseId, status }` | `Phase` | Manual status override |

### 7.4 `actions/actions.ts`

| Function | Parameters | Returns | Description |
| -------- | ---------- | ------- | ----------- |
| `createAction` | `{ phaseId, title }` | `Action` | Create action + link to phase via phase_actions |
| `toggleAction` | `{ actionId }` | `Action` | Toggle completed state. Updates all linked phases' progress |
| `deleteAction` | `{ actionId, phaseId }` | `void` | Unlink from phase. If no remaining links, delete action |
| `linkSharedAction` | `{ actionId, phaseId }` | `PhaseAction` | Link existing action to another phase (shared action) |
| `findDuplicateAction` | `{ userId, title }` | `Action \| null` | String-match for shared action detection |

### 7.5 `actions/whys.ts`

| Function | Parameters | Returns | Description |
| -------- | ---------- | ------- | ----------- |
| `findOrCreateWhy` | `{ label, parentWhyId? }` | `Why` | Find existing WHY by label (per user) or create new |
| `getWhysByUser` | — | `Why[]` | All WHYs for the current user |
| `addWhyDepth` | `{ whyId, label }` | `Why` | Add a child WHY (for 5 Whys feature) |

### 7.6 `actions/journals.ts`

| Function | Parameters | Returns | Description |
| -------- | ---------- | ------- | ----------- |
| `upsertDailyJournal` | `{ date, mood, content? }` | `DailyJournal` | Create or update daily journal (one per day) |
| `createGoalJournal` | `{ goalId, phaseId?, mood, content? }` | `GoalJournal` | Create per-goal reflection entry |
| `getDailyJournal` | `{ date }` | `DailyJournal \| null` | Get today's daily journal (for nudge check) |
| `getGoalJournals` | `{ goalId }` | `GoalJournal[]` | All journals for a goal grouped by phase |
| `getRecentJournals` | `{ days: number }` | `{ daily, goal }[]` | For emotion chart + timeline |

### 7.7 `actions/change-history.ts`

| Function | Parameters | Returns | Description |
| -------- | ---------- | ------- | ----------- |
| `recordChange` | `{ goalId, field, oldValue, newValue, reason }` | `ChangeHistory` | Record a change with reason |
| `getChangeHistory` | `{ goalId }` | `ChangeHistory[]` | All changes for a goal |

### 7.8 `actions/cross-goals.ts`

| Function | Parameters | Returns | Description |
| -------- | ---------- | ------- | ----------- |
| `createLink` | `{ sourceGoalId, targetGoalId }` | `CrossGoalLink` | Create bidirectional link |
| `getLinkedGoals` | `{ goalId }` | `Goal[]` | All goals linked to this goal |
| `autoLinkByWhy` | `{ goalId }` | `void` | Auto-create links for goals sharing same WHY |

### 7.9 `actions/profile.ts`

| Function | Parameters | Returns | Description |
| -------- | ---------- | ------- | ----------- |
| `updateProfile` | `{ displayName?, avatarUrl? }` | `Profile` | Update profile fields |
| `completeOnboarding` | — | `void` | Set `onboarding_completed = true` |
| `updateThemePreference` | `{ theme }` | `void` | Update theme in DB |
| `toggleWhyDepth` | `{ enabled }` | `void` | Toggle "이유 더 파보기" setting |

---

## 8. Custom Hooks Reference

| Hook | Parameters | Returns | Purpose |
| ---- | ---------- | ------- | ------- |
| `useGoals` | `initialGoals` | `{ goals, isLoading, error }` | Goal list with realtime subscription |
| `usePhases` | `goalId` | `{ phases, isLoading }` | Phases for a goal |
| `useActions` | `phaseId` | `{ actions, isLoading }` | Actions for a phase |
| `useJournals` | `goalId?` | `{ dailyJournals, goalJournals }` | Journal entries |
| `useRetroNudge` | — | `{ hasRecordedToday: boolean }` | Check if today's daily retro is done |
| `useWeeklySchedule` | `weekStart` | `{ days: DaySchedule[] }` | Actions grouped by date for weekly panel |
| `useCrossGoals` | `goalId` | `{ linkedGoals, sharedWhys }` | Cross-goal connections |
| `useActivitySummary` | `goalId` | `{ avgDays, journalCount, moodCounts, changeCount }` | Aggregated data for S-DETAIL |
| `useCoachingMessage` | `goalId, phaseId` | `{ message: string }` | Rule-based coaching message selection |

---

## 9. Static Data Architecture

### 9.1 `data/categories.ts`

Defines the 9 fixed categories with their icons, colors, and fixed WHAT options.

```ts
interface CategoryConfig {
  id: Category
  label: string           // Korean display name
  icon: string            // Emoji
  color: {
    light: string         // Hex
    dark: string          // Hex (+15% lightness)
  }
  whatOptions: string[]   // Fixed WHAT sub-options
}
```

| Category ID | Label | Icon | Color |
| ----------- | ----- | ---- | ----- |
| `exercise` | 운동 | 💪 | `#5C9E9E` / `#6EB3B3` |
| `study` | 공부 | 📚 | `#C9A86C` / `#D4B87C` |
| `career` | 취업 | 💼 | `#5E7B8B` / `#7090A0` |
| `finance` | 재테크 | 💰 | `#B87C6B` / `#C89080` |
| `relationships` | 관계 | 💕 | `#7B6B8D` / `#907FA2` |
| `hobbies` | 취미 | 🎨 | `#6B9B7A` / `#80B08E` |
| `habits` | 습관 | 🧘 | `#8FA3B0` / `#A0B5C0` |
| `reading` | 독서 | 📖 | `#A68B5B` / `#B89E70` |
| `other` | 기타 | ✨ | `#9B9EB0` / `#AEB0C2` |

### 9.2 `data/why-options.ts`

Fixed WHY options per category (from [P-CATEGORY.md §6.3](plan/P-CATEGORY.md)).

```ts
const WHY_OPTIONS: Record<Category, string[]> = {
  exercise:      ['건강 유지', '체력 향상', '다이어트', '스트레스 해소'],
  study:         ['성적 향상', '자기계발', '시험 준비', '커리어 성장'],
  career:        ['경제적 독립', '커리어 성장', '자아실현', '안정적 생활'],
  finance:       ['경제적 독립', '노후 준비', '목표 자금 마련', '재정 안정'],
  relationships: ['소속감', '정서적 안정', '사회적 성장', '외로움 해소'],
  hobbies:       ['스트레스 해소', '자기표현', '성취감', '새로운 경험'],
  habits:        ['자기관리', '건강 유지', '생산성 향상', '마음의 안정'],
  reading:       ['지식 확장', '사고력 향상', '영감', '휴식'],
  other:         [],  // Manual input only
}
```

> WHY duplication across categories is intentional — it serves as the natural starting point for Cross-Goal connections.

### 9.3 `data/phase-templates.ts`

32 category-specific templates + 1 generic template. Each template has 3 phases with 3 actions + 1 coaching message each.

```ts
interface PhaseTemplate {
  phase: 1 | 2 | 3
  title: string
  actions: string[]       // 3 action titles
  coachingMessage: string
}

interface GoalTemplate {
  category: Category
  what: string            // Specific WHAT option
  phases: PhaseTemplate[]
}
```

Status mapping determines which phases are generated:

| Status | Starting Phase | Phases Generated |
| ------ | -------------- | ---------------- |
| `just_starting` | Phase 1 | 1, 2, 3 |
| `some_experience` | Phase 2 | 2, 3 |
| `actively_doing` | Phase 3 | 3 only |

For manual WHAT input or `other` category → use the generic template (시작하기 → 실천하기 → 발전하기).

Full template data: [PHASE-TEMPLATES.md](data/PHASE-TEMPLATES.md)

### 9.4 `data/coaching-messages.ts`

Rule-based conditional coaching messages (from [P-FEATURES.md §3.5](plan/P-FEATURES.md)).

```ts
interface CoachingRule {
  condition: (ctx: CoachingContext) => boolean
  message: (ctx: CoachingContext) => string
  priority: number  // Higher priority rules checked first
}

interface CoachingContext {
  goal: Goal
  currentPhase: Phase
  previousPhase: Phase | null
  recentJournal: GoalJournal | null
  recentWhyChange: ChangeHistory | null
  actionCompletionRate: number
}
```

Rule priority order:

1. Recent journal mood is 😣 → empathy message
2. WHY recently changed → direction-checking message
3. High action completion rate (≥ 80%) → encouragement
4. Previous phase `days_taken` exists → pace reference
5. Default → phase template coaching message

### 9.5 `data/emotions.ts`

```ts
interface EmotionConfig {
  id: Mood
  label: string
  emoji: string
  color: string
}

const EMOTIONS: EmotionConfig[] = [
  { id: 'good',      label: '좋았어',    emoji: '😊', color: '#4CC970' },
  { id: 'neutral',   label: '보통',      emoji: '😐', color: '#3D8DB0' },
  { id: 'tough',     label: '힘들었어',  emoji: '😣', color: '#8B7FBF' },
  { id: 'motivated', label: '의욕적',    emoji: '🔥', color: '#E8923F' },
]
```

---

## 10. Design System Implementation

### 10.1 `globals.css` — CSS Custom Properties

All semantic tokens from [3-DESIGN-GUIDE.md §2.2](3-DESIGN-GUIDE.md):

```css
:root {
  /* Background */
  --bg-primary: #FFFFFF;
  --bg-secondary: #EAF6FA;
  --bg-tertiary: #F5F8FA;
  --bg-glass: rgba(255, 255, 255, 0.72);
  --bg-overlay: rgba(10, 22, 40, 0.45);

  /* Text */
  --text-primary: #0F2137;
  --text-secondary: #1F4F73;
  --text-tertiary: #3D8DB0;
  --text-on-accent: #FFFFFF;

  /* Accent */
  --accent-primary: #E8923F;
  --accent-hover: #D47834;

  /* Borders */
  --border-default: rgba(42, 107, 142, 0.12);
  --border-glass: rgba(255, 255, 255, 0.25);
  --border-selected: #E8923F;

  /* Shadows */
  --shadow-card: 0 2px 8px rgba(10, 22, 40, 0.08);
  --shadow-card-hover: 0 4px 12px rgba(10, 22, 40, 0.10);
  --shadow-modal: 0 8px 32px rgba(10, 22, 40, 0.2);
}

.dark {
  --bg-primary: #0A1628;
  --bg-secondary: #0F2137;
  --bg-tertiary: #142E47;
  --bg-glass: rgba(15, 33, 55, 0.65);
  --bg-overlay: rgba(0, 0, 0, 0.55);

  --text-primary: #F0F4F7;
  --text-secondary: #5FAFC8;
  --text-tertiary: #1F4F73;

  --accent-primary: #F0AD5E;
  --accent-hover: #E8923F;

  --border-default: rgba(93, 175, 200, 0.15);
  --border-glass: rgba(255, 255, 255, 0.08);
  --border-selected: #F0AD5E;

  --shadow-card: 0 2px 8px rgba(0, 0, 0, 0.3);
  --shadow-card-hover: 0 4px 12px rgba(0, 0, 0, 0.35);
  --shadow-modal: 0 8px 32px rgba(0, 0, 0, 0.5);
}
```

### 10.2 `tailwind.config.ts` — Extended Theme

Key extensions:

- **Colors**: `ocean` (50–950), `sunrise` (50–900), `coral` (300–500), all 9 category colors
- **Spacing**: 4px grid (`space-1` through `space-20`)
- **Typography**: `display`, `h1`–`h3`, `body-lg`, `body`, `body-sm`, `caption`, `caption-sm`, `overline`
- **Border radius**: `card` (12px), `node` (8px), `button` (8px), `modal` (16px), `badge` (6px)
- **Breakpoints**: `xs` (<640), `sm` (640), `md` (768), `lg` (1024), `xl` (1280), `2xl` (1536)
- **Animation**: `duration-instant` (100ms), `duration-fast` (150ms), `duration-normal` (200ms), `duration-moderate` (300ms)

### 10.3 Glass Utility Classes

Defined in `globals.css` as composable classes:

```css
.glass-1 { /* Nav, weekly strip */
  background: rgba(255, 255, 255, 0.60);
  backdrop-filter: blur(16px);
  border: 1px solid var(--border-glass);
}

.glass-2 { /* Goal cards, schedule panel */
  background: rgba(255, 255, 255, 0.72);
  backdrop-filter: blur(20px);
  border: 1px solid var(--border-glass);
}

.glass-3 { /* Modals, coaching bubbles */
  background: rgba(255, 255, 255, 0.85);
  backdrop-filter: blur(24px);
  border: 1px solid var(--border-glass);
}

.glass-4 { /* Dropdowns, tooltips */
  background: rgba(255, 255, 255, 0.92);
  backdrop-filter: blur(12px);
  border: 1px solid var(--border-glass);
}

/* Dark mode overrides via .dark parent */
```

### 10.4 Dark Mode Strategy

1. `theme-store` reads from `localStorage('inu-theme')` on init
2. Root layout includes inline `<script>` that sets `document.documentElement.classList` before paint
3. CSS custom properties swap via `.dark` class (§10.1)
4. Tailwind `dark:` variant for one-off overrides
5. Theme preference synced to `profiles.theme_preference` in Supabase
6. Transition: `background-color`, `color`, `border-color` with 300ms fade. No transition on `backdrop-filter`.

### 10.5 Korean Typography

```css
/* Headings: slightly tighter */
.heading { letter-spacing: -0.01em; }

/* Body: default */
.body { letter-spacing: 0; }

/* Korean line breaking: prevent mid-syllable breaks */
* { word-break: keep-all; }

/* Rendering */
body {
  text-rendering: optimizeLegibility;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
```

---

## 11. Supabase Client Setup

### 11.1 Browser Client (`lib/supabase/client.ts`)

```ts
import { createBrowserClient } from '@supabase/ssr'

export function createClient() {
  return createBrowserClient(
    process.env.NEXT_PUBLIC_SUPABASE_URL!,
    process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!
  )
}
```

Used in: Client components, custom hooks, Zustand stores.

### 11.2 Server Client (`lib/supabase/server.ts`)

```ts
import { createServerClient } from '@supabase/ssr'
import { cookies } from 'next/headers'

export async function createClient() {
  const cookieStore = await cookies()
  return createServerClient(
    process.env.NEXT_PUBLIC_SUPABASE_URL!,
    process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!,
    {
      cookies: {
        getAll: () => cookieStore.getAll(),
        setAll: (cookiesToSet) => {
          cookiesToSet.forEach(({ name, value, options }) => {
            cookieStore.set(name, value, options)
          })
        },
      },
    }
  )
}
```

Used in: Server Components, Server Actions, Route Handlers.

### 11.3 Middleware Client (`lib/supabase/middleware.ts`)

Handles session refresh in middleware. Updates cookies on response.

### 11.4 Environment Variables

```env
# .env.local.example
NEXT_PUBLIC_SUPABASE_URL=https://your-project.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=your-anon-key
SUPABASE_SERVICE_ROLE_KEY=your-service-role-key   # Server-only, optional
GEMINI_API_KEY=your-gemini-key                     # Server-only
```

---

## 12. TypeScript Types Overview

### 12.1 `types/category.ts`

```ts
type Category =
  | 'exercise' | 'study' | 'career' | 'finance'
  | 'relationships' | 'hobbies' | 'habits' | 'reading' | 'other'

type Priority = 'essential' | 'optional'

type CurrentStatus = 'just_starting' | 'some_experience' | 'actively_doing'

type PhaseStatus = 'upcoming' | 'in_progress' | 'completed'

type Mood = 'good' | 'neutral' | 'tough' | 'motivated'
```

### 12.2 `types/goal.ts`

```ts
interface Goal {
  id: string
  user_id: string
  title: string
  category: Category
  how: string | null
  priority: Priority
  current_status: CurrentStatus
  current_phase: number
  display_order: number
  created_at: string
  updated_at: string
}

interface Why {
  id: string
  user_id: string
  label: string
  parent_why_id: string | null
  depth: number           // 1–5
  created_at: string
}

interface GoalWhy {
  goal_id: string
  why_id: string
}

interface Phase {
  id: string
  goal_id: string
  phase_order: number
  title: string
  status: PhaseStatus
  coaching_message: string
  started_at: string | null
  completed_at: string | null
  days_taken: number | null
  created_at: string
  updated_at: string
}

interface Action {
  id: string
  user_id: string
  title: string
  completed: boolean
  completed_at: string | null
  created_at: string
  updated_at: string
}

interface PhaseAction {
  phase_id: string
  action_id: string
  display_order: number
}

interface CrossGoalLink {
  id: string
  source_goal_id: string
  target_goal_id: string
  created_at: string
}

interface CrossGoalConnection {
  id: string
  link_id: string
  label: string
  description: string | null
  created_at: string
}

interface ChangeHistory {
  id: string
  goal_id: string
  field: string
  old_value: string
  new_value: string
  reason: string
  changed_at: string
}
```

### 12.3 `types/journal.ts`

```ts
interface DailyJournal {
  id: string
  user_id: string
  journal_date: string    // YYYY-MM-DD, unique per user
  mood: Mood
  content: string | null
  created_at: string
  updated_at: string
}

interface GoalJournal {
  id: string
  goal_id: string
  phase_id: string | null
  mood: Mood
  content: string | null
  created_at: string
}

interface WeeklyJournal {
  id: string
  user_id: string
  week_start: string      // YYYY-MM-DD (Monday), unique per user
  mood: Mood
  content: string | null
  created_at: string
  updated_at: string
}
```

### 12.4 `types/ui.ts`

```ts
type ModalType =
  | 'welcome'
  | 'goal-detail'
  | 'refine'
  | 'phase-complete'
  | 'change-history'
  | 'goal-complete'
  | 'delete-confirm'
  | null

type GoalsView = 'card' | 'tree'
type CardTab = 'in_progress' | 'completed'
type RetroTab = 'daily' | 'weekly'

type GoalCreationStep = 'category' | 'what' | 'why' | 'details'

interface DaySchedule {
  date: string            // YYYY-MM-DD
  actions: (Action & { goalTitle: string; category: Category })[]
  completedCount: number
  totalCount: number
}

interface ActivitySummary {
  avgPhaseDays: number | null
  journalCount: number
  moodCounts: Record<Mood, number>
  whyChangeCount: number
}
```

---

## 13. Tree View Architecture (React Flow)

### 13.1 Node Types

Registered as custom React Flow node types:

| Type Key | Component | Layer | Description |
| -------- | --------- | ----- | ----------- |
| `category` | `CategoryNode` | WHAT (top) | Category root (💪 운동) |
| `what` | `WhatNode` | WHAT | Specific goal (러닝, 헬스) |
| `why` | `WhyNode` | WHY | Shared WHY hub (🎯 건강). Double border if shared |
| `phase` | `PhaseNode` | HOW (bottom) | Phase with status indicator + action preview |
| `creating` | `CreatingNode` | Any | Node being created (dashed border + shimmer) |

### 13.2 Edge Types

| Type Key | Component | Style | Description |
| -------- | --------- | ----- | ----------- |
| `solid` | `SolidEdge` | Solid line | Parent-child direct connection |
| `dashed` | `DashedEdge` | Dashed line | Cross-Goal connection (P1) |

### 13.3 Layout Strategy

- Direction: **top-to-bottom** (WHAT → WHY → HOW)
- Auto-layout algorithm: Dagre or Elkjs for hierarchical positioning
- Minimum horizontal spacing: 80px (goal nodes), 160px (between categories)
- Canvas padding: 16px from viewport edges
- Zoom range: 50%–150%
- Mobile: pinch zoom + 1-finger pan

### 13.4 Tree Data Transformation

Server data → React Flow nodes/edges:

```
Goals + WHYs + Phases (from Supabase)
  │
  ├─ Group goals by category → CategoryNode per unique category
  ├─ Create WhatNode per goal
  ├─ Create WhyNode per unique WHY (merge shared WHYs)
  ├─ Create PhaseNode per phase
  │
  ├─ Edges: Category → What (solid)
  ├─ Edges: What → WHY (solid, via goal_whys)
  ├─ Edges: WHY → Phase (solid, via goal → phases)
  └─ Edges: Cross-Goal (dashed, via cross_goal_links, P1)
```

### 13.5 Inline Goal Creation

When creating a goal in the tree:

1. `CreatingNode` appears with shimmer effect
2. Step-by-step flow renders inside tree nodes (not a separate modal)
3. On each step, new nodes and edges animate in (200ms fade)
4. Auto-layout adjusts (existing nodes shift 200ms, new nodes fade in 200ms)
5. Completed goal solidifies into permanent nodes
6. `goal-creation-store` manages the multi-step flow state

---

## 14. Responsive Layout Patterns

### 14.1 Card View Split Layout

| Breakpoint | Layout | Right Panel |
| ---------- | ------ | ----------- |
| Desktop (≥1024px) | 60:40 split (max-w 600px : 400px) | Always visible |
| Tablet (768–1023px) | Full-width cards | Collapse toggle (`[📅]` icon) |
| Mobile (<768px) | Full-width cards | Replaced by sticky weekly strip + summary bar |

### 14.2 Tree View

All breakpoints: full-width canvas. Right panel hidden when in tree view.

### 14.3 Modal Sizing

| Modal | Desktop max-width | Mobile |
| ----- | ----------------- | ------ |
| S-DETAIL | 680px | Full-width, 16px padding, bottom-sheet |
| S-CHANGE, S-PHASE, confirm | 480px | Full-width, 16px padding, bottom-sheet |
| S-WEL | Full-screen | Full-screen |

### 14.4 Page Container

- Max content width: `1400px`, centered
- Horizontal padding: 24px (desktop), 16px (mobile)
- Header height: 56px (desktop), 48px (mobile)
- Sub-nav height: 44px

---

## 15. Key Conventions & Patterns

### 15.1 Error Handling

- **Server Actions**: Return `{ data, error }` pattern. Never throw.
- **Client Components**: Use `try/catch` for server action calls, show toast on error.
- **Loading States**: Skeleton shimmer (§6.12 of Design Guide), not spinners.
- **Empty States**: 🦭 + message + CTA pattern (§10.3 of Design Guide).
- **AI Fallback**: On Gemini timeout (5s) or error → seamlessly switch to rule-based content. No error UI for AI failures.

### 15.2 Optimistic Updates

- Action checkbox toggle: Update UI immediately, then sync with server
- If server action fails: Revert UI + show error toast

### 15.3 Toast Notifications

| Type | Color | Duration | Example |
| ---- | ----- | -------- | ------- |
| Success | Green | 3s auto | "변경사항이 저장되었어요" |
| Info | Blue | 4s auto | "할 일이 연결되었어요" |
| Warning | Gold | 5s auto | "이 작업은 되돌릴 수 없어요" |
| Error | Red | Manual close | "데이터를 불러오지 못했어요" |

### 15.4 Animation Rules

**Allowed only**: `opacity` (fade), `background-color`/`border-color`/`color` transitions, `box-shadow` (hover), `width` (progress bar), skeleton shimmer.

**Prohibited**: `transform: scale()`, `translateY()`, spring/bounce easing, confetti/particles/sparkle, transitions > 300ms.

| Token | Duration | Usage |
| ----- | -------- | ----- |
| `instant` | 100ms | Checkbox, hover color, focus ring |
| `fast` | 150ms | Tab underline, button hover, toast exit |
| `normal` | 200ms | View fade, card hover, progress bar |
| `moderate` | 300ms | Modal fade, dark mode transition |

### 15.5 Accessibility

- All interactive elements: `cursor: pointer`
- Never use color alone to indicate state (always pair with icon/text)
- `prefers-reduced-motion`: disable all animations
- Focus ring: `box-shadow: 0 0 0 2px rgba(232, 146, 63, 0.2)` on focus
- Korean line-breaking: `word-break: keep-all`
- Emoji icons always paired with text labels

---

*Document version: v1.0*
*Last modified: February 2, 2026*
*v1.0: Initial architecture based on 1-PLAN.md v7.3, 2-WIREFRAMES.md v4.4, 3-DESIGN-GUIDE.md v1.0. React Flow confirmed for tree view. Full scaffolding depth with types + logic.*
