# Product Manager Memory

## Project: vijaybala.com

### Product Context
- **Type:** Personal portfolio site (static landing page → adding interactive wall feature)
- **Tech Stack:** SvelteKit 2.0, Svelte 4.2.7, TypeScript 5, Vite 5
- **Deployment:** Vercel (switching from adapter-auto to adapter-vercel)
- **Backend:** Supabase (Postgres + Phone/SMS Auth + Realtime)
- **Design System:** Minimalist, EB Garamond serif font, centered layouts, black text on white

### Current Features
- Single landing page (`src/routes/+page.svelte`): name, title, location, email, LinkedIn links
- No backend, no auth, purely static
- Font Awesome icons for links

### MVP: Message Wall Feature
**Goal:** Authenticated visitors can post 140-char text messages visible to all.

**Core Requirements:**
- Phone number + SMS OTP auth (Supabase Auth)
- Text-only posts, 140 char max
- Public read (no auth required to view)
- Auth required to post
- Immutable posts (no edit/delete in MVP)

**Technical Decisions:**
- Database: Single `posts` table (id, content, user_id, created_at)
- RLS Policies: Public SELECT, authenticated INSERT only, no UPDATE/DELETE
- Real-time updates via Supabase Realtime (WebSocket)
- No pagination in MVP (acceptable up to ~1000 posts)
- Anonymous posts (no author display)

**Out of Scope (V1):**
- User profiles, display names, avatars
- Post editing/deletion
- Rich text, media, links
- Admin moderation UI
- Rate limiting beyond Supabase defaults
- Analytics (deferred to backlog)

### Agent Ownership
- `backend-architect`: Supabase schema, migrations, RLS policies, SvelteKit server integration (hooks, API routes)
- `svelte-frontend-engineer`: Wall page UI, auth flow, post form, post list, routing, navigation
- `ui`: Design review, visual consistency, responsive layout, accessibility

### Known Risks
1. **SMS Costs:** ~$0.0079/SMS on Supabase free tier. Monitor for abuse. Consider CAPTCHA in V2.
2. **Abuse Prevention:** No moderation UI in MVP. Posts are immutable. RLS prevents unauthorized writes but not spam from legitimate users.
3. **Scale:** No pagination — acceptable for MVP, will break at ~1000+ posts. Success problem.

### Pending Creator Decisions
- SMS budget tolerance
- Launch timeline/hard deadlines
- Comfort level with unmoderated content
- Wall page tagline/description

### Board Location
`/Users/vijaybala/Personal/vijaybala.com/docs/pm/board.md`

---

## Patterns & Learnings

### SvelteKit + Supabase Auth Integration
- Use `@supabase/auth-helpers-sveltekit` for session management
- `hooks.server.ts` handles session loading from cookies on every request
- Store session in `event.locals.session` for server-side access
- Client-side: use Supabase client + SvelteKit stores for reactive auth state

### MVP Scoping Principles (Applied Here)
- Start with anonymous posts before adding profiles (reduce scope)
- Public read access encourages traffic without auth friction
- Immutable posts simplify backend (no UPDATE/DELETE logic, audit logs, or moderation UI)
- Real-time updates add high UX value for low dev cost (Supabase makes this trivial)
- Defer analytics, admin panels, and rate limiting to post-launch

### Task Breakdown Quality Checklist
- Each task has clear owner (single agent)
- Acceptance criteria are testable (no ambiguity)
- Dependencies explicitly listed
- Size estimate (S/M/L) helps prioritization
- Tasks are sequenced to unblock parallelizable work early

### Design Consistency for New Features
- Match existing typography (EB Garamond)
- Maintain centered, minimalist layout
- Ensure mobile responsiveness (existing site is mobile-friendly)
- Use existing link/button patterns before inventing new UI components
