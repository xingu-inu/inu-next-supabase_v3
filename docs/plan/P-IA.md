# P-IA — Information Architecture

> Screen inventory, sitemap, navigation, and URL structure

← [Plan Overview](../1-PLAN.md)

---

## 5.1 Screen Inventory

| Screen ID | Screen Name | Type | Description |
| --------- | ----------- | ---- | ----------- |
| S-LND | 랜딩 | Page | Service introduction for non-authenticated users |
| S-AUTH | 로그인 | Page | Google OAuth |
| S-WEL | Welcome | Modal (Full) | First-visit user welcome + Goal creation start |
| S-GOAL | Goal 생성 | Tree View Inline | Create Goals directly within Tree View |
| S-CARD | Goals - 카드 뷰 | Page (Default) | Goal card list (default screen, includes completed goals tab). Sub-view within Goals section |
| S-TREE | Goals - 트리 뷰 | Page | Goal structure tree visualization + Goal creation. Sub-view within Goals section |
| S-CAL | 캘린더 | Page (P1) | Action schedule visualization |
| S-RETRO | 회고 페이지 | Page | Daily/weekly retrospective + emotion flow chart + unified timeline (accessed via main tab [📝 회고]) |
| S-RETRO-DAILY | 일일 회고 | S-RETRO sub-view | Daily mood + optional per-goal reflection entries |
| S-RETRO-WEEKLY | 주간 회고 | S-RETRO sub-view (P1) | Auto-generated weekly summary + weekly reflection entry |
| S-DETAIL | Goal 상세 | Modal | Goal edit/detail + reflection notes tab + Cross-Goal |
| S-REFINE | 구체화하기 | Modal | Additional refinement questions |
| S-PHASE | Phase 완료 | Modal | Phase feedback + next-step coaching |
| S-CHANGE | 변경 이력 | Modal | Record reason when properties change |
| S-COMPLETE | Goal 완료 | Modal | Journey summary + completion list |
| S-SET | 설정 | Page | Profile, account management, "이유 더 파보기" ON/OFF toggle |
| S-404 | 에러 | Page | Page not found |

## 5.2 Sitemap

```text
INU
│
├── Unauthenticated Area
│   ├── S-LND  랜딩 페이지
│   └── S-AUTH 로그인
│
├── Onboarding (first visit)
│   ├── S-WEL  Welcome
│   └── S-GOAL Goal Creation Flow
│
├── Main (default entry point after authentication)
│   ├── [📝 회고] S-RETRO 회고 페이지
│   │     ├── S-RETRO-DAILY 일일 회고 (default)
│   │     └── S-RETRO-WEEKLY 주간 회고 (P1)
│   ├── [🎯 목표]
│   │     ├── S-CARD 카드 뷰 ← default screen
│   │     └── S-TREE 트리 뷰 (+ inline Goal creation)
│   ├── [📅 캘린더] S-CAL 캘린더 뷰 (P1)
│   ├── [Modal] S-DETAIL Goal detail/edit
│   └── [Modal] S-REFINE 구체화하기
│
├── Common
│   └── S-SET 설정
│
└── Error
    └── S-404 Page not found
```

## 5.3 Navigation

```text
🦭 INU    [📝 회고] [🎯 목표 ▾] [📅 캘린더]    👤
              ●        └─ [카드 뷰] / [트리 뷰]
```

- **Logo** (🦭 INU): Click → main screen (Goals Card View)
- **Main tabs**: [📝 회고] [🎯 목표] [📅 캘린더] — 3 primary sections
  - **[📝 회고]**: Click → retrospective page (/retro). Shows dot (●) nudge if today's daily retrospective is not yet recorded
  - **[🎯 목표]**: Click → Goals section. Switch between [카드 뷰] / [트리 뷰] via internal sub-toggle
  - **[📅 캘린더]**: Click → calendar view (P1)
- **User Avatar**: Click → settings/logout dropdown

> Card View and Tree View are different visualizations of the same Goal data, so they are unified as a sub-toggle within the Goals section. Retrospective is a separate section for recording/reflection activities.
> Wireframes → see [2-WIREFRAMES.md](../2-WIREFRAMES.md) for each screen reference

## 5.4 URL Structure

| Screen | URL |
| ------ | --- |
| S-LND | `/` (unauthenticated) |
| S-AUTH | `/auth` |
| S-CARD | `/` (default after authentication) |
| S-TREE | `/tree` |
| S-RETRO | `/retro` |
| S-CAL | `/calendar` |
| S-SET | `/settings` |

> **Same URL handling**: `/` renders different screens based on authentication state
>
> - Unauthenticated: Landing page (S-LND)
> - Authenticated: Goals Card View (S-CARD)
> - Implementation: Branch based on auth check in Next.js middleware or server component
>
> Modals (S-WEL, S-GOAL, S-DETAIL, S-REFINE) are managed via state without URLs
> Sub-tabs within S-RETRO (daily/weekly) are managed via client state (no separate URLs needed)
