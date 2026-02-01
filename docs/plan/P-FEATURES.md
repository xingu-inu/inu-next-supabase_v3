# P-FEATURES — Core Features

> Full feature spec covering Goal creation, Card View, Tree View, Coaching, AI Recommendations, Retrospective, etc. (3.1–3.10)

← [Plan Overview](../1-PLAN.md)

---

## 3.1 Refine (Goal Creation Flow)

**Core idea**: Create Goals directly inside the Tree View. No separate modal — clicking a node branches downward to complete the structure. **Multiple selection** to break goals into smaller pieces.

> Wireframe → [S-GOAL](../wireframes/02-onboarding/S-GOAL.md)

**Characteristics:**
- The Tree View itself serves as both the Goal creation flow and the result visualization
- Each selection creates a node and branches downward
- **Multiple selection allowed**: sub-items and WHY can each have multiple selections → break into smaller pieces
- **Each selection becomes an independent Goal**: e.g. 러닝 and 헬스 become separate Goal cards with separate Phase/Action
- **Shared WHY**: Goals sharing the same WHY are naturally connected in the tree
- Multiple categories can be created in parallel (multiple Goals at once)
- Completed Goals remain in the tree as visualization

**Internal mapping** (transparent to the user):

| Question | Internal Layer | Required | Multiple Selection |
| ---- | ----------- | --------- | --------- |
| "어떤 것을 시작해볼까요?" | WHAT (Category) | Required | Single |
| "어떤 ~이요?" | WHAT (Refinement) | Required | ✅ Multiple allowed (each becomes an independent Goal). Encourages granularity — see below |
| "왜 하고 싶어?" | WHY | Required | ✅ Multiple allowed (Cross-Goal mapping) |
| "어떻게/얼마나?" | HOW | Optional (if skipped, HOW row is hidden on card/detail) | Single |
| "중요도는?" | PRIORITY | Required | Single |
| "지금 어디쯤이에요?" | STATUS | Required | Single |
| "어디서?" / "누구와?" | WHERE / WHO | Moves to Refine flow | - |

> **No limit on multiple selections** — select as many as desired. UI handles overflow with scroll/collapse.

**Goal Granularity Principle: "One Goal = One Specific Activity"**

- A Goal must be **specific enough** so that multiple types of activities are not mixed within it
- Example: "헬스" → separate independent Goals for "하체 운동", "등 운동", "어깨 운동"
- **AI-driven granularity prompting**: At the WHAT refinement step, AI suggests "이 목표를 더 나눌 수 있어요" with additional branches
  - Example: User selects "헬스" → AI asks "어떤 부위 운동이요?" → suggests options like 하체, 등, 어깨
  - If the user declines, proceed as-is (not mandatory)
- **Handling many Goals**: Cards use a compact dashboard format so multiple Goals fit on one screen + category filter for management
- No tree structure inside Actions — maintaining the Simplicity principle. Granularity is resolved at the Goal level

## 3.2 Card View (Default UI)

Main screen. **Left card list + Right weekly schedule panel** in a split layout. Check off Actions directly from cards; see today's schedule at a glance on the right.

> Wireframe → [S-CARD](../wireframes/03-main/S-CARD.md)

**Layout**: Desktop 60:40 split (left cards / right weekly schedule). Tablet has a toggle to collapse the right panel. Mobile uses a sticky weekly strip + today's to-do summary bar.

**Card View tabs**:

| Tab | Description |
| -- | ---- |
| **진행 중** (default) | List of in-progress Goal cards |
| **완료한 목표** | List of completed Goals. Muted style. Click to view journey summary (S-COMPLETE content) |

**Goal Card components (Actionable Card)**:

| Element | Description |
| ---- | ---- |
| Category icon + title | 💪 러닝 (header area — click to open S-DETAIL modal) |
| Priority label | 🔴 필수 / ⚪ 선택 (icon + text) |
| Reason | Why the user wants to do this (multiple allowed, separated by ·) |
| Progress | 📍 Percentage bar (based on overall phases) |
| Current phase name | "2️⃣ 습관 만들기" |
| **Inline Action checklist** | Incomplete Actions from the current Phase (max 3; overflow shows "외 N개 더보기"). Checkbox click immediately marks as complete |
| **[+ 할 일 추가]** | Inline text input to add an Action to the current Phase (Enter to save, Esc to cancel) |

> Clicking the card header area (title/WHY/progress) opens the **S-DETAIL modal** for detailed viewing/editing. Action checkboxes can be completed directly on the card (no modal needed).

**Right panel (Weekly/Monthly Schedule)**:

| Element | Description |
| ---- | ---- |
| **View toggle** | [주간] (default) / [월간] switch |
| Weekly strip (default) | This week's 7 days (Mon–Sun). Completed Actions shown as category-colored dots |
| Monthly grid | Mini calendar with category-colored dots per date. Click a date to see its Actions |
| Date selection | Click a date → shows that date's Action list (default: today) |
| Action list | All Actions for the selected date (category icon + name + checkbox) |
| Completion rate | "✅ 완료 1/3" |

> The right panel provides schedule overview in weekly or monthly format. Weekly view is the default.
> When switching to Tree View, the right panel hides → tree canvas uses full width.

## 3.3 Tree View (Goal Structure Visualization + Cross-Goal Connections)

Visualizes relationships between goals as a graph. See the big picture + discover connections between goals. **Same order as the creation flow** (WHAT → WHY → HOW), arranged top to bottom.

> Wireframe → [S-TREE](../wireframes/03-main/S-TREE.md)

**Tree layer structure** (top → bottom):

| Layer | Role | Example |
| ------ | ---- | ---- |
| **WHAT (Category)** | Top level. What are you doing | 💪 운동 |
| **WHAT (Refinement)** | Specific goal | 러닝, 헬스 |
| **WHY** | Why are you doing it. Goals sharing the same WHY connect here | 🎯 건강, 🎯 스트레스 해소 |
| **HOW (Phase/Action)** | How to do it. Roadmap phases and action items | 1️⃣ 기초 준비 → 2️⃣ 습관 만들기 |

> WHY sits in the middle, acting as a hub that **connects "what" above to "how" below**. Goals sharing the same WHY naturally converge at the WHY layer, then diverge into their own execution paths in the HOW layer below.

**Value of the Tree View**:

- Goals sharing the same WHY are naturally connected at the WHY layer
- **Cross-Goal dotted lines** reveal hidden connections between goals
- "Which of these two matters more?" → the tree makes it clear
- Tracing from WHAT down through WHY to HOW — see the full context of a goal at a glance
- Visually grasp the **impact** of a single activity contributing to multiple WHYs

**WHY Node Internal Expansion (Depth in WHY Node)** — Optional feature:

> Activated only when "이유 더 파보기" is turned ON in settings. Default is OFF.

- **OFF (default)**: Simple single-level WHY display
- **ON**: Inside the WHY node, clicking "왜?" allows exploration **up to 5 levels deep** (5 Whys framework)
  - **depth indicator** shown (1/5, 2/5... 5/5) — visually guides current depth
  - At level 5: **"핵심 이유에 도달했어요"** message displayed
  - Each level's WHY is linked to its parent WHY (`parent_why_id`)
- **ON→OFF toggle**: depth 2+ WHYs are hidden from UI (data preserved). Toggling back to ON restores them

## 3.4 Refine (Expanding Existing Goals)

From an existing Goal card, click [✨ 구체화하기] → AI suggests context-appropriate follow-up questions (target distance, companions, supplies, etc.).

> Wireframe → [S-REFINE](../wireframes/04-goal-modals/S-REFINE.md)

## 3.5 Adaptive Roadmap & Coaching System

**Philosophy**: Never ask about deadlines. AI automatically adapts to the user's pace. **Show the entire roadmap** so the user can see the big picture, while providing visual emphasis on the current phase and **1:1 coaching messages**.

> Per Cornell's "GAINS & DRAINs" framework, deadline pressure can trigger "Negative self-efficacy." Instead, **short-term achievement highlights + personalized feedback** are more effective.

**How it works:**

1. At Goal creation, only ask "지금 어디쯤이에요?"
2. AI auto-generates a roadmap based on current status
3. **Entire roadmap is visible** — all phases and Actions are shown
4. **Current phase is visually highlighted** + coaching message provided for each phase
5. On Phase completion: progress feedback + next-phase coaching

> Wireframe → [S-DETAIL](../wireframes/04-goal-modals/S-DETAIL.md)

### Coaching System (1:1 Care)

Provides context-appropriate coaching messages at each phase to help the user understand **"why this matters right now"** and maintain motivation.

**Coaching message generation**: Hybrid approach

| Method | Description |
| ---- | ---- |
| Rule-based | Pre-defined message templates per Phase (fast response) |
| AI personalization | Gemini personalizes based on the user's WHY, progress, and change history |

**Coaching message types:**

| Type | Description | Example |
| ---- | ---- | ---- |
| Phase importance | Why this phase matters right now | "자기분석은 취업의 기초예요." |
| Big-picture connection | Position within the overall roadmap | "여기까지 오면 전체의 75%예요." |
| Cross-Goal impact | Contributing to other goals as well | "이 단계에서 커리어 성장에도 기여해요." |
| Incremental next step | Suggest an achievable next action | "먼저 강점 3가지만 적어보는 것부터 시작해봐요." |
| WHY reminder | Recall why the user is doing this | "경제적 독립을 위해 잘 나아가고 있어요." |
| Change-based advice | Personalized based on past change history | "전에 WHY를 바꿨을 때처럼, 방향을 점검해보는 것도 좋아요." |
| Pace reference | Based on previous Phase duration | "이전 단계를 10일 만에 완료했어요. 이번도 나의 페이스로." |

**Conditional branching based on past data** (rule-based, no AI needed):

| Condition | Referenced Data | Coaching Message Example |
| ---- | ----------- | ---------------- |
| Recent journal mood is 😣 | goal_journals.mood | "지난번에 힘들다고 했었죠. 작은 것부터 다시 시작해봐요." |
| WHY was recently changed | change_history (field='why') | "최근에 방향을 바꿨으니, 새 방향으로 천천히 가봐요." |
| High Action completion rate (≥80%) | actions.completed aggregate | "꾸준히 잘하고 있어요. 이 흐름 유지해봐요." |
| Previous Phase days_taken exists | phases.days_taken | "이전 단계를 N일 만에 완료했어요. 이번도 나의 페이스로." |
| No condition matched (default) | - | Use default Phase template message |

**Key point: Guide toward an achievable next step from the current state, not an unrealistically high goal**

### Phase Completion Feedback

> Wireframe → [S-PHASE](../wireframes/04-goal-modals/S-PHASE.md)

- "나의 페이스": shows the number of days spent on this phase
- **Growth comparison**: Compare duration with the previous Phase or a similar Phase from a previous Goal (e.g. "지난 기초 단계보다 3일 빨라졌어요")
  - If no comparison target exists (first Phase/Goal), hide the comparison line
  - Implementation: Compare completed phases.days_taken for the same user (simple query)
- Next-phase coaching message + WHY reminder
- [페이스 올리기] button

### Core Principles

| Principle | Description |
| ---- | ---- |
| No deadline questions | Remove pressure, respect the user's own pace |
| Full roadmap visibility | See the big picture and understand why "now" matters |
| Current phase emphasis | Visually guide the user to where they should focus |
| Per-phase coaching messages | Provide context for "why this matters right now" |
| Progress visualization | Continuously show where the user stands in the overall journey |
| Completion feedback | Strengthen self-understanding with "나의 페이스" data |
| Incremental next steps | Guide toward achievable next actions instead of unrealistic goals |
| Optional challenge | Go faster if desired, otherwise stay the course ([페이스 올리기]) |
| WHY reminder | Recall "why I'm doing this" at phase completion → boost motivation |

### [페이스 올리기] Behavior Definition

- When clicking [페이스 올리기] on Phase completion feedback (S-PHASE):
  - MVP (rule-based): Suggest adding 1–2 bonus Actions to the next Phase, or propose pulling Actions from the next Phase into the current one
  - User approves/rejects the suggestion → on approval, Actions are added and Phase structure is updated
  - P1 (AI): Gemini generates context-appropriate challenge tasks

### Goal Completion Feedback (All Phases Complete)

> Wireframe → [S-COMPLETE](../wireframes/05-retrospective/S-COMPLETE.md)

- On Goal completion, move to **completed list** (separated from Card View, viewable in the 완료한 목표 tab)
- Provide **journey summary** for self-understanding (total days, per-phase duration, journal count, emotion flow)
- **WHY-based next Goal recommendation**: Recommend based on the most active WHY (journal count + positive emotion ratio)
  - Prioritize un-started Goals among connected Cross-Goals
  - If no recommendation target exists, prompt new Goal creation
  - Implementation: Aggregate goal_journals + goal_whys tables (no AI needed)

## 3.6 AI Recommendations (Gemini 3)

| Feature | Description |
| ---- | ---- |
| **Option recommendation** | Recommend related sub-options when entering a goal |
| **WHY recommendation** | Recommend reasons/motivations matching the goal |
| **Action generation** | Auto-generate specific actions for achieving the goal |
| **Expansion suggestions** | Suggest questions to further refine existing goals |

**Implementation approach**: Rule-based + Gemini 3 hybrid
- Rule-based: Pre-defined option pools per category (fast response)
- Gemini 3: Context-aware dynamic recommendations (personalized)
- Call path: Next.js Server Action → Gemini 3 API (API Key in env vars, no client exposure)

**AI Fallback (failure handling)**:

| Scenario | Response |
| ---- | ---- |
| API call in progress | Show skeleton shimmer loading on option cards |
| Timeout (5 seconds) | Auto-switch to rule-based option pool |
| API error | "AI 추천을 불러오지 못했어요" notice + show rule-based options |
| Rule-based default behavior | Display from pre-defined option pool per category (always works) |
| **MVP (AI P1)** | **Fixed option pools only** — WHAT: fixed WHAT options per category from 2-WIREFRAMES, WHY: [§6.3](P-CATEGORY.md), Phase/Action: [§6.4](P-CATEGORY.md) + [PHASE-TEMPLATES.md](../data/PHASE-TEMPLATES.md) |

## 3.7 Change History Tracking

When a Goal's attributes (WHY, category, frequency, etc.) are changed later, ask **"왜 바꾸나요?"** → record the reason for the change.

> Wireframe → [S-CHANGE](../wireframes/04-goal-modals/S-CHANGE.md), [S-DETAIL Change History tab](../wireframes/04-goal-modals/S-DETAIL.md)

**Self-understanding value:**

| Value | Description |
| ---- | ---- |
| WHY change tracking | "I originally did it for health, but stress relief turned out to be the real reason" |
| Pattern discovery | "I always change my reason after 2 weeks" |
| Long-term data | Long-term data accumulation for deeper self-understanding |

## 3.8 Cross-Goal Mapping (Goal Impact Map)

**Core idea**: Visualize how a single activity contributes to multiple goals. See at a glance "which goals move forward simultaneously when I do this."

**Connection creation methods**: Hybrid

| Method | Description |
| ---- | ---- |
| Auto-connection | Goals sharing the same WHY are automatically connected |
| AI recommendation | Gemini recommends connection points with existing Goals |
| User approval | Users can approve/reject/modify recommended connections |

**How it works:**

1. Selecting multiple WHYs during Goal creation → automatically creates Cross-Goal connections
2. AI recommends connection points with existing Goals → user approves/rejects
3. Connected goals are displayed on Goal cards and in Tree View

> Wireframe → [S-DETAIL](../wireframes/04-goal-modals/S-DETAIL.md), [S-TREE](../wireframes/03-main/S-TREE.md)

**Cross-Goal connection types:**

| Type | Description | Example |
| ---- | ---- | ---- |
| Shared WHY | Goals sharing the same WHY | 러닝 + 식단 → WHY: 건강 |
| **Shared Action** | **A single Action linked to Phases of multiple Goals simultaneously** | **Java 공부 → 취업준비 Phase 1 + 오픈소스 기여 Phase 2** |
| Activity contribution | One activity also contributes to another goal | Spring 공부 → contributes to "기술 면접 대비" in 취업 준비 |
| Habit chain | One habit serves as the foundation for another | 아침 기상 → 러닝 → 집중력 → 공부 |

**Shared Action behavior:**

- A single Action (e.g. "Java Spring 공부") can be linked to Phases of multiple Goals simultaneously
- Checking it off on one side → marks complete on both sides → reflected in both progress bars
- 🔗 icon indicates the Action is also connected to another Goal

**Shared Action creation method (MVP):**

- **Auto-detection**: When adding a new Action, if an existing Action with the same title exists, suggest "이미 '취업 준비'에 같은 할 일이 있어요. 연결할까요?" (string matching)
- On user approval, link to the existing Action; on rejection, create an independent Action
- AI-based similar Action recommendations added in P1

> Wireframe → [S-DETAIL Shared Action](../wireframes/04-goal-modals/S-DETAIL.md)

**Value of Cross-Goal Mapping:**

| Value | Description |
| ---- | ---- |
| **Motivation** | "Doing this one thing advances 3 goals at once" |
| **Prioritization** | Activities contributing to more goals naturally rank higher |
| **Efficiency** | Discover overlapping activities to save time |
| **Self-understanding** | "So my goals are connected like this" → deeper understanding |

## 3.9 Retrospective System

**Core idea**: A retrospective system for comfortably looking back on the day and the week, like writing a diary. **"Overall mood of the day" is the #1 priority; per-goal entries are optional**. Access via the main tab [📝 회고] to naturally accumulate qualitative self-understanding data.

> Change History = "what was changed" (structural changes)
> Retrospective = "how I felt" (emotional/situational changes)
> Together → complete context for "why I changed that at that time"

**Two entry points:**

| Entry Point | Location | Purpose |
| ------ | ---- | ---- |
| **Main tab [📝 회고]** | Global header main tab | Record **daily/weekly retrospectives** + look back (S-RETRO page) |
| Goal detail [📝 기록] | Inside S-DETAIL modal | Navigate to S-RETRO page (auto-expand the relevant Goal's entry tile) |

> Two data types: daily_journals (overall daily mood) + goal_journals (per-Goal reflections). The main tab [📝 회고] records both daily mood and per-goal entries at once. The 📝 inside the S-DETAIL modal navigates to the S-RETRO page and auto-expands the relevant Goal's entry tile.

### Daily Retrospective (S-RETRO-DAILY) — P0

Starts with the question **"오늘 하루 어땠어요?"** in a diary format.

> Wireframe → [S-RETRO](../wireframes/05-retrospective/S-RETRO.md)

**Entry flow:**

1. Click main tab [📝 회고] → /retro page (daily retrospective sub-tab is default)
2. **Overall daily mood** selection (required, single): 😊 좋았어 / 😐 보통 / 😣 힘들었어 / 🔥 의욕적
3. Free text (optional): "오늘 하고 싶은 말..."
4. **Per-goal entries** (optional): Active Goal tiles shown below → tap to record that Goal's emotion + a note
5. Save → daily mood saved to daily_journals, per-Goal entries saved to goal_journals

**Core design principles:**

- **Recording just the daily mood is a fully valid entry** — per-goal entries can be skipped
- Instead of a Goal selection dropdown, use **active Goal card tiles** → tap to record, or don't
- One daily entry per date (re-recording overwrites; "다시 기록하기" button)
- Date display: diary-style format like "오늘의 하루 — 2026.01.31 (금)"

### Weekly Retrospective (S-RETRO-WEEKLY) — P1

**A weekly review looking back on the past week.** Auto-summary of daily data + user's weekly reflections.

> Wireframe → [S-RETRO](../wireframes/05-retrospective/S-RETRO.md)

**Structure:**

| Section | Description |
| ---- | ---- |
| **This week's mood summary** (auto-generated) | Daily mood emojis by day of week + "most frequent mood" display. Aggregated from daily_journals |
| **Per-Goal weekly activity** (auto-aggregated) | Per-Goal: journal count + mood distribution + completed Action count. Aggregated from goal_journals + actions |
| **Weekly reflection** (user input) | Weekly mood (😊 좋은 한주 / 😐 무난했어 / 😣 힘든 한주 / 🔥 불태웠어) + free text |
| **Past weekly retrospective history** | List of past weekly retrospective cards. ◀ ▶ week navigation |

**Entry flow:**

1. [📝 회고] → click [주간 회고] sub-tab
2. Review this week's auto-summary (based on daily entries)
3. "이번 주는 어떤 한 주였나요?" → select weekly mood + note (optional)
4. Save → saved to weekly_journals
5. For weeks with no daily entries: "이번 주 기록이 없어요. 일일 회고를 먼저 시작해보세요." notice

**Weekly nudge:**

- If no weekly retrospective exists on Sunday (or Monday), show a ● dot on the retrospective tab
- No push notifications (Calm principle)

### Retrospective Page (S-RETRO) Common Structure

> Wireframe → [S-RETRO](../wireframes/05-retrospective/S-RETRO.md)

**Sub-tabs:** [일일 회고] (default) / [주간 회고] (P1)

**Common elements:**

| Section | Description |
| ---- | ---- |
| **Emotion flow chart** | Dot chart for the last 2 weeks. ★ = overall daily mood (daily_journals), ● = per-Goal mood (goal_journals). Y-axis: 4 emotion types, X-axis: dates. Color-coded by Goal category |
| **Unified timeline** | Daily entries + per-Goal journal notes displayed in chronological order. Filter tabs: [전체] [하루] [💪] [📚] etc. |

**Retrospective tab nudge:**

- If today's daily retrospective is not recorded, show a small dot (●) on the [📝 회고] tab
- Dot disappears once recorded
- No push notifications (Calm principle)

**Self-understanding value:**

| Value | Description |
| ---- | ---- |
| Daily emotion logging | "How was my day overall" — self-state awareness independent of Goals |
| Per-Goal emotion logging | Accumulate "what emotions I felt while working on this goal" |
| Per-Phase context | Which phases were hard, which phases were enjoyable |
| Weekly rhythm discovery | Discover personal rhythms through day-of-week mood patterns |
| Change History complement | View emotional context at the time of WHY changes |
| Emotion flow visualization | Chart gives an at-a-glance view of the last 2 weeks' emotion patterns (daily + per-Goal) |
| Long-term patterns (Phase 2) | AI analyzes emotion flow: "러닝은 항상 🔥로 시작해서 😐로 가네요" |

> **MVP (P0)**: Daily retrospective (daily mood + optional per-goal entries) + emotion flow chart + unified timeline + Goal card journal modal. **P1**: Weekly retrospective (auto-summary + weekly reflection). AI emotion pattern analysis is Phase 2.

## 3.10 Data Utilization: Insights (Past → Future Flow)

**Core idea**: Connect accumulated past data (journal notes, change history, Phase duration, Action completion rate) to future actions to strengthen self-understanding. MVP includes **only what can be implemented with simple DB aggregations (COUNT, AVG)**.

**Goal Detail Activity Summary (S-DETAIL [정보] tab)**:

> Wireframe → [S-DETAIL](../wireframes/04-goal-modals/S-DETAIL.md)

| Item | Data Source | Display Example |
| ---- | ----------- | --------- |
| Average phase duration | phases.days_taken AVG | "평균 단계 소요: 12일" |
| Journal emotion aggregation | goal_journals.mood COUNT | "성찰 노트: 8개 (🔥 3 · 😊 3 · 😐 1 · 😣 1)" |
| Reason change count | change_history WHERE field='why' COUNT | "이유 변경: 1회" |

- If the Goal lacks sufficient data (e.g. 0 Phases completed), hide the activity summary section
- Implementation: Real-time aggregate queries. No separate table needed

**Full Data Utilization Map:**

```text
[Past Data]                [Utilization Path]                  [Where Users See It]
──────────────────────────────────────────────────────────────────────
Phase duration          →  Reflected in next Phase coaching   →  S-DETAIL coaching message
                        →  Growth comparison at Phase end     →  S-PHASE
                        →  Goal detail activity summary       →  S-DETAIL

Daily retrospective     →  Retrospective emotion flow chart   →  S-RETRO
mood (daily_journals)      (★)
                        →  Retrospective unified timeline     →  S-RETRO
                        →  Weekly retrospective auto-summary  →  S-RETRO-WEEKLY
                           (P1)

Per-Goal journal        →  Retrospective emotion flow chart   →  S-RETRO
mood (goal_journals)       (●)
                        →  Retrospective unified timeline     →  S-RETRO
                        →  Coaching message conditional       →  S-DETAIL coaching message
                           branching
                        →  Goal detail activity summary       →  S-DETAIL
                           (emotion aggregation)
                        →  Emotion flow summary at Goal       →  S-COMPLETE
                           completion

Weekly retrospective    →  Weekly retrospective history       →  S-RETRO-WEEKLY
(weekly_journals)(P1)   →  (Phase 2) Long-term pattern        →  Future
                           analysis

Change history          →  Coaching message conditional       →  S-DETAIL coaching message
                           branching
                        →  Goal detail activity summary       →  S-DETAIL
                           (change count)

Action completion rate  →  Coaching message conditional       →  S-DETAIL coaching message
                           branching
                        →  Weekly retrospective per-Goal      →  S-RETRO-WEEKLY
                           activity (P1)

Cross-Goal data         →  Next Goal recommendation at Goal   →  S-COMPLETE
                           completion
```
