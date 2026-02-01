# INU — Database Schema

> Supabase PostgreSQL schema, RLS policies, indexes, and migration SQL

- Data Structure Overview → [P-DATA.md](plan/P-DATA.md)
- Architecture → [4-ARCHITECTURE.md](4-ARCHITECTURE.md)

---

## 1. ER Diagram

```text
auth.users (Supabase built-in)
  └── profiles (1:1)
        ├── daily_journals (1:N, one per day)
        ├── weekly_journals (1:N, one per week, P1)
        ├── actions (1:N, independent entity)
        └── goals (1:N)
              ├── goal_whys (N:M) ─── whys (self-ref: parent_why_id, max depth 5)
              ├── phases (1:N)
              │     └── phase_actions (N:M) ─── actions
              ├── cross_goal_links (N:M self-ref)
              │     └── cross_goal_connections (1:N)
              ├── change_history (1:N)
              └── goal_journals (1:N)
```

Key relationships:
- `actions` is an **independent entity** belonging to the user (not to a specific goal)
- A single Action can be linked to **multiple Phases** via `phase_actions` (shared Action)
- Completing a shared Action → progress reflected across all linked Phases
- `whys` supports self-referencing hierarchy via `parent_why_id` (5 Whys, max depth 5)
- `daily_journals` is one per day per user (mood independent of Goals)
- `goal_journals` is per-Goal reflection (linked to optional phase)

---

## 2. Full Migration SQL

```sql
-- ============================================================
-- INU Database Schema
-- Supabase PostgreSQL Migration: 001_initial_schema.sql
-- ============================================================

-- ────────────────────────────────────────────────────────────
-- Extensions
-- ────────────────────────────────────────────────────────────

create extension if not exists "uuid-ossp";

-- ────────────────────────────────────────────────────────────
-- Custom Types
-- ────────────────────────────────────────────────────────────

create type category as enum (
  'exercise', 'study', 'career', 'finance',
  'relationships', 'hobbies', 'habits', 'reading', 'other'
);

create type priority as enum ('essential', 'optional');

create type current_status as enum (
  'just_starting', 'some_experience', 'actively_doing'
);

create type phase_status as enum ('upcoming', 'in_progress', 'completed');

create type mood as enum ('good', 'neutral', 'tough', 'motivated');

create type theme_preference as enum ('system', 'light', 'dark');

-- ────────────────────────────────────────────────────────────
-- 1. profiles
-- ────────────────────────────────────────────────────────────

create table profiles (
  id                   uuid primary key references auth.users(id) on delete cascade,
  display_name         text,
  avatar_url           text,
  onboarding_completed boolean not null default false,
  theme                theme_preference not null default 'system',
  why_depth_enabled    boolean not null default false,
  created_at           timestamptz not null default now(),
  updated_at           timestamptz not null default now()
);

comment on table profiles is 'User profile extending auth.users (1:1)';

-- Auto-create profile on user signup
create or replace function handle_new_user()
returns trigger as $$
begin
  insert into public.profiles (id, display_name, avatar_url)
  values (
    new.id,
    coalesce(new.raw_user_meta_data ->> 'full_name', new.raw_user_meta_data ->> 'name'),
    coalesce(new.raw_user_meta_data ->> 'avatar_url', new.raw_user_meta_data ->> 'picture')
  );
  return new;
end;
$$ language plpgsql security definer;

create trigger on_auth_user_created
  after insert on auth.users
  for each row execute function handle_new_user();

-- ────────────────────────────────────────────────────────────
-- 2. goals
-- ────────────────────────────────────────────────────────────

create table goals (
  id              uuid primary key default uuid_generate_v4(),
  user_id         uuid not null references auth.users(id) on delete cascade,
  title           text not null,
  category        category not null,
  how             text,                          -- nullable (skippable)
  priority        priority not null default 'optional',
  current_status  current_status not null,
  current_phase   integer not null default 1,    -- tracks which phase is active
  display_order   integer not null default 0,
  completed       boolean not null default false,
  completed_at    timestamptz,
  created_at      timestamptz not null default now(),
  updated_at      timestamptz not null default now()
);

comment on table goals is 'User goals with category, priority, and status';

create index idx_goals_user_id on goals(user_id);
create index idx_goals_user_category on goals(user_id, category);
create index idx_goals_user_completed on goals(user_id, completed);

-- ────────────────────────────────────────────────────────────
-- 3. whys
-- ────────────────────────────────────────────────────────────

create table whys (
  id              uuid primary key default uuid_generate_v4(),
  user_id         uuid not null references auth.users(id) on delete cascade,
  label           text not null,
  parent_why_id   uuid references whys(id) on delete set null,
  depth           integer not null default 1 check (depth >= 1 and depth <= 5),
  created_at      timestamptz not null default now()
);

comment on table whys is 'Shared WHY entity with 5 Whys hierarchy support';

-- Label must be unique per user (same WHY text = same WHY entity)
create unique index idx_whys_user_label on whys(user_id, label);
create index idx_whys_parent on whys(parent_why_id);

-- ────────────────────────────────────────────────────────────
-- 4. goal_whys (N:M join)
-- ────────────────────────────────────────────────────────────

create table goal_whys (
  goal_id   uuid not null references goals(id) on delete cascade,
  why_id    uuid not null references whys(id) on delete cascade,
  primary key (goal_id, why_id)
);

comment on table goal_whys is 'Many-to-many link between goals and WHYs';

create index idx_goal_whys_why on goal_whys(why_id);

-- ────────────────────────────────────────────────────────────
-- 5. phases
-- ────────────────────────────────────────────────────────────

create table phases (
  id               uuid primary key default uuid_generate_v4(),
  goal_id          uuid not null references goals(id) on delete cascade,
  phase_order      integer not null,
  title            text not null,
  status           phase_status not null default 'upcoming',
  coaching_message text,
  started_at       timestamptz,
  completed_at     timestamptz,
  days_taken       integer,                     -- computed on completion
  created_at       timestamptz not null default now(),
  updated_at       timestamptz not null default now()
);

comment on table phases is 'Roadmap phases for each goal (3 phases per template)';

create index idx_phases_goal on phases(goal_id);
create unique index idx_phases_goal_order on phases(goal_id, phase_order);

-- ────────────────────────────────────────────────────────────
-- 6. actions
-- ────────────────────────────────────────────────────────────

create table actions (
  id            uuid primary key default uuid_generate_v4(),
  user_id       uuid not null references auth.users(id) on delete cascade,
  title         text not null,
  completed     boolean not null default false,
  completed_at  timestamptz,
  created_at    timestamptz not null default now(),
  updated_at    timestamptz not null default now()
);

comment on table actions is 'Independent action items (can be shared across phases)';

create index idx_actions_user on actions(user_id);
create index idx_actions_user_title on actions(user_id, title);

-- ────────────────────────────────────────────────────────────
-- 7. phase_actions (N:M join)
-- ────────────────────────────────────────────────────────────

create table phase_actions (
  phase_id      uuid not null references phases(id) on delete cascade,
  action_id     uuid not null references actions(id) on delete cascade,
  display_order integer not null default 0,
  primary key (phase_id, action_id)
);

comment on table phase_actions is 'Many-to-many link between phases and actions';

create index idx_phase_actions_action on phase_actions(action_id);

-- ────────────────────────────────────────────────────────────
-- 8. cross_goal_links
-- ────────────────────────────────────────────────────────────

create table cross_goal_links (
  id              uuid primary key default uuid_generate_v4(),
  source_goal_id  uuid not null references goals(id) on delete cascade,
  target_goal_id  uuid not null references goals(id) on delete cascade,
  created_at      timestamptz not null default now(),
  constraint cross_goal_links_no_self check (source_goal_id != target_goal_id),
  constraint cross_goal_links_unique unique (source_goal_id, target_goal_id)
);

comment on table cross_goal_links is 'Bidirectional links between related goals';

create index idx_cross_goal_source on cross_goal_links(source_goal_id);
create index idx_cross_goal_target on cross_goal_links(target_goal_id);

-- ────────────────────────────────────────────────────────────
-- 9. cross_goal_connections
-- ────────────────────────────────────────────────────────────

create table cross_goal_connections (
  id          uuid primary key default uuid_generate_v4(),
  link_id     uuid not null references cross_goal_links(id) on delete cascade,
  label       text not null,
  description text,
  created_at  timestamptz not null default now()
);

comment on table cross_goal_connections is 'Labeled connections describing how goals relate';

create index idx_cross_goal_conn_link on cross_goal_connections(link_id);

-- ────────────────────────────────────────────────────────────
-- 10. change_history
-- ────────────────────────────────────────────────────────────

create table change_history (
  id          uuid primary key default uuid_generate_v4(),
  goal_id     uuid not null references goals(id) on delete cascade,
  field       text not null,                    -- 'why', 'category', 'priority', 'how', etc.
  old_value   text not null,
  new_value   text not null,
  reason      text not null,                    -- User's answer to "왜 바꾸나요?"
  changed_at  timestamptz not null default now()
);

comment on table change_history is 'Tracks goal attribute changes with user-provided reasons';

create index idx_change_history_goal on change_history(goal_id);
create index idx_change_history_goal_field on change_history(goal_id, field);

-- ────────────────────────────────────────────────────────────
-- 11. goal_journals
-- ────────────────────────────────────────────────────────────

create table goal_journals (
  id          uuid primary key default uuid_generate_v4(),
  goal_id     uuid not null references goals(id) on delete cascade,
  phase_id    uuid references phases(id) on delete set null,
  mood        mood not null,
  content     text,                             -- Optional free text
  created_at  timestamptz not null default now()
);

comment on table goal_journals is 'Per-goal reflection entries with mood and optional notes';

create index idx_goal_journals_goal on goal_journals(goal_id);
create index idx_goal_journals_goal_phase on goal_journals(goal_id, phase_id);
create index idx_goal_journals_created on goal_journals(created_at);

-- ────────────────────────────────────────────────────────────
-- 12. daily_journals
-- ────────────────────────────────────────────────────────────

create table daily_journals (
  id            uuid primary key default uuid_generate_v4(),
  user_id       uuid not null references auth.users(id) on delete cascade,
  journal_date  date not null,                  -- One entry per day per user
  mood          mood not null,
  content       text,
  created_at    timestamptz not null default now(),
  updated_at    timestamptz not null default now()
);

comment on table daily_journals is 'One daily mood record per user per day';

create unique index idx_daily_journals_user_date on daily_journals(user_id, journal_date);

-- ────────────────────────────────────────────────────────────
-- 13. weekly_journals (P1)
-- ────────────────────────────────────────────────────────────

create table weekly_journals (
  id          uuid primary key default uuid_generate_v4(),
  user_id     uuid not null references auth.users(id) on delete cascade,
  week_start  date not null,                    -- Monday of the week, one per user per week
  mood        mood not null,
  content     text,
  created_at  timestamptz not null default now(),
  updated_at  timestamptz not null default now()
);

comment on table weekly_journals is 'Weekly retrospective record (P1)';

create unique index idx_weekly_journals_user_week on weekly_journals(user_id, week_start);

-- ────────────────────────────────────────────────────────────
-- updated_at trigger
-- ────────────────────────────────────────────────────────────

create or replace function update_updated_at()
returns trigger as $$
begin
  new.updated_at = now();
  return new;
end;
$$ language plpgsql;

-- Apply to all tables with updated_at
create trigger set_updated_at before update on profiles
  for each row execute function update_updated_at();

create trigger set_updated_at before update on goals
  for each row execute function update_updated_at();

create trigger set_updated_at before update on phases
  for each row execute function update_updated_at();

create trigger set_updated_at before update on actions
  for each row execute function update_updated_at();

create trigger set_updated_at before update on daily_journals
  for each row execute function update_updated_at();

create trigger set_updated_at before update on weekly_journals
  for each row execute function update_updated_at();
```

---

## 3. Row Level Security (RLS)

All tables have RLS enabled. Users can only access their own data.

```sql
-- ────────────────────────────────────────────────────────────
-- Enable RLS on all tables
-- ────────────────────────────────────────────────────────────

alter table profiles enable row level security;
alter table goals enable row level security;
alter table whys enable row level security;
alter table goal_whys enable row level security;
alter table phases enable row level security;
alter table actions enable row level security;
alter table phase_actions enable row level security;
alter table cross_goal_links enable row level security;
alter table cross_goal_connections enable row level security;
alter table change_history enable row level security;
alter table goal_journals enable row level security;
alter table daily_journals enable row level security;
alter table weekly_journals enable row level security;

-- ────────────────────────────────────────────────────────────
-- profiles
-- ────────────────────────────────────────────────────────────

create policy "Users can view own profile"
  on profiles for select using (auth.uid() = id);

create policy "Users can update own profile"
  on profiles for update using (auth.uid() = id);

-- Insert is handled by trigger (handle_new_user), no user-facing insert policy needed

-- ────────────────────────────────────────────────────────────
-- goals
-- ────────────────────────────────────────────────────────────

create policy "Users can view own goals"
  on goals for select using (auth.uid() = user_id);

create policy "Users can insert own goals"
  on goals for insert with check (auth.uid() = user_id);

create policy "Users can update own goals"
  on goals for update using (auth.uid() = user_id);

create policy "Users can delete own goals"
  on goals for delete using (auth.uid() = user_id);

-- ────────────────────────────────────────────────────────────
-- whys
-- ────────────────────────────────────────────────────────────

create policy "Users can view own whys"
  on whys for select using (auth.uid() = user_id);

create policy "Users can insert own whys"
  on whys for insert with check (auth.uid() = user_id);

create policy "Users can update own whys"
  on whys for update using (auth.uid() = user_id);

create policy "Users can delete own whys"
  on whys for delete using (auth.uid() = user_id);

-- ────────────────────────────────────────────────────────────
-- goal_whys (access via goal ownership)
-- ────────────────────────────────────────────────────────────

create policy "Users can view own goal_whys"
  on goal_whys for select using (
    exists (select 1 from goals where goals.id = goal_whys.goal_id and goals.user_id = auth.uid())
  );

create policy "Users can insert own goal_whys"
  on goal_whys for insert with check (
    exists (select 1 from goals where goals.id = goal_whys.goal_id and goals.user_id = auth.uid())
  );

create policy "Users can delete own goal_whys"
  on goal_whys for delete using (
    exists (select 1 from goals where goals.id = goal_whys.goal_id and goals.user_id = auth.uid())
  );

-- ────────────────────────────────────────────────────────────
-- phases (access via goal ownership)
-- ────────────────────────────────────────────────────────────

create policy "Users can view own phases"
  on phases for select using (
    exists (select 1 from goals where goals.id = phases.goal_id and goals.user_id = auth.uid())
  );

create policy "Users can insert own phases"
  on phases for insert with check (
    exists (select 1 from goals where goals.id = phases.goal_id and goals.user_id = auth.uid())
  );

create policy "Users can update own phases"
  on phases for update using (
    exists (select 1 from goals where goals.id = phases.goal_id and goals.user_id = auth.uid())
  );

create policy "Users can delete own phases"
  on phases for delete using (
    exists (select 1 from goals where goals.id = phases.goal_id and goals.user_id = auth.uid())
  );

-- ────────────────────────────────────────────────────────────
-- actions
-- ────────────────────────────────────────────────────────────

create policy "Users can view own actions"
  on actions for select using (auth.uid() = user_id);

create policy "Users can insert own actions"
  on actions for insert with check (auth.uid() = user_id);

create policy "Users can update own actions"
  on actions for update using (auth.uid() = user_id);

create policy "Users can delete own actions"
  on actions for delete using (auth.uid() = user_id);

-- ────────────────────────────────────────────────────────────
-- phase_actions (access via action ownership)
-- ────────────────────────────────────────────────────────────

create policy "Users can view own phase_actions"
  on phase_actions for select using (
    exists (select 1 from actions where actions.id = phase_actions.action_id and actions.user_id = auth.uid())
  );

create policy "Users can insert own phase_actions"
  on phase_actions for insert with check (
    exists (select 1 from actions where actions.id = phase_actions.action_id and actions.user_id = auth.uid())
  );

create policy "Users can delete own phase_actions"
  on phase_actions for delete using (
    exists (select 1 from actions where actions.id = phase_actions.action_id and actions.user_id = auth.uid())
  );

-- ────────────────────────────────────────────────────────────
-- cross_goal_links (access via goal ownership)
-- ────────────────────────────────────────────────────────────

create policy "Users can view own cross_goal_links"
  on cross_goal_links for select using (
    exists (select 1 from goals where goals.id = cross_goal_links.source_goal_id and goals.user_id = auth.uid())
  );

create policy "Users can insert own cross_goal_links"
  on cross_goal_links for insert with check (
    exists (select 1 from goals where goals.id = cross_goal_links.source_goal_id and goals.user_id = auth.uid())
  );

create policy "Users can delete own cross_goal_links"
  on cross_goal_links for delete using (
    exists (select 1 from goals where goals.id = cross_goal_links.source_goal_id and goals.user_id = auth.uid())
  );

-- ────────────────────────────────────────────────────────────
-- cross_goal_connections (access via link ownership)
-- ────────────────────────────────────────────────────────────

create policy "Users can view own cross_goal_connections"
  on cross_goal_connections for select using (
    exists (
      select 1 from cross_goal_links cgl
      join goals g on g.id = cgl.source_goal_id
      where cgl.id = cross_goal_connections.link_id and g.user_id = auth.uid()
    )
  );

create policy "Users can insert own cross_goal_connections"
  on cross_goal_connections for insert with check (
    exists (
      select 1 from cross_goal_links cgl
      join goals g on g.id = cgl.source_goal_id
      where cgl.id = cross_goal_connections.link_id and g.user_id = auth.uid()
    )
  );

create policy "Users can delete own cross_goal_connections"
  on cross_goal_connections for delete using (
    exists (
      select 1 from cross_goal_links cgl
      join goals g on g.id = cgl.source_goal_id
      where cgl.id = cross_goal_connections.link_id and g.user_id = auth.uid()
    )
  );

-- ────────────────────────────────────────────────────────────
-- change_history (access via goal ownership)
-- ────────────────────────────────────────────────────────────

create policy "Users can view own change_history"
  on change_history for select using (
    exists (select 1 from goals where goals.id = change_history.goal_id and goals.user_id = auth.uid())
  );

create policy "Users can insert own change_history"
  on change_history for insert with check (
    exists (select 1 from goals where goals.id = change_history.goal_id and goals.user_id = auth.uid())
  );

-- No update/delete on change_history (immutable audit log)

-- ────────────────────────────────────────────────────────────
-- goal_journals (access via goal ownership)
-- ────────────────────────────────────────────────────────────

create policy "Users can view own goal_journals"
  on goal_journals for select using (
    exists (select 1 from goals where goals.id = goal_journals.goal_id and goals.user_id = auth.uid())
  );

create policy "Users can insert own goal_journals"
  on goal_journals for insert with check (
    exists (select 1 from goals where goals.id = goal_journals.goal_id and goals.user_id = auth.uid())
  );

-- ────────────────────────────────────────────────────────────
-- daily_journals
-- ────────────────────────────────────────────────────────────

create policy "Users can view own daily_journals"
  on daily_journals for select using (auth.uid() = user_id);

create policy "Users can insert own daily_journals"
  on daily_journals for insert with check (auth.uid() = user_id);

create policy "Users can update own daily_journals"
  on daily_journals for update using (auth.uid() = user_id);

-- ────────────────────────────────────────────────────────────
-- weekly_journals
-- ────────────────────────────────────────────────────────────

create policy "Users can view own weekly_journals"
  on weekly_journals for select using (auth.uid() = user_id);

create policy "Users can insert own weekly_journals"
  on weekly_journals for insert with check (auth.uid() = user_id);

create policy "Users can update own weekly_journals"
  on weekly_journals for update using (auth.uid() = user_id);
```

---

## 4. Supabase Realtime Configuration

Enable realtime on tables that need live updates in the client:

| Table | Realtime | Reason |
| ----- | -------- | ------ |
| `goals` | Yes | Card view updates when goals change |
| `phases` | Yes | Phase status changes (completion) |
| `actions` | Yes | Action toggle reflects on cards + weekly panel |
| `phase_actions` | Yes | New action links |
| `daily_journals` | No | Only fetched on retro page load |
| `goal_journals` | No | Only fetched on demand |
| `change_history` | No | Append-only, fetched on demand |
| `whys` | No | Rarely changes after creation |

```sql
-- Enable realtime for specific tables
alter publication supabase_realtime add table goals;
alter publication supabase_realtime add table phases;
alter publication supabase_realtime add table actions;
alter publication supabase_realtime add table phase_actions;
```

---

## 5. Key Queries Reference

### 5.1 Get Goals with WHYs (Card View)

```sql
select
  g.*,
  array_agg(distinct w.label) as why_labels,
  (
    select count(*) filter (where a.completed)::float
           / nullif(count(*), 0)
    from phase_actions pa
    join actions a on a.id = pa.action_id
    join phases p on p.id = pa.phase_id
    where p.goal_id = g.id
  ) as overall_progress
from goals g
left join goal_whys gw on gw.goal_id = g.id
left join whys w on w.id = gw.why_id
where g.user_id = $1
  and g.completed = false
group by g.id
order by g.display_order;
```

### 5.2 Get Phases with Actions (Goal Detail)

```sql
select
  p.*,
  json_agg(
    json_build_object(
      'id', a.id,
      'title', a.title,
      'completed', a.completed,
      'completed_at', a.completed_at,
      'is_shared', (
        select count(*) > 1
        from phase_actions pa2
        where pa2.action_id = a.id
      )
    ) order by pa.display_order
  ) as actions
from phases p
left join phase_actions pa on pa.phase_id = p.id
left join actions a on a.id = pa.action_id
where p.goal_id = $1
group by p.id
order by p.phase_order;
```

### 5.3 Activity Summary (S-DETAIL)

```sql
-- Average phase duration
select avg(days_taken) as avg_days
from phases
where goal_id = $1 and days_taken is not null;

-- Journal mood counts
select mood, count(*) as count
from goal_journals
where goal_id = $1
group by mood;

-- WHY change count
select count(*) as why_changes
from change_history
where goal_id = $1 and field = 'why';
```

### 5.4 Emotion Chart Data (S-RETRO, last 14 days)

```sql
-- Daily moods (★)
select journal_date, mood
from daily_journals
where user_id = $1
  and journal_date >= current_date - interval '13 days'
order by journal_date;

-- Goal moods (●) with category
select gj.created_at::date as journal_date, gj.mood, g.category
from goal_journals gj
join goals g on g.id = gj.goal_id
where g.user_id = $1
  and gj.created_at >= current_date - interval '13 days'
order by gj.created_at;
```

### 5.5 Weekly Schedule (Right Panel)

```sql
select
  a.id, a.title, a.completed, a.completed_at,
  g.title as goal_title, g.category
from actions a
join phase_actions pa on pa.action_id = a.id
join phases p on p.id = pa.phase_id
join goals g on g.id = p.goal_id
where g.user_id = $1
  and p.status = 'in_progress'
order by g.display_order, pa.display_order;
```

> Note: MVP does not have date-specific scheduling on actions. The weekly panel shows all in-progress actions. Date-specific scheduling comes with Calendar (P1).

### 5.6 Retro Nudge Check

```sql
select exists(
  select 1 from daily_journals
  where user_id = $1 and journal_date = current_date
) as has_recorded_today;
```

### 5.7 Cross-Goal Links for a Goal

```sql
select
  g2.id, g2.title, g2.category,
  array_agg(distinct w.label) as shared_whys
from cross_goal_links cgl
join goals g2 on (
  case when cgl.source_goal_id = $1 then g2.id = cgl.target_goal_id
       else g2.id = cgl.source_goal_id end
)
left join goal_whys gw1 on gw1.goal_id = $1
left join goal_whys gw2 on gw2.goal_id = g2.id and gw2.why_id = gw1.why_id
left join whys w on w.id = gw2.why_id
where cgl.source_goal_id = $1 or cgl.target_goal_id = $1
group by g2.id;
```

### 5.8 WHY-Based Next Goal Recommendation (S-COMPLETE)

```sql
-- Find the most active WHY for the completed goal
with active_why as (
  select gw.why_id, count(gj.id) as journal_count
  from goal_whys gw
  left join goal_journals gj on gj.goal_id = gw.goal_id
  where gw.goal_id = $1
  group by gw.why_id
  order by journal_count desc
  limit 1
)
-- Find other goals with the same WHY that are not completed
select g.id, g.title, g.category
from goals g
join goal_whys gw on gw.goal_id = g.id
join active_why aw on aw.why_id = gw.why_id
where g.user_id = $2
  and g.completed = false
  and g.id != $1
order by g.display_order
limit 3;
```

---

## 6. Phase State Machine

```
              ┌─────────────────────────────────┐
              │                                 │
              ▼                                 │
         ┌─────────┐    previous phase    ┌────────────┐    all actions    ┌───────────┐
         │ upcoming │ ──── completed ────► │ in_progress │ ── completed ──► │ completed │
         └─────────┘                      └────────────┘                   └───────────┘
```

### Transition Rules

| Trigger | From | To | Side Effects |
| ------- | ---- | -- | ------------ |
| Goal created | — | Phase 1 = `in_progress`, rest = `upcoming` | `started_at = now()` on Phase 1 |
| All actions in phase completed | `in_progress` | `completed` | `completed_at = now()`, `days_taken = completed_at - started_at` |
| Previous phase completed | `upcoming` (next phase) | `in_progress` | `started_at = now()` |
| All phases completed | — | — | `goals.completed = true`, `goals.completed_at = now()` |

### Status Mapping for Template Generation

| `current_status` | Starting Phase | Phases Generated |
| ---------------- | -------------- | ---------------- |
| `just_starting` | Phase 1 = `in_progress` | 1, 2, 3 |
| `some_experience` | Phase 2 = `in_progress` | 2, 3 |
| `actively_doing` | Phase 3 = `in_progress` | 3 only |

---

## 7. Table Summary

| # | Table | Rows Per | Key Constraints |
| - | ----- | -------- | --------------- |
| 1 | `profiles` | 1 per user | PK = auth.users FK, auto-created on signup |
| 2 | `goals` | N per user | Indexed by user_id, category, completed |
| 3 | `whys` | N per user | Unique (user_id, label), self-ref parent_why_id |
| 4 | `goal_whys` | N:M | Composite PK (goal_id, why_id) |
| 5 | `phases` | ~1–3 per goal | Unique (goal_id, phase_order) |
| 6 | `actions` | N per user | Independent entity, sharable |
| 7 | `phase_actions` | N:M | Composite PK (phase_id, action_id) |
| 8 | `cross_goal_links` | N:M self | No self-links, unique pair |
| 9 | `cross_goal_connections` | N per link | Labeled connection descriptions |
| 10 | `change_history` | N per goal | Immutable (no update/delete) |
| 11 | `goal_journals` | N per goal | Mood required, content optional |
| 12 | `daily_journals` | 1 per day per user | Unique (user_id, journal_date), upsert pattern |
| 13 | `weekly_journals` | 1 per week per user | Unique (user_id, week_start), P1 |

---

*Document version: v1.0*
*Last modified: February 2, 2026*
*v1.0: Initial database schema based on P-DATA.md. 13 tables, full RLS policies, auto-created profile trigger, updated_at triggers, realtime configuration, key queries reference.*
