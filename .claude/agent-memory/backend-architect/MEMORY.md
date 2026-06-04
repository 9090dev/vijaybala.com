# Backend Architect Memory — vijaybala.com

## Project Overview
- SvelteKit 2.0 personal portfolio site, Svelte 4.2.7, TypeScript 5, Vite 5
- Deployed to Vercel via `@sveltejs/adapter-vercel`
- Backend: Supabase (Postgres + phone/SMS auth)
- Dependencies: `@supabase/supabase-js@2.95.2`, `@supabase/ssr@0.8.0`

## Key Architecture Decisions
- **Wall feature**: public read, authenticated write (phone auth). Posts table with 140-char limit.
- **Auth pattern**: `safeGetSession()` on `event.locals` — validates JWT server-side via `getUser()`.
- **Session flow**: hooks.server.ts -> +layout.server.ts -> +layout.ts -> +layout.svelte -> pages.
- **RLS**: posts readable by anyone, insertable by authenticated users (user_id = auth.uid()).

## Codebase Patterns
- `tsconfig.json` extends `.svelte-kit/tsconfig.json` — do NOT override `module` or `moduleResolution` (SvelteKit sets `bundler`/`esnext`). Overriding with `NodeNext` breaks `$types` and `$lib` imports.
- `.gitignore` already excludes `.env.*` but explicitly allows `.env.example`.
- `createBrowserClient` from `@supabase/ssr@0.8.0` auto-handles `document.cookie` — no manual cookies config needed in browser.
- `createServerClient` expects `cookies: { getAll, setAll }` — use SvelteKit `event.cookies.getAll()` and `event.cookies.set()`.
- `@supabase/ssr@0.8.0` exports: `createBrowserClient`, `createServerClient`, `isBrowser`, `parse` (deprecated -> use `parseCookieHeader`).

## Files Written (Wall Feature Backend)
- `docs/supabase-schema.sql` — posts table, indexes, RLS policies
- `src/lib/database.types.ts` — hand-written DB types (can be replaced with `supabase gen types`)
- `src/lib/supabase.ts` — browser client factory
- `src/hooks.server.ts` — server client, cookie handling, safeGetSession
- `src/routes/+layout.server.ts` — passes session to client
- `src/routes/+layout.ts` — creates browser client, manages auth invalidation
- `src/routes/+layout.svelte` — onAuthStateChange listener, slot
- `src/routes/wall/+page.server.ts` — loads posts DESC
- `src/app.d.ts` — App.Locals and App.PageData types
- `.env.example` — PUBLIC_SUPABASE_URL, PUBLIC_SUPABASE_ANON_KEY

## Gotchas
- `svelte-check` needs `.env` present to resolve `$env/static/public` imports. Create temp `.env` for CI or use `--threshold warning`.
- The SupabaseClient type from `createServerClient` vs app.d.ts can conflict if module resolution differs (CJS vs ESM). Fixed by not overriding moduleResolution in tsconfig.
