# P-TECH — Tech Stack

> Frontend, Backend, and External Services technology choices

← [Plan Overview](../1-PLAN.md)

---

## 7.1 Frontend

| Technology | Version | Purpose |
| ---------- | ------- | ------- |
| Next.js (App Router) | 16.1.x | React SSR/SSG |
| React | 19.2.x | UI Library |
| Tailwind CSS | 4.x | Styling |
| Zustand | 5.x | State Management |
| React Flow or D3.js | - | Tree view visualization (including Cross-Goal dotted lines) |
| **pnpm** | 10.x | Package manager |

> **Notes on using pnpm:**
>
> - Supabase CLI: Use `pnpm dlx supabase` instead of `npx supabase`
> - Add `strict-peer-dependencies=false` to `.npmrc` (prevents some package peer dependency warnings)
> - No additional configuration needed for Vercel deployment — `pnpm-lock.yaml` is auto-detected

## 7.2 Backend (Supabase + Next.js)

| Technology | Purpose |
| ---------- | ------- |
| Supabase | BaaS (Database, Auth, Realtime) |
| Supabase Auth | Authentication (built-in Google OAuth, session management) |
| Supabase Database | Hosted PostgreSQL + Row Level Security (RLS) |
| @supabase/ssr | Supabase client for Next.js server components |
| Next.js Server Actions | Replaces API endpoints (data mutation) |
| Next.js Route Handlers | External API communication (Gemini) |

## 7.3 External Services

| Service | Purpose | Call Method |
| ------- | ------- | ----------- |
| Gemini 3 API (Flash/Pro) | AI recommendations (구체화하기) | Next.js Server Action / Route Handler |
| Google OAuth | Login | Built into Supabase Auth |
