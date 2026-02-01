# P-FLOWS — User Flows

> 7 flows covering onboarding, Goal creation/editing/deletion, daily use, and section navigation

← [Plan Overview](../1-PLAN.md)

---

## 4.1 Onboarding Flow (Simplified)

```text
1. Landing Page
   │  "나를 알아가는 가장 쉬운 방법" + [시작하기]
   ▼
2. Login (Google OAuth via Supabase Auth)
   ▼
3. Welcome Modal
   │  "먼저 하나만 만들어볼까요?"  ← Recommend 1 Goal (not forced)
   ▼
4. Goal Creation Flow (inline refinement in Tree View)
   │  Click through the Tree View → first Goal complete
   ▼
5. Stay in Tree View
   │  After reviewing the completed tree, user manually navigates to Card View tab
   │  Can freely add more Goals afterward (from Card View or Tree View)
```

- **Key point**: Enter Goal creation immediately
- **Recommend 1 Goal**: Guide users to start with a single Goal (prevents overwhelm; not forced)
- **Goal creation location**: Created inline in Tree View → stays in Tree View after completion (user navigates to Card View on their own)
- **Drop-off prevention during onboarding**: If user clicks a view tab during Goal creation, a confirmation modal appears ("목표를 만들고 있었어요. 그만둘까요?")
- **Removed**: Complex onboarding steps like Brain Dump, category sorting, etc.

## 4.2 Goal Creation Flow (Tree View Inline)

> Wireframe → [S-GOAL](../wireframes/02-onboarding/S-GOAL.md)

- **Goal creation always happens in Tree View only** (maintains consistency)
- Clicking [+ 새 목표 추가] in Card View → switches to Tree View and enters inline creation flow
- Multiple selections create independent Goal cards each (e.g., Running and Fitness are separate)
- Goals sharing the same WHY are connected under the same WHY node in Tree View
- Completed Goals remain as nodes in the tree
- Multiple Goals can be created in parallel
- New Goals can also be added under existing WHY nodes
- **Fixed option pools**: WHAT options → [WHAT-OPTIONS.md](../data/WHAT-OPTIONS.md), WHY options → [WHY-OPTIONS.md](../data/WHY-OPTIONS.md)
- **Phase/Action auto-generation**: Rule-based templates → [PHASE-TEMPLATES.md](../data/PHASE-TEMPLATES.md)

## 4.3 Goal Edit Flow

> Wireframe → [S-DETAIL Edit Mode](../wireframes/04-goal-modals/S-DETAIL.md)

- Click [편집] in S-DETAIL → enter inline edit mode
  - WHY: Can add/remove as tags
  - HOW: Inline text input
  - Importance: Radio selection (required/optional)
- **Auto-triggered change history**: When WHY, category, or importance changes → change history modal (S-CHANGE) opens automatically → asks "왜 바꾸나요?"
- [저장] / [취소] buttons displayed
- On successful save, toast notification ("변경사항이 저장되었어요")

## 4.4 Action Add/Edit/Delete Flow

> Wireframe → [S-DETAIL Action Edit](../wireframes/04-goal-modals/S-DETAIL.md)

- Click [+ 할 일 추가] → inline text input field appears within the current Phase
- Enter to save, Esc to cancel
- Existing Action: click text → inline edit mode
- Delete: hover reveals delete icon (🗑️) → click to confirm and delete
- When deleting a shared Action: "이 할 일은 다른 목표에서도 사용 중이에요. 이 목표에서만 연결을 해제할까요?" confirmation

## 4.5 Goal Delete Flow

- Click [🗑️ 삭제] in S-DETAIL → confirmation modal
  - "정말 삭제할까요? 이 목표의 모든 기록이 사라져요."
  - If shared Actions exist: "이 목표만 삭제돼요. 공유된 할 일은 다른 목표에서 유지돼요."
  - [삭제] / [취소] buttons
- On successful deletion, toast notification + return to Card View

## 4.6 Daily Use Flow

```text
[Main - Goals Card View] ← Split layout (left: cards / right: weekly schedule)
        │
        ├── Check Actions directly on card (inline)
        │    ├── Click Action checkbox → immediately marked complete
        │    ├── Progress bar auto-updates + right panel syncs
        │    └── [+ 할 일 추가] → inline input to add Action to current Phase
        │
        ├── Review and check today's tasks in the right panel
        │    ├── Weekly strip shows this week's completion status at a glance
        │    ├── Check off scheduled Actions for today → left card progress syncs
        │    └── Click a date → view Actions for that date
        │
        ├── Click card header → open S-DETAIL modal
        │    ├── View Phase Stepper, coaching messages, Cross-Goal details
        │    ├── [✨ 구체화하기] → answer additional questions to expand structure
        │    ├── [📝 기록] → navigate to S-RETRO page (auto-expands the Goal tile)
        │    ├── [🔗 연결된 목표] → view Cross-Goal mapping
        │    └── When all Actions are complete → Phase completion feedback
        │         ├── View "나의 페이스" data
        │         ├── Coaching message guides to next step
        │         └── [페이스 올리기] → AI suggests a more challenging schedule
        │
        ├── Click [+ 새 목표 추가]
        │    └── Switch to Tree View → enter inline Goal creation flow
        │
        ├── [🌳 트리] sub-toggle within Goals
        │    └── View full goal structure (WHAT → WHY → HOW)
        │    └── Cross-Goal dotted connections show relationships between goals
        │    └── Phase progress visualization
        │    └── (Right panel hides on Tree View entry → full width)
        │
        └── Swipe card left (Mobile, header area)
             └── Quick actions (record / delete)
```

## 4.7 Main Section Navigation Flow

```text
┌────────────────────────────────────────────────────────────────┐
│  [📝 회고]  ←→  [🎯 목표]                                      │
│                   ├─ Card View (default)                       │
│                   └─ Tree View                                 │
│                                                                │
│  회고: Daily/weekly retrospective + emotion flow + timeline    │
│  Goals: Individual goal management (Card View) + structure     │
│         visualization (Tree View)                              │
└────────────────────────────────────────────────────────────────┘

Navigation: Click top main tab → smooth animated transition
Within Goals: Card/Tree sub-toggle (different visualizations of the same Goal data)
```
