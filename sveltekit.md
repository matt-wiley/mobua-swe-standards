---
name: sveltekit
kind: framework
coverage: [3]
---
# SvelteKit standards

## Base (researched at SvelteKit 3.1 / Svelte 5.57, 2026-10-16)

### Project setup (SvelteKit 3)
- Configure SvelteKit through the `sveltekit({...})` plugin in `vite.config.js`. `svelte.config.js` is no longer supported, and `kit.*` options are now top-level. Minimum versions: Node 22.17, TypeScript 6, Svelte 5.57.1, Vite 8.0.12, vite-plugin-svelte 7. Source: https://svelte.dev/docs/kit/migrating-to-sveltekit-3
- Define the `#lib`/`#lib/*` subpath imports in `package.json` and import with file extensions (`#lib/foo.js`). `$lib` has been removed. Source: same.
- Extend `$app/tsconfig` in `tsconfig.json` with explicit `include`/`exclude`, not `.svelte-kit/tsconfig.json`. Source: same.
- Use `$app/state` (not the removed `$app/stores`), `$app/env` (not `$app/environment`), and `asset()`/`resolve()` from `$app/paths` (not `base`/`assets`/`resolveRoute`). Source: same.
- Navigation: use `goto(url, { shallow: true })` instead of `pushState`/`replaceState`, and `refreshAll` instead of `invalidateAll`. `goto` rejects external URLs; use `window.location.href` for those. Handle the `{ type: 'error' }` branch of `preloadData`. Source: same.
- Treat `page.url` as readonly; copy it before changing it. Source: same.
- Check errors with `isHttpError`/`isRedirect`, not `instanceof`. Use `Response.json()` and `new Response()`; `json()` and `text()` from the kit are deprecated. Source: same.
- Files or folders whose name contains a `server` segment are server-only. Source: same.

### Static SPA mode
- For an SPA served by another backend, use `@sveltejs/adapter-static` with a `fallback` page, plus `export const ssr = false` in the root `+layout`. Prerender individual pages with `prerender = true; ssr = true` where possible. Don't name the fallback `index.html`, because it can conflict with prerendering. Don't use `+page.server.*`, `+layout.server.*` or `+server.*` files. Source: https://svelte.dev/docs/kit/single-page-apps
- Remote functions are still experimental. Don't use them in a static SPA. Source: https://svelte.dev/docs/kit/migrating-to-sveltekit-3

### Svelte 5 components
- See `svelte.md` (moved there 2026-10-22 so non-Kit projects can use it).
