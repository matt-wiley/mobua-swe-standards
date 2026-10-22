---
name: sveltekit
kind: framework
coverage: [2, 3]
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

## Backward: v2 (researched 2026-10-22; for repos on SvelteKit 2.x, from the installed @sveltejs/kit 2.70 types and source)
These replace the matching Base rules for a repo that has not moved to v3. Items marked UNVERIFIED need a check against the v3 migration guide.
- Inline config: from kit 2.62 `sveltekit({...})` in `vite.config` accepts config directly (top-level, no `kit: {}`), and `svelte.config.js` is then ignored. Below 2.62, or with no inline config, `svelte.config.js` is still used. Floors on 2.70: Node >= 18.13, TypeScript ^5.3.3 || ^6, Vite 5 to 8, Svelte 4 or 5. The v3 floors (Node 22.17, Svelte 5.57.1, vite-plugin-svelte 7) do not apply. Source: https://svelte.dev/docs/kit/migrating-to-sveltekit-3
- Keep `$lib` (default `src/lib`, extensionless imports). `#lib/*` subpath imports are v3-only. Source: https://svelte.dev/docs/kit/$lib
- `tsconfig.json` extends `./.svelte-kit/tsconfig.json`; `$app/tsconfig` does not exist in v2. Source: https://svelte.dev/docs/kit/configuration#typescript
- Use `$app/state` (since 2.12, needs Svelte 5); `$app/stores` is deprecated but present. `$app/env`, `asset()`/`resolve()` from `$app/paths` and `refreshAll` already exist in 2.70, so the v3 forms are usable; `base`/`assets`/`resolveRoute` are deprecated. Source: https://svelte.dev/docs/kit/migrating-to-sveltekit-2
- Shallow routing uses `pushState`/`replaceState`; `goto(url, { shallow: true })` is v3-only. `invalidateAll` is the v2 API (`refreshAll` also works). Source: https://svelte.dev/docs/kit/shallow-routing
- Use `isHttpError`/`isRedirect` (present in 2.70). `json()`/`text()` from the kit are not deprecated in v2; `Response.json()` is the forward-compatible form. Source: https://svelte.dev/docs/kit/@sveltejs-kit
- Server-only modules in v2 are `*.server.*` files and `$lib/server/`; the v3 "any `server` path segment" rule does not apply. UNVERIFIED. Source: https://svelte.dev/docs/kit/server-only-modules
- Static SPA rules (adapter-static `fallback`, `ssr = false` in the root layout, no server files) and the experimental status of remote functions are identical in v2. Source: https://svelte.dev/docs/kit/single-page-apps
