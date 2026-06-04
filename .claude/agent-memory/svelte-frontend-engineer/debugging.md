# Debugging Notes

## Form inputs overflowing container (Feb 2026)

**Symptom**: All elements on the wall page appeared 100% screen width, even though `.wall` had `max-width: 520px` and `margin: 0 auto`.

**Root cause**: No global `box-sizing: border-box` reset. The `.auth-form` and `.post-form` containers use `display: flex; flex-direction: column`, which applies `align-items: stretch` by default. This stretches child `<input>` and `<textarea>` elements to 100% of the container width. But with the default `box-sizing: content-box`, the padding (e.g., `10px 12px`) and border (`1px solid`) are added *on top of* that 100% width, causing elements to overflow the container by ~26px. This overflow made the page scrollable horizontally and gave the appearance that everything was full-width.

**Fix**: Added universal `box-sizing: border-box` reset to `/static/global.css`:
```css
*,
*::before,
*::after {
  box-sizing: border-box;
}
```

**Lesson**: Always include a `box-sizing: border-box` reset in any project's global CSS. Without it, any element with padding or border that is sized to 100% (whether explicitly or via flex stretch) will overflow its container. This is a foundational CSS reset that prevents an entire category of layout bugs.

## Realtime posts clobbered by reactive statement (Feb 2026)

**Symptom**: After creating a post on the wall page, the new post did not appear until manual page refresh. The post was successfully inserted into the database.

**Root cause**: The reactive statement `$: posts = data.posts ?? []` unconditionally overwrote the local `posts` array every time `data` changed. After inserting a post, `submitPost()` called `invalidate('supabase:auth')`, which re-ran the layout load. Even though the page server load did not depend on `supabase:auth`, the reactive statement still fired because `data` got a new object reference from the layout re-run. The realtime channel correctly prepended the new post, but the reactive overwrite immediately replaced it with stale server data.

**Fix**: Two changes:
1. Changed `$: posts = data.posts ?? []` from a blind overwrite to an identity-tracked merge. Introduced `lastServerPosts` to track the previous server data reference. Only merge when the reference actually changes, and preserve any realtime-only posts not yet in the server response.
2. Removed the unnecessary `invalidate('supabase:auth')` from `submitPost()` -- posting does not change auth state, so the invalidation was both unnecessary and harmful.

**Lesson**: In Svelte 4, `$: localVar = data.prop` is a footgun when `localVar` is also mutated by realtime/websocket callbacks. The reactive statement fires whenever `data` changes for any reason (not just when that specific prop changes), and unconditionally overwrites local mutations. Pattern: seed the variable once with `let x = data.prop`, then use a guarded reactive block that checks whether the server data actually changed before merging.
