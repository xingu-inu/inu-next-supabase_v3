# INU — Development TODO

> Full development roadmap from project setup to publishing.
> Each phase builds on the previous. P0 = MVP Must Have, P1 = MVP Should Have.

---

## Phase 0: Project Setup & Infrastructure

> Initialize the project, install dependencies, and set up the development environment.

- [ ] Initialize Next.js 16.1.x project with App Router (`pnpm create next-app`)
- [ ] Install core dependencies: React 19.2.x, Tailwind CSS 4.x, Zustand 5.x
- [ ] Install Supabase packages: `@supabase/ssr`, `@supabase/supabase-js`
- [ ] Install React Flow: `@xyflow/react`
- [ ] Install Lucide React icons
- [ ] Configure `tsconfig.json` with path aliases
- [ ] Configure `tailwind.config.ts` (extended theme: colors, spacing, typography, animations, breakpoints)
- [ ] Configure `postcss.config.mjs`
- [ ] Configure `.npmrc` (`strict-peer-dependencies=false`)
- [ ] Self-host fonts in `public/fonts/` (Pretendard + JetBrains Mono)
- [ ] Create `.env.local` from `.env.local.example` template
- [ ] Set up the full `src/` folder structure per architecture spec

**References:**
- [P-TECH.md](plan/P-TECH.md) — Tech stack selections
- [4-ARCHITECTURE.md](4-ARCHITECTURE.md) §1 Tech Stack, §2 Folder Structure

---

## Phase 1: Database & Authentication

> Set up Supabase, run the database migration, and implement the full auth flow.

### Database

- [ ] Create Supabase project (dev environment)
- [ ] Run `supabase/migrations/001_initial_schema.sql` — creates all 13 tables:
  - `profiles`, `goals`, `whys`, `goal_whys`, `phases`, `actions`, `phase_actions`
  - `cross_goal_links`, `cross_goal_connections`, `change_history`
  - `goal_journals`, `daily_journals`, `weekly_journals`
- [ ] Verify custom enum types: `category`, `priority`, `current_status`, `phase_status`, `mood`, `theme_preference`
- [ ] Verify auto-profile trigger (`handle_new_user`) works on auth signup
- [ ] Verify `updated_at` triggers on all applicable tables
- [ ] Enable RLS policies on all 13 tables
- [ ] Enable Supabase Realtime on: `goals`, `phases`, `actions`, `phase_actions`

### Supabase Client Setup

- [ ] `src/lib/supabase/client.ts` — Browser client (`createBrowserClient`)
- [ ] `src/lib/supabase/server.ts` — Server client (`createServerClient` with cookies)
- [ ] `src/lib/supabase/middleware.ts` — Middleware client (session refresh)
- [ ] `src/lib/supabase/admin.ts` — Service role client (optional, server-only)

### Authentication

- [ ] `src/app/auth/page.tsx` — Login page (S-AUTH)
- [ ] `src/app/auth/callback/route.ts` — OAuth callback handler (`exchangeCodeForSession`)
- [ ] `src/actions/auth.ts` — `signInWithGoogle()`, `signOut()`
- [ ] `src/middleware.ts` — Auth routing middleware:
  - Public routes: `/`, `/auth`
  - Protected routes: `/tree`, `/retro`, `/settings`
  - Session refresh on every request
- [ ] Conditional rendering on `/` (landing for unauth, card view for auth)

**References:**
- [5-DATABASE.md](5-DATABASE.md) — Full migration SQL, RLS policies, ER diagram, key queries
- [4-ARCHITECTURE.md](4-ARCHITECTURE.md) §3 Routing & Auth, §11 Supabase Client Setup
- [P-DATA.md](plan/P-DATA.md) — Data structure overview
- [P-FLOWS.md](plan/P-FLOWS.md) §4.1 Onboarding Flow

---

## Phase 2: Design System & UI Primitives

> Implement the visual foundation: CSS custom properties, glassmorphism, and all shared components.

### CSS Foundation

- [ ] `src/app/globals.css` — CSS custom properties for light + dark mode (semantic tokens from §2.2 of Design Guide)
- [ ] Glassmorphism utility classes: `.glass-1`, `.glass-2`, `.glass-3`, `.glass-4`
- [ ] Modal overlay class (`.modal-overlay`)
- [ ] Navigation bar glass class (`.glass-nav`)
- [ ] Coaching bubble glass class (`.coaching-bubble`)
- [ ] Tree node glass classes (`.tree-node`, `.tree-node-selected`, `.tree-node-shared-why`)
- [ ] Skeleton shimmer animation (`@keyframes shimmer`)
- [ ] `@media (prefers-reduced-motion)` support
- [ ] Korean typography rules (`word-break: keep-all`, `letter-spacing`, font rendering)
- [ ] Ocean/Sunrise gradients (page backgrounds, progress fill, landing CTA)

### Tailwind Configuration

- [ ] Colors: `ocean` (50–950), `sunrise` (50–900), `coral` (300–500), 9 category colors
- [ ] Spacing: 4px grid (`space-1` through `space-20`)
- [ ] Typography: `display`, `h1`–`h3`, `body-lg`, `body`, `body-sm`, `caption`, `caption-sm`, `overline`
- [ ] Border radius: `card` (12px), `node` (8px), `button` (8px), `modal` (16px), `badge` (6px)
- [ ] Animation durations: `instant` (100ms), `fast` (150ms), `normal` (200ms), `moderate` (300ms)
- [ ] Breakpoints: `xs`, `sm`, `md` (768px), `lg` (1024px), `xl`, `2xl` (1536px)

### Shared UI Primitives (`src/components/ui/`)

- [ ] `button.tsx` — 6 variants (primary, secondary, ghost, danger, icon, dashed) x 3 sizes (sm, md, lg)
- [ ] `glass-card.tsx` — Levels 1–4 with light/dark mode support
- [ ] `input.tsx` — Styled input with focus ring
- [ ] `textarea.tsx` — Min-height 80px, resize vertical
- [ ] `checkbox.tsx` — With 🔗 shared and 📅 scheduled badges, line-through on complete
- [ ] `progress-bar.tsx` — Ocean → Sunrise gradient fill, 200ms width transition
- [ ] `badge.tsx` — Category tag, Essential/Optional badges, Connection badge
- [ ] `tabs.tsx` — Underline style (active = 600 weight + 2px Sunrise underline)
- [ ] `toast.tsx` — 4 types (success/info/warning/error), left accent bar, auto-dismiss, top-center stacked
- [ ] `coaching-bubble.tsx` — 🦭 ◁ speech bubble with Sunrise-tinted glass
- [ ] `skeleton.tsx` — Shimmer loading effect
- [ ] `empty-state.tsx` — 🦭 + primary/secondary message + CTA button
- [ ] `error-state.tsx` — ⚠️ + retry button
- [ ] `dropdown.tsx` — Glass-4, item hover, divider support

### Dark Mode System

- [ ] `src/stores/theme-store.ts` — Zustand store (`system`/`light`/`dark`, persisted to localStorage)
- [ ] Inline `<script>` in root layout `<head>` for flash prevention
- [ ] Sync theme preference to `profiles.theme_preference` via server action
- [ ] Tailwind `dark:` variant overrides where needed
- [ ] 300ms fade transition on `background-color`, `color`, `border-color`

**References:**
- [3-DESIGN-GUIDE.md](3-DESIGN-GUIDE.md) — Full design system (colors, typography, spacing, glass, components, motion, dark mode, icons, illustrations)
- [4-ARCHITECTURE.md](4-ARCHITECTURE.md) §4.4 UI Primitive Specs, §10 Design System Implementation

---

## Phase 3: Core Data Layer

> TypeScript types, static data, Zustand stores, server actions, and custom hooks.

### TypeScript Types (`src/types/`)

- [ ] `database.ts` — Supabase-generated types (or manual mirror of DB schema)
- [ ] `category.ts` — `Category`, `Priority`, `CurrentStatus`, `PhaseStatus`, `Mood` types
- [ ] `goal.ts` — `Goal`, `Why`, `GoalWhy`, `Phase`, `Action`, `PhaseAction`, `CrossGoalLink`, `CrossGoalConnection`, `ChangeHistory` interfaces
- [ ] `journal.ts` — `DailyJournal`, `GoalJournal`, `WeeklyJournal` interfaces
- [ ] `ui.ts` — `ModalType`, `GoalsView`, `CardTab`, `RetroTab`, `GoalCreationStep`, `DaySchedule`, `ActivitySummary`

### Static Data (`src/data/`)

- [ ] `categories.ts` — 9 fixed categories with icons, colors, whatOptions
- [ ] `why-options.ts` — Fixed WHY options per category (from P-CATEGORY.md §6.3)
- [ ] `phase-templates.ts` — 32 category-specific + 1 generic template (3 phases x 3 actions + coaching each)
- [ ] `coaching-messages.ts` — Rule-based conditional coaching with priority-ordered rules
- [ ] `emotions.ts` — 4 emotion configs (good, neutral, tough, motivated) with colors

### Zustand Stores (`src/stores/`)

- [ ] `ui-store.ts` — goalsView, cardTab, retroTab, selectedGoalId, activeModal, selectedDate, summaryBarExpanded
- [ ] `goal-creation-store.ts` — isCreating, currentStep, selectedCategory, selectedWhats[], selectedWhys[], how, priority, currentStatus
- [ ] `theme-store.ts` — theme, resolvedTheme, setTheme (already listed in Phase 2)

### Server Actions (`src/actions/`)

- [ ] `auth.ts` — `signInWithGoogle()`, `signOut()` (already started in Phase 1)
- [ ] `goals.ts` — `createGoal()`, `updateGoal()`, `deleteGoal()`, `reorderGoals()`, `completeGoal()`
- [ ] `phases.ts` — `completePhase()`, `updatePhaseStatus()`
- [ ] `actions.ts` — `createAction()`, `toggleAction()`, `deleteAction()`, `linkSharedAction()`, `findDuplicateAction()`
- [ ] `whys.ts` — `findOrCreateWhy()`, `getWhysByUser()`, `addWhyDepth()`
- [ ] `journals.ts` — `upsertDailyJournal()`, `createGoalJournal()`, `getDailyJournal()`, `getGoalJournals()`, `getRecentJournals()`
- [ ] `weekly-journals.ts` — CRUD for weekly journal entries (P1, but define early)
- [ ] `change-history.ts` — `recordChange()`, `getChangeHistory()`
- [ ] `cross-goals.ts` — `createLink()`, `getLinkedGoals()`, `autoLinkByWhy()`
- [ ] `profile.ts` — `updateProfile()`, `completeOnboarding()`, `updateThemePreference()`, `toggleWhyDepth()`
- [ ] `ai.ts` — AI recommendation server action (P1, stub for now)

### Custom Hooks (`src/hooks/`)

- [ ] `use-goals.ts` — Goal list with Supabase Realtime subscription
- [ ] `use-phases.ts` — Phases for a goal
- [ ] `use-actions.ts` — Actions for a phase
- [ ] `use-journals.ts` — Daily + goal journal entries
- [ ] `use-retro-nudge.ts` — Check if today's daily retro is recorded
- [ ] `use-weekly-schedule.ts` — Actions grouped by date for weekly panel
- [ ] `use-cross-goals.ts` — Cross-goal connections and shared WHYs
- [ ] `use-activity-summary.ts` — Aggregated data (avg phase days, journal counts, mood counts, change count)
- [ ] `use-coaching-message.ts` — Rule-based coaching message selection with conditional branching

### Utilities (`src/lib/`)

- [ ] `utils.ts` — Shared helper functions
- [ ] `constants.ts` — App-wide constants

**References:**
- [4-ARCHITECTURE.md](4-ARCHITECTURE.md) §5 State Management, §6 Data Fetching, §7 Server Actions, §8 Custom Hooks, §9 Static Data, §12 TypeScript Types
- [P-CATEGORY.md](plan/P-CATEGORY.md) — Category list, WHY options, Phase/Action templates
- [PHASE-TEMPLATES.md](data/PHASE-TEMPLATES.md) — Full template data (32+ sets)
- [P-FEATURES.md](plan/P-FEATURES.md) §3.5 Coaching message rules

---

## Phase 4: Layout & Navigation Shell

> Build the app shell: root layout, header, tabs, modal system, and page container.

- [ ] `src/app/layout.tsx` — Root layout (fonts, ThemeScript, SupabaseProvider, ToastProvider)
- [ ] `src/components/layout/header.tsx` — Logo (🦭 INU), MainTabs, UserAvatar + dropdown
- [ ] `src/components/layout/main-tabs.tsx` — [📝 회고] [🎯 목표] with RetroNudgeDot
- [ ] `src/components/layout/goals-sub-toggle.tsx` — [📋 카드] / [🌳 트리] toggle
- [ ] `src/components/modals/modal-overlay.tsx` — Glass-3 overlay with backdrop blur, click-outside-to-close
- [ ] Root-level modal renderer (reads `activeModal` from Zustand, renders appropriate modal)
- [ ] Main section navigation transitions (fade-out 150ms → fade-in 150ms)
- [ ] Page container: `max-w-[1400px]`, centered, responsive padding (24px desktop / 16px mobile)
- [ ] Header: 56px desktop / 48px mobile, sticky, Glass-1

**References:**
- [4-ARCHITECTURE.md](4-ARCHITECTURE.md) §4.3 Component Hierarchy, §14 Responsive Layout
- [P-IA.md](plan/P-IA.md) — Screen inventory, sitemap, navigation structure, URL mapping
- [3-DESIGN-GUIDE.md](3-DESIGN-GUIDE.md) §5.4 Navigation Bar, §8.3 Transition Patterns

---

## Phase 5: Landing, Auth Pages & Onboarding (P0)

> Build the unauthenticated experience and first-visit onboarding.

### Landing Page (S-LND)

- [ ] `src/app/page.tsx` — Conditional render (landing vs card view based on auth)
- [ ] `src/components/landing/hero-section.tsx` — Tagline, CTA button, seal illustration
- [ ] `src/components/landing/feature-cards.tsx` — Feature highlights

### Auth Page (S-AUTH)

- [ ] `src/app/auth/page.tsx` — Google OAuth login button (already in Phase 1)

### Welcome Modal (S-WEL)

- [ ] `src/components/modals/welcome-modal.tsx` — Full-screen, seal greeting, "먼저 하나만 만들어볼까요?"
- [ ] Trigger: Show when `profile.onboarding_completed === false`
- [ ] On completion → `completeOnboarding()` server action

### Error Page (S-404)

- [ ] `src/app/not-found.tsx` — 🦭 + ❓ + "페이지를 찾지 못했어요" + [홈으로] CTA

**References:**
- [S-LND.md](wireframes/01-unauthenticated/S-LND.md) — Landing page wireframe
- [S-AUTH.md](wireframes/01-unauthenticated/S-AUTH.md) — Login page wireframe
- [S-WEL.md](wireframes/02-onboarding/S-WEL.md) — Welcome modal wireframe
- [S-404.md](wireframes/06-etc/S-404.md) — Error page wireframe
- [P-FLOWS.md](plan/P-FLOWS.md) §4.1 Onboarding Flow

---

## Phase 6: Card View & Weekly Panel (P0)

> Build the default main screen — goal cards with inline actions and the weekly schedule panel.

### Card View Layout

- [ ] `src/components/card-view/card-view-layout.tsx` — Split layout (60:40 desktop, full-width tablet/mobile)
- [ ] `src/components/card-view/goal-card-list.tsx` — Scrollable card list with 24px gap
- [ ] `src/components/card-view/add-goal-card.tsx` — Dashed border "+ 새 목표 추가" card (navigates to Tree View creation)

### Goal Card

- [ ] `src/components/card-view/goal-card.tsx` — Glass-2 card with:
  - Category emoji + title (click → open S-DETAIL modal)
  - Priority badge (🔴 필수 / ⚪ 선택)
  - WHY text (1-line ellipsis, multiple separated by ·)
  - Progress bar (📍 percentage, Ocean→Sunrise gradient)
  - Current phase name
  - Inline action checklist (max 3, overflow "외 N개 더보기")
  - [+ 할 일 추가] inline input
- [ ] `src/components/card-view/inline-action.tsx` — Checkbox + title + shared/scheduled badges
- [ ] `src/components/card-view/inline-action-input.tsx` — Enter to save, Esc to cancel
- [ ] `src/components/card-view/completed-card.tsx` — Dashed border, opacity 0.7, click for journey summary

### Card View Tabs

- [ ] In-progress tab (default) — Active goal cards
- [ ] Completed goals tab — CompletedCard list

### Weekly Panel (Right Side)

- [ ] `src/components/card-view/weekly-panel.tsx` — Container for strip + action list
- [ ] `src/components/card-view/weekly-strip.tsx` — 7 days (Mon–Sun), category-colored dots for completed
- [ ] `src/components/card-view/daily-actions.tsx` — Actions for selected date (category icon + name + checkbox)
- [ ] Completion rate display ("✅ 완료 1/3")

### Mobile Layout

- [ ] `src/components/card-view/mobile-summary-bar.tsx` — Sticky weekly strip + today's to-do summary bar
- [ ] Tablet: collapse toggle for right panel (`[📅]` icon)

### Optimistic Updates

- [ ] Action checkbox toggle: immediate UI update → server sync → revert on failure + error toast

**References:**
- [S-CARD.md](wireframes/03-main/S-CARD.md) — Card view wireframe (14 wireframe details)
- [P-FEATURES.md](plan/P-FEATURES.md) §3.2 Card View
- [4-ARCHITECTURE.md](4-ARCHITECTURE.md) §14 Responsive Layout Patterns
- [5-DATABASE.md](5-DATABASE.md) §5.1 Goals with WHYs query, §5.5 Weekly Schedule query

---

## Phase 7: Tree View & Goal Creation Flow (P0)

> Build the tree visualization with React Flow and the inline goal creation experience.

### Tree Canvas

- [ ] `src/app/tree/page.tsx` — Tree View page (server component, fetch goals/whys/phases)
- [ ] `src/components/tree-view/tree-canvas.tsx` — React Flow canvas (full-width, pinch zoom, 1-finger pan)
- [ ] `src/components/tree-view/tree-toolbar.tsx` — [+ 새 목표] button (top-left)
- [ ] `src/components/tree-view/tree-zoom-controls.tsx` — [+][-] zoom (top-right), range 50%–150%
- [ ] `src/components/tree-view/tree-legend.tsx` — Layer labels: WHAT → WHY → HOW (bottom-left)
- [ ] `src/components/tree-view/tree-minimap.tsx` — Minimap overview (bottom-right)

### Custom Node Types (`src/components/tree-view/nodes/`)

- [ ] `category-node.tsx` — Category root (💪 운동), top layer
- [ ] `what-node.tsx` — Specific goal (러닝, 헬스)
- [ ] `why-node.tsx` — Shared WHY hub (🎯 건강), double border if shared across goals
- [ ] `phase-node.tsx` — Phase with status indicator + action preview
- [ ] `creating-node.tsx` — Dashed border + shimmer effect during creation

### Custom Edge Types (`src/components/tree-view/edges/`)

- [ ] `solid-edge.tsx` — Parent-child direct connections
- [ ] `dashed-edge.tsx` — Cross-Goal connections (P1 visualization, define type now)

### Tree Data Transformation

- [ ] Transform server data (goals + whys + phases) → React Flow nodes/edges
- [ ] Group goals by category → CategoryNode per unique category
- [ ] Merge shared WHYs into single WhyNode
- [ ] Edges: Category→What (solid), What→WHY (solid), WHY→Phase (solid)
- [ ] Auto-layout using Dagre or Elkjs (top-to-bottom hierarchy)
- [ ] Minimum spacing: 80px horizontal (goals), 160px (between categories)

### Goal Creation Flow (Inline in Tree)

- [ ] `src/components/tree-view/goal-creation/creation-flow.tsx` — Orchestrates the multi-step flow
- [ ] `src/components/tree-view/goal-creation/step-category.tsx` — "어떤 것을 시작해볼까요?" (single select, 9 categories)
- [ ] `src/components/tree-view/goal-creation/step-what.tsx` — "어떤 ~이요?" (multiple select from fixed WHAT options)
- [ ] `src/components/tree-view/goal-creation/step-why.tsx` — "왜 하고 싶어?" (multiple select from fixed WHY options)
- [ ] `src/components/tree-view/goal-creation/step-details.tsx` — HOW (optional), priority (required), current status (required)
- [ ] Auto-generate phases + actions from templates based on `currentStatus`
- [ ] Multiple WHAT selections → each becomes independent Goal
- [ ] Multiple WHY selections → shared WHY nodes, auto Cross-Goal linking
- [ ] CreatingNode animation: shimmer → solidify into permanent nodes (200ms fade)
- [ ] Auto-layout adjustment when new nodes are added (200ms repositioning)
- [ ] Drop-off prevention: confirmation if user navigates away during creation

**References:**
- [S-TREE.md](wireframes/03-main/S-TREE.md) — Tree view wireframe (14 wireframe details)
- [S-GOAL.md](wireframes/02-onboarding/S-GOAL.md) — Goal creation flow wireframe
- [P-FEATURES.md](plan/P-FEATURES.md) §3.1 Refine (Goal Creation), §3.3 Tree View
- [4-ARCHITECTURE.md](4-ARCHITECTURE.md) §13 Tree View Architecture (React Flow)
- [P-CATEGORY.md](plan/P-CATEGORY.md) — Category system, WHAT options, WHY options
- [PHASE-TEMPLATES.md](data/PHASE-TEMPLATES.md) — Phase/action auto-generation templates

---

## Phase 8: Goal Detail, Roadmap & All Modals (P0)

> Build all modal screens: goal detail, refinement, phase completion, change history, goal completion.

### Goal Detail Modal (S-DETAIL)

- [ ] `src/components/modals/goal-detail/goal-detail-modal.tsx` — 3-tab modal (max-w 680px desktop, bottom-sheet mobile)
- [ ] `src/components/modals/goal-detail/info-tab.tsx` — Goal info + roadmap view
- [ ] `src/components/modals/goal-detail/phase-stepper.tsx` — Visual step indicator (completed/current/upcoming dots + lines)
- [ ] `src/components/modals/goal-detail/phase-section.tsx` — Expandable phase with actions list + coaching bubble
- [ ] `src/components/modals/goal-detail/action-edit.tsx` — Inline action add/edit/delete within phase
- [ ] `src/components/modals/goal-detail/cross-goal-section.tsx` — Connected goals display with shared WHYs
- [ ] `src/components/modals/goal-detail/activity-summary.tsx` — Avg phase duration, journal mood counts, WHY change count
- [ ] `src/components/modals/goal-detail/reflection-tab.tsx` — Navigate to S-RETRO (auto-expand goal tile)
- [ ] `src/components/modals/goal-detail/history-tab.tsx` — Change history timeline
- [ ] Edit mode: toggle [편집] → inline editing of WHY (tags), HOW (text), priority (radio)
- [ ] Auto-trigger S-CHANGE modal when WHY/category/priority changes in edit mode
- [ ] Shared action detection: on add action → `findDuplicateAction()` → SharedActionDialog

### Shared Action Dialog

- [ ] `src/components/modals/shared-action-dialog.tsx` — "이미 '취업 준비'에 같은 할 일이 있어요. 연결할까요?"

### Refine Modal (S-REFINE)

- [ ] `src/components/modals/refine-modal.tsx` — [✨ 구체화하기] expansion questions (WHERE, WHO, etc.)

### Phase Complete Modal (S-PHASE)

- [ ] `src/components/modals/phase-complete-modal.tsx`:
  - "나의 페이스" — days spent on this phase
  - Growth comparison with previous phase (hide if no comparison target)
  - Next-phase coaching message + WHY reminder
  - [페이스 올리기] button (MVP: suggest 1–2 bonus actions from templates)

### Change History Modal (S-CHANGE)

- [ ] `src/components/modals/change-history-modal.tsx` — "왜 바꾸나요?" free text + save

### Goal Complete Modal (S-COMPLETE)

- [ ] `src/components/modals/goal-complete-modal.tsx`:
  - Journey summary (total days, per-phase duration, journal count, emotion flow)
  - WHY-based next goal recommendation (prioritize un-started cross-goals)
  - If no recommendation → prompt new goal creation
  - Move to completed goals list

### Delete Confirmation

- [ ] `src/components/modals/delete-confirm-modal.tsx` — "정말 삭제할까요?" + shared action warning

### Coaching Message System

- [ ] Implement conditional branching rules in `use-coaching-message.ts`:
  1. Recent journal mood 😣 → empathy message
  2. WHY recently changed → direction-checking message
  3. Action completion rate ≥ 80% → encouragement
  4. Previous phase `days_taken` exists → pace reference
  5. Default → phase template coaching message

### Phase State Machine

- [ ] Phase transitions: `upcoming` → `in_progress` → `completed`
- [ ] On goal create: Phase 1 = `in_progress`, rest = `upcoming` (respects currentStatus mapping)
- [ ] On all actions complete in phase: auto-complete phase, calculate `days_taken`
- [ ] On phase complete: advance next phase to `in_progress`
- [ ] On all phases complete: mark goal as completed

**References:**
- [S-DETAIL.md](wireframes/04-goal-modals/S-DETAIL.md) — Goal detail wireframe
- [S-REFINE.md](wireframes/04-goal-modals/S-REFINE.md) — Refine modal wireframe
- [S-PHASE.md](wireframes/04-goal-modals/S-PHASE.md) — Phase completion wireframe
- [S-CHANGE.md](wireframes/04-goal-modals/S-CHANGE.md) — Change history wireframe
- [S-COMPLETE.md](wireframes/05-retrospective/S-COMPLETE.md) — Goal completion wireframe
- [P-FEATURES.md](plan/P-FEATURES.md) §3.4 Expanding Goals, §3.5 Adaptive Roadmap & Coaching, §3.8 Change History, §3.9 Cross-Goal Mapping, §3.11 Data Insights
- [P-FLOWS.md](plan/P-FLOWS.md) §4.3 Goal Edit, §4.4 Action CRUD, §4.5 Goal Delete
- [5-DATABASE.md](5-DATABASE.md) §6 Phase State Machine, §5.2 Phases with Actions query, §5.3 Activity Summary queries

---

## Phase 9: Retrospective System & Cross-Goal (P0)

> Build the daily retrospective page, emotion chart, unified timeline, and cross-goal features.

### Retrospective Page (S-RETRO)

- [ ] `src/app/retro/page.tsx` — Retro page (server component, fetch recent journals)
- [ ] `src/components/retro/retro-layout.tsx` — Sub-tabs: [일일 회고] (default) / [주간 회고] (P1)

### Daily Retrospective

- [ ] `src/components/retro/daily-retro.tsx` — "오늘 하루 어땠어요?" entry flow:
  1. Overall daily mood selection (required, single): 😊 😐 😣 🔥
  2. Free text (optional): "오늘 하고 싶은 말..."
  3. Per-goal entry tiles (optional): tap active goal → record emotion + note
  4. Save → `upsertDailyJournal()` + `createGoalJournal()` per goal
- [ ] `src/components/retro/emotion-selector.tsx` — 4 rounded-rect buttons (emoji 28px + label)
- [ ] `src/components/retro/goal-tile.tsx` — Active goal card tiles for per-goal entries
- [ ] One daily entry per date (re-recording overwrites via upsert)
- [ ] Date display: "오늘의 하루 — 2026.01.31 (금)" format

### Emotion Flow Chart

- [ ] `src/components/retro/emotion-chart.tsx` — Dot chart, last 14 days:
  - ★ = overall daily mood (from `daily_journals`)
  - ● = per-goal mood (from `goal_journals`), colored by category
  - Y-axis: 4 emotion types, X-axis: dates

### Unified Timeline

- [ ] `src/components/retro/unified-timeline.tsx` — Chronological entries:
  - Daily + per-goal journal notes in time order
  - Filter tabs: [전체] [하루] [💪] [📚] etc. (by category)
  - Reflection card style: Glass-1, left emotion-color accent bar, date+mood header

### Retro Nudge

- [ ] `use-retro-nudge.ts` hook — Check `daily_journals` for today
- [ ] Show ● dot on [📝 회고] tab when no entry recorded today
- [ ] Dot disappears once recorded

### Cross-Goal Features

- [ ] `autoLinkByWhy()` — Auto-create links when goals share same WHY
- [ ] Cross-Goal display on GoalCard (connected goals section)
- [ ] Cross-Goal display on S-DETAIL InfoTab (CrossGoalSection)
- [ ] Shared Action creation: `findDuplicateAction()` by title → suggest linking
- [ ] Shared Action behavior: check on one side → marks complete on both → both progress bars update
- [ ] 🔗 icon on shared actions

**References:**
- [S-RETRO.md](wireframes/05-retrospective/S-RETRO.md) — Retrospective page wireframe
- [P-FEATURES.md](plan/P-FEATURES.md) §3.9 Cross-Goal Mapping, §3.10 Retrospective System
- [P-FLOWS.md](plan/P-FLOWS.md) §4.6 Daily Use Flow
- [5-DATABASE.md](5-DATABASE.md) §5.4 Emotion Chart query, §5.6 Retro Nudge query, §5.7 Cross-Goal Links query

---

## Phase 10: Settings, Polish & QA (P0)

> Settings page, error handling, loading states, accessibility, and end-to-end testing.

### Settings Page (S-SET)

- [ ] `src/app/settings/page.tsx` — Settings page (server component)
- [ ] `src/components/settings/settings-form.tsx`:
  - Profile edit (display name, avatar)
  - Theme selection: System / Light / Dark
  - "이유 더 파보기" toggle (WHY depth ON/OFF)
  - Logout button

### Loading States

- [ ] Skeleton shimmer on all data-loading screens (card list, tree, retro)
- [ ] AI recommendation loading: 🦭 + shimmer in coaching bubble
- [ ] Page-level loading states for route transitions

### Empty States

- [ ] Card View: "아직 목표가 없어요" → [+ 목표 만들기]
- [ ] Tree View: "첫 번째 목표를 만들어볼까요?" + coaching bubble
- [ ] Retrospective: "아직 기록이 없어요" → [첫 기록 시작하기]

### Error Handling

- [ ] Server Actions: `{ data, error }` return pattern (never throw)
- [ ] Client: try/catch → error toast
- [ ] AI fallback: on Gemini timeout (5s) or error → seamless switch to rule-based content

### Accessibility

- [ ] All interactive elements: `cursor: pointer`
- [ ] Focus ring: `box-shadow: 0 0 0 2px rgba(232, 146, 63, 0.2)`
- [ ] Never color alone to indicate state (always icon/text paired)
- [ ] `prefers-reduced-motion`: disable all animations
- [ ] Korean line-breaking: `word-break: keep-all`
- [ ] Emoji always paired with text labels

### End-to-End Flow Testing

- [ ] Onboarding: Land → Login → Welcome → Create first goal → Card View
- [ ] Daily use: Check action on card → progress updates → phase completes → feedback modal
- [ ] Edit flow: Open S-DETAIL → edit WHY → S-CHANGE triggers → save → toast
- [ ] Delete flow: S-DETAIL → delete → confirm → card removed → toast
- [ ] Retro flow: Navigate to 회고 → record daily mood → add per-goal entries → view chart + timeline
- [ ] Goal completion: All phases done → S-COMPLETE → journey summary → move to completed tab
- [ ] Cross-goal: Shared WHY → auto-linked → shared action → complete on one side → both update
- [ ] Tree view: Create goal → nodes appear → auto-layout → WHY merging
- [ ] Responsive: Test all screens at xs, md, lg breakpoints
- [ ] Dark mode: Toggle → verify all components switch correctly

**References:**
- [S-SET.md](wireframes/06-etc/S-SET.md) — Settings page wireframe
- [4-ARCHITECTURE.md](4-ARCHITECTURE.md) §15 Key Conventions (error handling, optimistic updates, toast, animation, accessibility)
- [3-DESIGN-GUIDE.md](3-DESIGN-GUIDE.md) §8 Motion, §10 Illustrations & Brand Expression

---

## Phase 11: P1 Features (Should Have)

> Features included in MVP but developed after all P0 items are complete.

### Weekly Retrospective (S-RETRO-WEEKLY)

- [ ] `src/components/retro/weekly-retro.tsx`:
  - This week's mood summary (auto-generated from daily_journals)
  - Per-goal weekly activity (journal count + mood distribution + completed actions)
  - Weekly reflection input: weekly mood + free text
  - Past weekly retrospective history (◀ ▶ week navigation)
- [ ] Weekly nudge: ● dot on retro tab if no weekly retro by Sunday/Monday
- [ ] "이번 주 기록이 없어요" notice for weeks with no daily entries

### AI Recommendations (Gemini 3)

- [ ] `src/app/api/ai/route.ts` — Route Handler for Gemini 3 API
- [ ] Option recommendation during goal creation
- [ ] WHY recommendation matching the goal
- [ ] Action generation (auto-generate specific actions)
- [ ] Expansion suggestions for existing goals (refine modal)
- [ ] Fallback: skeleton shimmer loading → timeout 5s → switch to rule-based options
- [ ] AI failure: "AI 추천을 불러오지 못했어요" notice + rule-based fallback

### Cross-Goal Tree Visualization

- [ ] DashedEdge connections between goals in Tree View (visual representation)
- [ ] AI-based similar Action recommendations

**References:**
- [P-FEATURES.md](plan/P-FEATURES.md) §3.7 AI Recommendations
- [P-MVP.md](plan/P-MVP.md) §9.1 P1 items
- [S-RETRO.md](wireframes/05-retrospective/S-RETRO.md) — Weekly retro section

---

## Phase 12: Deployment & Publishing

> Production setup, deployment, and final checks.

- [ ] Set up Vercel project (auto-detects `pnpm-lock.yaml`)
- [ ] Configure environment variables on Vercel:
  - `NEXT_PUBLIC_SUPABASE_URL`
  - `NEXT_PUBLIC_SUPABASE_ANON_KEY`
  - `SUPABASE_SERVICE_ROLE_KEY`
  - `GEMINI_API_KEY`
- [ ] Set up Supabase production project
  - Run migration on production database
  - Enable RLS + Realtime
  - Configure Google OAuth redirect URLs for production domain
- [ ] Domain configuration (DNS, SSL)
- [ ] SEO: meta tags, Open Graph images, page titles
- [ ] Performance optimization:
  - Glass blur reduction at tree view zoom-out (50%): simplify to opaque bg
  - Mobile: reduce blur from 20px to 12px
  - `backdrop-filter` only on viewport-visible elements
- [ ] Lighthouse audit (performance, accessibility, SEO, best practices)
- [ ] Final end-to-end QA on production
- [ ] Launch 🚀

**References:**
- [P-TECH.md](plan/P-TECH.md) §7.1 pnpm notes (Vercel auto-detection)
- [4-ARCHITECTURE.md](4-ARCHITECTURE.md) §11.4 Environment Variables
- [3-DESIGN-GUIDE.md](3-DESIGN-GUIDE.md) §5.7 Performance Guidelines

---

## Excluded from MVP (Phase 2 — Post-Launch)

> Features requiring data accumulation or post-validation. Not part of this TODO.

| Feature | Reason |
| ------- | ------ |
| Time allocation recommendations | Requires Cross-Goal data accumulation |
| Weekly snapshot comparison | Requires data accumulation |
| Pattern analysis | Requires data accumulation |
| AI emotion pattern analysis | Requires retro data accumulation |
| Monthly retrospective | Requires weekly retro data accumulation |
| Seal growth system | After core validation |
| Social/community features | MVP is single-user focused |
| Identity exploration | Post-validation feature |
| Mobile app | Web first |

> See [P-MVP.md](plan/P-MVP.md) §9.2 for full exclusion list and rationale.

---

*Based on documentation v7.3 (Plan), v4.4 (Wireframes), v1.0 (Design Guide, Architecture, Database)*
*Last updated: February 2, 2026*
