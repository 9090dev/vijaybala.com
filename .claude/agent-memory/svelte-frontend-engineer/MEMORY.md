# Project Memory - vijaybala.com

## Stack
- SvelteKit with Svelte 4 (NOT Svelte 5) - uses `export let data`, `$:` reactive declarations, `on:click` event handlers
- TypeScript throughout (`<script lang="ts">`)
- Supabase for auth (phone OTP) and database (posts table)
- Supabase SSR via `@supabase/ssr` with `createBrowserClient` in layout load
- No Tailwind - vanilla scoped CSS in `<style>` blocks
- EB Garamond serif font loaded via Google Fonts in `/static/global.css`
- Body has `margin: 0; padding: 0` globally
- Global `box-sizing: border-box` reset in `/static/global.css`
- Deployment target: Vercel adapter

## Architecture
- `+layout.server.ts` - exposes session from `safeGetSession()`
- `+layout.ts` - creates browser supabase client, merges session, depends on `supabase:auth`
- `+layout.svelte` - listens to `onAuthStateChange`, calls `invalidate('supabase:auth')` on session change; just renders `<slot />`
- Data flows: `data.supabase` and `data.session` available on all pages via layout
- Database types in `src/lib/database.types.ts` (hand-written, not code-gen)
- `app.html` wraps `%sveltekit.body%` in a plain `<div>` (no styling), links `/global.css`

## Design System
- Minimal, personal site aesthetic - not SaaS
- Colors: #333 dark text/buttons, #666/#999/#aaa for secondary text, #ccc/#eee borders
- Inputs: 1px solid #ccc border, #fafafa background, 4px border-radius, EB Garamond font
- Buttons: #333 bg with #fff text, 4px border-radius, 0.4 opacity when disabled
- No shadows, no heavy borders - subtle and understated
- Max content width: 520px centered for wall page

## Key Files
- `/src/routes/+page.svelte` - Landing page (centered flex, h1 + links)
- `/src/routes/wall/+page.svelte` - Wall page (auth flow + post form + post list)
- `/src/routes/wall/+page.server.ts` - Server load for posts
- `/static/global.css` - Global styles (font, body margin, box-sizing reset)
- `/src/app.html` - HTML shell with viewport meta, global.css link

## Patterns
- Auth flow uses a 3-step state machine: 'phone' -> 'otp' -> 'done'
- Realtime subscriptions via `supabase.channel().on('postgres_changes', ...)` in onMount
- Channel cleanup in onDestroy with `supabase.removeChannel(channel)`
- `invalidate('supabase:auth')` triggers re-run of layout + page load functions
- Relative time uses inline `timeAgo()` helper (no external lib)

## Bugs Fixed
- Form inputs overflowing `.wall` container: missing `box-sizing: border-box` global reset. See `debugging.md`.
- Mobile padding: changed `.wall` mobile padding from `20px 0 60px` to `20px 16px 60px`
- Realtime posts clobbered by `$: posts = data.posts ?? []` reactive overwrite after `invalidate('supabase:auth')`. Fixed with identity-tracked merge and removed unnecessary invalidation from `submitPost()`. See `debugging.md`.
