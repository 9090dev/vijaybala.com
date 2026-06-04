# Home Page — CLAUDE.md

The home page (`+page.svelte`) is a static, fully client-rendered landing page. No server load function, no data fetching.

## What it renders

```
Vijay Bala
Software Engineer
New York, NY, USA

[Email]  [LinkedIn]
```

Centered flexbox column, full viewport height. Links: `mailto:contact@vijaybala.com` and LinkedIn (external, `target="_blank"`).

## Layout context

The global layout (`+layout.svelte`) is a simple pass-through wrapper that renders `<slot />`.

## Fonts & Styles

Global styles live in `static/global.css`:
- `box-sizing: border-box` reset
- Body font: EB Garamond (serif), loaded via Google Fonts
- Inter (400/500/600) also imported globally

## Adding Navigation

There is no shared nav component. If adding a nav, add it to `src/routes/+layout.svelte`.
