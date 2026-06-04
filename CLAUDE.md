# vijaybala.com — CLAUDE.md

Personal portfolio website for Vijay Bala, Software Engineer in New York.

## Tech Stack

- **Framework**: SvelteKit v2 + Svelte v4, TypeScript strict mode
- **Build**: Vite v5, deployed via `@sveltejs/adapter-vercel` (Node.js 22.x)
- **Fonts**: EB Garamond (serif, body), Inter (sans-serif)

## Project Structure

```
src/
  routes/
    +layout.svelte          # Minimal pass-through layout
    +page.svelte            # Home page — static, links to contact + LinkedIn
static/
  global.css                # Box-sizing reset, font imports, body defaults
```

## Routes

| Route | Auth | Purpose |
|-------|------|---------|
| `/` | None | Home — name, title, links |

## Commands

```bash
npm run dev       # Dev server
npm run build     # Production build (vite build + package)
npm run preview   # Preview production build
npm run check     # svelte-check type checking
```

## Adding a New Page

1. Create `src/routes/[name]/+page.svelte`
2. Add `+page.server.ts` if SSR data loading is needed
3. Add a `CLAUDE.md` in the route directory documenting the page
