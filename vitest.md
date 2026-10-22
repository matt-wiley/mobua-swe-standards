---
name: vitest
kind: framework
coverage: [4, 5]
---
# Vitest standards

## Base (researched at 5.0, 2026-10-22)

- Vitest 5 needs Vite >= 6.4 (a peer dependency; install it explicitly) and Node >= 22.12. Source: https://vitest.dev/guide/migration
- Config is not searched in parent directories; keep `vitest.config.ts` at the package root or pass `--config`. Source: same
- `clearMocks` defaults to `true`. Still reset what persists across tests: `vi.unstubAllGlobals()`/`unstubGlobals`, `vi.unstubAllEnvs()`/`unstubEnvs`, `vi.useRealTimers()` after fake timers or `setSystemTime`. Source: same; https://vitest.dev/guide/mocking
- Always `await` `expect(...).resolves`/`.rejects` and `toMatchFileSnapshot`; unawaited ones now fail the test. Source: https://vitest.dev/guide/migration
- `vi.mock`/`vi.unmock`/`vi.hoisted` only at module top level (nested scopes throw); `vi.mock` is hoisted above imports. Use `importOriginal` in the factory to override only what you need. Source: same; mocking guide
- `vi.mock` only replaces external access; calls inside the mocked module still hit the originals. Prefer designing a seam over mocking module internals. Source: mocking guide
- Use `vi.fn` inside mock factories and `vi.spyOn(obj, 'name')` for a single method or getter. Use `vi.fn(class ...)` for class fakes; `instanceof` and prototype methods work. Source: same
- Use `concurrent: false`, not the removed `test.sequential`/`describe.sequential`. `toThrow('')` matches any message; use `/^$/` for empty. Source: migration guide
- Use `test.projects` for multiple environments; inline projects inherit root config by default. Source: https://vitest.dev/blog/vitest-5
- Reporter output goes under `.vitest/`; gitignore it. Source: migration guide

## Backward: v4 (researched 2026-10-22; for repos on Vitest 4.x, from the v4 and v5 migration guides)
These replace the matching Base rules for a repo that has not moved to v5.
- Vitest 4 needs Vite >= 6.0 and Node >= 20. The Vite >= 6.4 / Node >= 22.12 floors and the explicit `vite` peer install are v5-only. Source: https://v4.vitest.dev/guide/migration
- `clearMocks` defaults to `false`: mock call history carries from one test into the next. Set `clearMocks: true` (or `mockReset`/`restoreMocks`) in config, or call `vi.clearAllMocks()` in `beforeEach`. Still reset stubs and timers (`vi.unstubAllGlobals()`, `vi.unstubAllEnvs()`, `vi.useRealTimers()`). Source: https://vitest.dev/guide/migration (v5 section)
- `vi.mock`/`vi.unmock`/`vi.hoisted` inside a function or `describe`/`test` callback only logs a warning in v4 (v5 throws). Keep them at the top level anyway. Source: same
- `test.sequential`, `describe.sequential` and the `sequential` option still exist in v4; `concurrent: false` also works and is the forward-compatible form. Source: same
- `toThrow('')` matches only an empty message (treated as `/^$/`); in v5 it matches anything. Source: same
- Inline `test.projects` entries do NOT inherit the root config by default; set `extends: true` on each. Source: same
- Class mocks: in v4 the mock's prototype is not chained to the implementation, so `instanceof` and prototype methods can fail with `vi.fn(Dog)`. Use a factory or `vi.fn(class ...)` and verify. Mocks called with `new` construct an instance. Source: https://v4.vitest.dev/guide/migration
- `bench` is still a top-level import in v4 (the context-fixture API is v5). Source: https://vitest.dev/guide/migration
- Not verified for v4: unawaited `.resolves`/`.rejects`, config search in parent directories, the `.vitest/` reporter output directory. Keep the Base advice (await, root config, gitignore) as a safe superset.
