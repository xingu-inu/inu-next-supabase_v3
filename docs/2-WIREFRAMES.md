# INU - Screen Design (Wireframes)

> Layout, components, states, and interaction definitions for each screen
> For feature specs, see [1-PLAN.md](1-PLAN.md)
> For individual wireframes, see the [wireframes/](wireframes/) folder

---

## 1. Screen List

| Screen ID | Screen Name | Type | Description | Wireframe |
| --------- | ----------- | ---- | ----------- | --------- |
| S-LND | 랜딩 | Page | Landing page for non-logged-in users | [S-LND.md](wireframes/01-unauthenticated/S-LND.md) |
| S-AUTH | 로그인 | Page | Google OAuth | [S-AUTH.md](wireframes/01-unauthenticated/S-AUTH.md) |
| S-WEL | Welcome | Modal (Full) | First-visit welcome | [S-WEL.md](wireframes/02-onboarding/S-WEL.md) |
| S-GOAL | Goal Creation | Inline in Tree View | Create Goal directly within the tree view | [S-GOAL.md](wireframes/02-onboarding/S-GOAL.md) |
| S-CARD | Card View | Page (Default) | Goal card list + inline Actions + weekly schedule (split layout) | [S-CARD.md](wireframes/03-main/S-CARD.md) |
| S-TREE | Tree View | Page | Goal structure tree visualization + Goal creation | [S-TREE.md](wireframes/03-main/S-TREE.md) |
| S-DETAIL | Goal Detail | Modal | Goal edit/detail + reflection notes + Cross-Goal | [S-DETAIL.md](wireframes/04-goal-modals/S-DETAIL.md) |
| S-REFINE | 구체화하기 | Modal | Add refinement | [S-REFINE.md](wireframes/04-goal-modals/S-REFINE.md) |
| S-PHASE | Phase Complete | Modal | Phase feedback + next step | [S-PHASE.md](wireframes/04-goal-modals/S-PHASE.md) |
| S-CHANGE | Change History | Modal | Record reasons when attributes change | [S-CHANGE.md](wireframes/04-goal-modals/S-CHANGE.md) |
| S-RETRO | Retrospective Page | Page | Daily/weekly retrospective + emotion flow chart + integrated timeline | [S-RETRO.md](wireframes/05-retrospective/S-RETRO.md) |
| S-COMPLETE | Goal Complete | Modal | Journey summary + archive | [S-COMPLETE.md](wireframes/05-retrospective/S-COMPLETE.md) |
| S-SET | Settings | Page | Profile, account, "이유 더 파보기" ON/OFF | [S-SET.md](wireframes/06-etc/S-SET.md) |
| S-404 | Error | Page | Page not found | [S-404.md](wireframes/06-etc/S-404.md) |

---

## 2. Wireframes by Screen

### Unauthenticated Area

- [S-LND — Landing Page](wireframes/01-unauthenticated/S-LND.md)
- [S-AUTH — 로그인](wireframes/01-unauthenticated/S-AUTH.md)

### Onboarding

- [S-WEL — Welcome Modal](wireframes/02-onboarding/S-WEL.md)
- [S-GOAL — Goal Creation (Inline in Tree View)](wireframes/02-onboarding/S-GOAL.md)

### Main Screens

- [S-CARD — Card View (Desktop Split / Mobile / Empty / Complete + Inline Actions + Weekly Schedule)](wireframes/03-main/S-CARD.md)
- [S-TREE — Tree View (T-1~T-14 + Rule Table)](wireframes/03-main/S-TREE.md) ★ Largest

### Goal-Related Modals

- [S-DETAIL — Goal Detail Modal (4 Tabs + Edit + Actions)](wireframes/04-goal-modals/S-DETAIL.md)
- [S-REFINE — 구체화하기 Modal](wireframes/04-goal-modals/S-REFINE.md)
- [S-PHASE — Phase Complete Feedback Modal](wireframes/04-goal-modals/S-PHASE.md)
- [S-CHANGE — Change History Modal](wireframes/04-goal-modals/S-CHANGE.md)

### Retrospective

- [S-RETRO — Retrospective Page (Daily Desktop/Mobile + Weekly)](wireframes/05-retrospective/S-RETRO.md)
- [S-COMPLETE — Goal Complete Feedback Modal](wireframes/05-retrospective/S-COMPLETE.md)

### Etc

- [S-SET — Settings Page](wireframes/06-etc/S-SET.md)
- [S-404 — Page Not Found](wireframes/06-etc/S-404.md)

---

## 3. Mobile Wireframe Reference

> Mobile wireframes are integrated into each screen's file.

| Screen | File Location |
| ------ | ------------- |
| Card View (Mobile) | [S-CARD.md](wireframes/03-main/S-CARD.md) → S-CARD — Card View (Mobile) |
| Tree View (Mobile) | [S-TREE.md](wireframes/03-main/S-TREE.md) → T-14. Mobile Tree View |
| Daily Retrospective (Mobile) | [S-RETRO.md](wireframes/05-retrospective/S-RETRO.md) → S-RETRO-DAILY — Daily Retrospective (Mobile) |
| Goal Creation (Mobile) | [S-TREE.md](wireframes/03-main/S-TREE.md) → T-12. Goal Creation Inline Flow |

---

## 4. State Definitions

### Common States

| State | Representation |
| ----- | -------------- |
| Loading | Skeleton UI (shimmer) |
| Empty | Guide message + CTA |
| Error | Error message + retry button |
| Success | Toast notification |

#### Loading — Skeleton UI (Card View Example)

```text
╭──────────────────────────────────────────────────────────────╮
│  ░░░░░░░░░░░░░░░░                                            │
│  ░░░░░░░░░░ · ░░░░░░░░░░░                                    │
│  ░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░  ░░%                  │
│  ░░░░░░░░░░░░░ · ░░░░░░░                                     │
╰──────────────────────────────────────────────────────────────╯
╭──────────────────────────────────────────────────────────────╮
│  ░░░░░░░░░░░░░░░░                                            │
│  ░░░░░░░░░░ · ░░░░░░░░░░░                                    │
│  ░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░  ░░%                  │
│  ░░░░░░░░░░░░░ · ░░░░░░░                                     │
╰──────────────────────────────────────────────────────────────╯
```

- Shimmer animation applied to `░░` areas (left-to-right gradient loop)
- Number of cards: based on existing data or default 3

#### Error — Inline Error

```text
╭──────────────────────────────────────────────────────────────╮
│                                                                │
│                     ⚠️ 데이터를 불러오지 못했어요               │
│                                                                │
│                       ╭──────────────╮                        │
│                       │  다시 시도    │                        │
│                       ╰──────────────╯                        │
│                                                                │
╰──────────────────────────────────────────────────────────────╯
```

- Clicking [다시 시도] re-requests only the affected area (not a full page refresh)

#### Coaching Message Loading/Fallback

Loading (waiting for AI response):

```text
     🦭 ◁ │ ░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░ │         ← shimmer
```

AI failure → rule-based fallback (seamlessly replaced without separate notification to the user):

```text
     🦭 ◁ │ "꾸준함이 체력의 기초예요" │               ← rule-based message
```

### Goal Card States

| State | Representation |
| ----- | -------------- |
| Default | Header (title + WHY + progress) + current phase + inline Action checklist |
| Hover | Increased shadow (header area) |
| On Action check | ☐→☑ + strikethrough + progress bar update animation |

### Right Panel (Weekly/Monthly Schedule) States

| State | Representation |
| ----- | -------------- |
| Date dot (completed) | Category color ● (max 3, +N if exceeded) |
| Date dot (scheduled) | Empty circle ○ |
| Today's date | Bold emphasis + "오늘" label |
| Date with no Actions | "일정이 없어요" message |
| Monthly view active | Mini calendar grid with category dots per date |
| Monthly today highlight | Bold bracket [14] around today's date |
| View toggle | [주간] / [월간] tabs with underline on active |

### Phase States

| State | Representation |
| ----- | -------------- |
| Upcoming | opacity 0.6 + muted style, content visible |
| In Progress | Emphasized border + "지금 여기" indicator + coaching message |
| Completed | Check icon + completion color |

### Retrospective Page States

| State | Representation |
| ----- | -------------- |
| Today not recorded | Today's daily record UI active (emotion selection available) |
| Today's record complete | "✅ 오늘 기록 완료" + mood icon + [다시 기록하기] |
| No records (Empty) | Guide message + CTA ("첫 기록을 시작해보세요") |

### Retrospective Tab Nudge States

| State | Representation |
| ----- | -------------- |
| Today's daily retrospective not recorded | [📝●회고] — dot (●) next to tab (Sunrise Gold) |
| Today's record complete | [📝 회고] — no dot (default state) |
| Current page is /retro | Tab active color (Sunrise Gold) |

### Action Check States

| State | Representation |
| ----- | -------------- |
| Incomplete | ☐ empty checkbox |
| Complete | ☑ checked + strikethrough |
| Shared | 🔗 icon (linked to other Goals) |
| Scheduled | 📅 icon (has scheduled date) |

### Toast Notifications

| Type | Color | Duration |
| ---- | ----- | -------- |
| Success | Green | 3s |
| Info | Blue | 4s |
| Warning | Yellow | 5s |
| Error | Red | Manual close |

### Tree Node States

| State | Representation | Wireframe Symbol |
| ----- | -------------- | ---------------- |
| Default | Standard rounded-corner card | `╭──╮╰──╯` |
| Hover | cursor: pointer + subtle shadow change | `╭──╮╰──╯` + `░░` |
| Selected | Bold border + expanded info display | `┏━━┓┗━━┛` |
| Inactive | opacity 0.5 + dashed border | `╭╌╌╮╰╌╌╯` |
| Creating | Dashed + shimmer effect | `╭┄┄╮╰┄┄╯` |
| WHY (Shared) | Double border + connection count badge | `╔══╗║  ║╚══╝` |

### Tree Connection Line States

| Connection Type | Representation | Wireframe Symbol |
| --------------- | -------------- | ---------------- |
| Parent-child (direct) | Solid line | `━━━` / `│` |
| Cross-Goal | Dashed line | `┄ ┄ ┄` |
| WHY Depth | Double arrow | `══>` |
| Branch (1:N) | Solid line + branch point | `├──┬──┤` |
| Merge (N:1 shared WHY) | Solid merge | `└──┼──┘` |

---

## 5. Interaction Definitions

### Main Section Transition (Retrospective ↔ Goals)

| Step | Description | Duration |
| ---- | ----------- | -------- |
| 1 | Current section fade-out | 150ms |
| 2 | New section fade-in | 150ms |
| Total | - | 300ms |

### Goals Internal Sub-Toggle (Card ↔ Tree)

| Step | Description | Duration |
| ---- | ----------- | -------- |
| 1 | Current view fade-out (Card→Tree: includes right panel slide-out) | 100ms |
| 2 | New view fade-in (Tree→Card: includes right panel slide-in) | 100ms |
| Total | - | 200ms |

### Card Inline Action Check

| Step | Description | Duration |
| ---- | ----------- | -------- |
| 1 | Checkbox ☐→☑ transition + strikethrough animation | 150ms |
| 2 | Progress bar width update | 300ms ease |
| 3 | Right panel Action state sync | Immediate |

### Right Panel Date Switch (Weekly/Monthly)

| Step | Description | Duration |
| ---- | ----------- | -------- |
| 1 | Selected date highlight transition | 100ms |
| 2 | Action list fade-out | 100ms |
| 3 | New date Action list fade-in | 100ms |
| Total | - | 200ms |

### Mobile Today's Tasks Summary Bar Toggle

| Step | Description | Duration |
| ---- | ----------- | -------- |
| 1 | Tap summary bar → Action list slide-down | 200ms ease |
| 2 | Tap again → Action list slide-up | 200ms ease |

### Tree Inline Goal Creation Node Transition

| Step | Description | Duration |
| ---- | ----------- | -------- |
| 1 | Node creation animation on option selection | 200ms |
| 2 | Next option slide-down | 200ms |
| Total | - | 400ms |

### Tree Auto-Layout

| Step | Description | Duration |
| ---- | ----------- | -------- |
| 1 | Existing nodes horizontal shift (push) | 200ms |
| 2 | New node fade-in | 200ms |
| Total | Steps 1 and 2 run simultaneously | 200ms |

### Tree Node Hover

| Step | Description |
| ---- | ----------- |
| Hover In | cursor: pointer + subtle shadow change |
| Hover Out | Return to original state |
| Duration | 150ms ease |

### Mobile Touch Interactions

| Gesture | Target | Action |
| ------- | ------ | ------ |
| Pinch zoom | Tree canvas | 50%–150% zoom in/out |
| 1-finger pan | Tree canvas | Viewport movement (inertia scroll) |
| Node tap | Tree node | Select node → display detail info |
| Node long-press (300ms) | Tree node | Show context menu |
| Card swipe left | Goal card header area | Quick actions (record/delete) |
| Card header tap | Goal card (title/WHY/progress) | Open S-DETAIL modal |
| Action checkbox tap | Card inline Action | Immediate completion |
| [+ 할 일 추가] tap | Card bottom | Inline text input |
| Weekly strip date tap | Weekly strip | Select date → switch summary bar content |
| Weekly strip left/right swipe | Weekly strip | Move by week |
| Today's tasks summary bar tap | Summary bar | Expand/collapse Action list |
| Emotion emoji tap | Retrospective emotion selection | Select emotion (single) |

### Modal Interactions

| Step | Description | Duration |
| ---- | ----------- | -------- |
| 1 | Background `░░░` overlay fade-in | 150ms |
| 2 | Modal card slide-up + fade-in | 200ms |
| Total | - | 350ms |
| Close | Reverse animation | 250ms |

---

*Document version: v4.4*
*Last modified: January 31, 2026*
*v4.4 changes: S-CARD split layout + inline Action redesign — Desktop 60:40 split (left: cards / right: weekly schedule panel). Added inline Action checklist to cards (current Phase incomplete Actions, max 3 + [+ 할 일 추가]). Right panel: weekly strip (category color dots) + date-based Action list + completion rate. Responsive: Tablet collapse toggle, Mobile weekly strip sticky + today's tasks summary bar (expand/collapse). Right panel hidden when switching to Tree View. Added "On Action check" to Goal card states, new right panel state definitions added. Added card inline Action check, right panel date switch, and mobile summary bar toggle to interaction definitions. Updated mobile touch interaction table (card header tap / Action check / weekly strip, etc.).*
*v4.3 changes: S-CARD compact card redesign — Converted cards to dashboard role. Cards now show only title + WHY + progress + current phase summary; detailed info (Phase Stepper, Action checklist, coaching messages, Cross-Goal, buttons) moved to S-DETAIL modal. Removed card checkbox toggle, removed collapse/expand interaction, removed mobile checkbox tap.*
*v4.2 changes: Subdivided wireframes folder into 6 subfolders — 01-unauthenticated, 02-onboarding, 03-main, 04-goal-modals, 05-retrospective, 06-etc.*
*v4.1 changes: Wireframe file separation — Split 2,687-line body into 17 individual files in the `docs/wireframes/` folder. This file changed to an overview file maintaining only the screen list + state definitions + interaction definitions.*
*v4.0 changes: Full wireframe UI redesign — Rounded corners (`╭╮╰╯`) + shadows (`░░░`) + active tab underline (`━━━`) + Seal speech bubble (`🦭 ◁`), etc., fully replaced to feel like an actual app UI. Layout width expanded to 80–90 characters. S-TREE section significantly expanded (3 → 15 wireframes): Empty State, single/multiple Goals, node anatomy, node states, zoom levels, connection line types, WHY Depth, interaction flows, Auto-Layout, inline Goal creation, context menu, Mobile tree added. Added `░░░` overlay background to modals. Integrated mobile wireframes into each section. Added tree node states/connection line states to state definitions. Added mobile touch/modal animation to interaction definitions.*
*v3.0 changes: Navigation structure reorganization — Changed entire header to 3 main sections [📝 회고] [🎯 목표], removed existing header 📝 icon. S-JRNL → S-RETRO retrospective page full redesign (daily retrospective: daily mood + optional goal-specific record tiles, weekly retrospective P1: auto summary + weekly impressions). S-JOURNAL → S-JRNL-ENTRY name change + emotion wording update. Mobile header update. Retrospective tab nudge state definition change.*
*v2.3 changes: Removed S-GOAL Step 1 [+ 직접 입력] (fixed 9 categories). Added S-CARD card interaction table (card click → S-DETAIL, checkbox toggle, [+ 새 목표 추가] → switch to Tree View, [⋯] dropdown). Specified S-CARD/S-DETAIL HOW hidden when skipped.*
*v2.2 changes: S-GOAL flow refinement — Added Step 2 WHAT refinement (fixed options + AI recommendations), added Step 3 WHY selection (AI recommendations + existing WHY reuse), renumbered to Step 4. Added S-TREE Auto-Layout rules. Added mobile WHAT/WHY wireframes. Added fixed WHAT options table per category. Specified option classification rules (fixed/AI/existing WHY).*
*v2.1 changes: Added S-JRNL reflection-only page wireframes (Desktop/Mobile/Empty State/record complete state), added 📝 icon to all header wireframes, added reflection page state definitions + header 📝 icon state definitions.*
*v2.0 changes: S-TREE tree order changed (WHAT→WHY→HOW), renamed "WHY-based" to "goal structure", added S-DETAIL insights section/edit mode/Action edit wireframes, added S-PHASE growth comparison, added S-COMPLETE WHY-based next Goal recommendation, added S-CARD archive tab.*
*v1.1 changes: Fully absorbed inline wireframes from sections 3–5 of 1-PLAN.md + added shared Action wireframes.*
*v1.0: Separated from section 10 of 1-PLAN.md v4.0.*
