---
name: svelte
kind: framework
coverage: [5]
---
# Svelte standards

## Base (researched at Svelte 5.57, 2026-10-16; moved from sveltekit.md 2026-10-22)
- Always write new code in runes mode. Avoid the legacy features: `$:`, `export let`, `$$props`/`$$restProps`, `on:` directives, `<slot>`/`$$slots`/`<svelte:fragment>`, `<svelte:component>`, `<svelte:self>`, `use:` actions, the `class:` directive, and stores for shared state. Source: https://svelte.dev/docs/svelte/best-practices
- Use `$state` only for values that must trigger updates. Use `$state.raw` for large data that is replaced rather than mutated, such as API responses. Source: same.
- Compute values with `$derived`/`$derived.by` and side-effect-free expressions, not `$effect`. Values that depend on props must be `$derived`. Source: same.
- `$effect` is an escape hatch. Put user-triggered logic in event handlers, sync with external libraries via `{@attach}`, and use `<svelte:window>`/`<svelte:document>` for global listeners. Source: same.
- Events are attributes (`onclick={...}`). Use `{#snippet}`/`{@render}` for composition. Source: same.
- Share state through classes with `$state` fields, scoped with `createContext` instead of module-level state or `setContext`/`getContext`. Source: same.
- Key each `{#each}` block by a unique id, never the index. Use clsx-style `class` arrays or objects, and `style:--name={value}` for CSS variables. Source: same.

### Without SvelteKit (added 2026-10-22)
- Mount components imperatively with `mount(Component, { target, props })` and remove them with `unmount`; `new Component()` is legacy. Pass a `$state` object as `props` and mutate it to update a mounted component from outside (replaces `$set`). Source: https://svelte.dev/docs/svelte/v5-migration-guide, https://svelte.dev/docs/svelte/imperative-component-api
- Runes only compile in `.svelte` and `.svelte.ts`/`.svelte.js` files; `$state(...)` must initialize a variable declaration or class field, not be returned directly. Source: https://svelte.dev/e/state_invalid_placement
