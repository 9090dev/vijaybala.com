# AppSec Engineer - Project Memory

## Project: vijaybala.com (SvelteKit + Supabase)

### Architecture
- SvelteKit frontend, Supabase backend (Postgres + Auth + Realtime)
- Auth: Phone OTP via Supabase Auth
- All mutations happen client-side through Supabase JS client (anon key + JWT)
- RLS is the sole authorization enforcement layer -- no server-side API routes for mutations
- Server load functions use `safeGetSession()` which validates JWT via `getUser()`
- Svelte auto-escapes template expressions (no `{@html}` usage) -- XSS risk is low

### Tables
- `profiles`: id (uuid, FK to auth.users), alias, first_name, last_name, created_at, updated_at
- `posts`: id (uuid), content (max 140), alias (denormalized), user_id (FK), created_at

### Security Review (2026-02-07) - Wall Feature
See: [wall-feature-review.md](wall-feature-review.md)

**Key findings:**
1. [HIGH] Posts UPDATE RLS policy too broad -- allows content/timestamp modification, not just alias propagation. Recommended: security-definer function or trigger.
2. [MEDIUM] No DELETE policy on posts -- users cannot delete their own posts.
3. [MEDIUM] Raw Supabase error.message rendered to DOM in fallback error handlers.
4. [LOW] No client-side alias format validation (DB constraint catches it, but leaks error details).
5. [LOW] Profiles publicly readable including full names -- privacy decision needed.

**Positive patterns:**
- safeGetSession() with getUser() server-side validation (hooks.server.ts)
- RLS enabled on all tables with correct uid checks
- DB-level constraints (regex, length, unique indexes) are solid
- Svelte auto-escaping prevents XSS
- Alias propagation correctly scoped with .eq('user_id') + RLS
