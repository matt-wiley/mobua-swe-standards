---
name: sveltekit
kind: framework
coverage: [3]
---
# SvelteKit (and Svelte 5) standards

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
- Always write new code in runes mode. Avoid the legacy features: `$:`, `export let`, `$$props`/`$$restProps`, `on:` directives, `<slot>`/`$$slots`/`<svelte:fragment>`, `<svelte:component>`, `<svelte:self>`, `use:` actions, the `class:` directive, and stores for shared state. Source: https://svelte.dev/docs/svelte/best-practices
- Use `$state` only for values that must trigger updates. Use `$state.raw` for large data that is replaced rather than mutated, such as API responses. Source: same.
- Compute values with `$derived`/`$derived.by` and side-effect-free expressions, not `$effect`. Values that depend on props must be `$derived`. Source: same.
- `$effect` is an escape hatch. Put user-triggered logic in event handlers, sync with external libraries via `{@attach}`, and use `<svelte:window>`/`<svelte:document>` for global listeners. Source: same.
- Events are attributes (`onclick={...}`). Use `{#snippet}`/`{@render}` for composition. Source: same.
- Share state through classes with `$state` fields, scoped with `createContext` instead of module-level state or `setContext`/`getContext`. Source: same.
- Key each `{#each}` block by a unique id, never the index. Use clsx-style `class` arrays or objects, and `style:--name={value}` for CSS variables. Source: same.
