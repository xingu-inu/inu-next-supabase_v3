# P-DATA — Data Structure

> Supabase PostgreSQL ER diagram, table list, Phase state transitions

← [Plan Overview](../1-PLAN.md)

---

> Detailed schema (SQL, RLS policies, etc.) will be defined in a separate technical document. This document provides only a brief overview of the table structure.

## 8.1 ER Diagram

```text
auth.users (Supabase built-in)
  └── profiles (1:1)
        ├── daily_journals (1:N, one per day)              ← NEW
        ├── weekly_journals (1:N, one per week, P1)        ← NEW
        ├── actions (1:N, independent entity)
        └── goals (1:N)
              ├── goal_whys (N:M) ─── whys (self-ref: parent_why_id, max depth 5)
              ├── phases (1:N)
              │     └── phase_actions (N:M) ─── actions
              ├── cross_goal_links (N:M self-ref)
              │     └── cross_goal_connections (1:N)
              ├── change_history (1:N)
              └── goal_journals (1:N)

* daily_journals: Daily mood record independent of Goals (one per day)
* weekly_journals: Weekly retrospective notes (one per week, P1)
* goal_journals: Same as before — per-Goal reflection records
* actions is an independent entity belonging directly to the user
* A single Action can be linked to multiple Phases (shared Action)
* Completing an Action → progress is reflected across all Phases referencing that Action
```

## 8.2 Table List

| Table | Description | Key Columns |
| ----- | ----------- | ----------- |
| **profiles** | User profile (extends auth.users) | id (FK→auth.users), display_name, avatar_url, onboarding_completed |
| **goals** | Goals | user_id, title, category, how, priority, current_status, current_phase, display_order |
| **whys** | Shared WHY entity (supports 5 Whys hierarchy) | user_id, label (UNIQUE per user), parent_why_id (FK→whys, nullable), depth (1~5, default 1) |
| **goal_whys** | Goal↔WHY many-to-many link | goal_id, why_id |
| **phases** | Roadmap stages | goal_id, phase_order, title, status, coaching_message, started_at, completed_at, days_taken |
| **actions** | Action items (independent entity, shareable) | user_id, title, completed, completed_at |
| **phase_actions** | Phase↔Action N:M link | phase_id, action_id, display_order |
| **cross_goal_links** | Links between goals | source_goal_id, target_goal_id |
| **cross_goal_connections** | Connection details | link_id, label, description |
| **change_history** | Change history | goal_id, field, old_value, new_value, reason, changed_at |
| **goal_journals** | Per-Goal reflection records | goal_id, phase_id(nullable), mood, content(nullable), created_at |
| **daily_journals** | Daily retrospective (daily mood) | user_id, journal_date (UNIQUE per user), mood, content(nullable) |
| **weekly_journals** | Weekly retrospective (P1) | user_id, week_start (UNIQUE per user), mood, content(nullable) |

- All tables: UUID PK, includes created_at/updated_at
- All tables: RLS enabled (users can only access their own data)
- `whys` is separated into its own table to support Cross-Goal mapping ("Goals sharing the same WHY" queries)
- `days_taken` is included in `phases`, eliminating the need for a separate pace_data table
- `actions` is an independent entity belonging directly to the `user`. Can be linked to multiple Phases via `phase_actions` (shared Action)
- When a shared Action is completed → progress is automatically reflected across all Phases referencing it

## 8.3 Phase State Transitions

```text
upcoming → in_progress → completed
   │                        │
   │  When previous Phase   │  When all Actions
   │  is completed          │  are completed
   └────────────────────────┘

* All Phases are visible from the start (no locking)
* upcoming: Not yet started stage (content is visible but visually muted)
* in_progress: Currently active stage (visually highlighted + coaching message)
* completed: Finished stage (shows completion indicator)
```

## 8.4 Current Status Options

| Status | Description | Effect |
| ------ | ----------- | ------ |
| just_starting | 이제 시작할래요 | AI generates roadmap from beginner stage |
| some_experience | 조금 해봤어요 | AI generates roadmap from intermediate stage |
| actively_doing | 열심히 하는 중 | AI generates roadmap from advanced stage |
