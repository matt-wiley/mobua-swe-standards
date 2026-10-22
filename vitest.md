---
name: vitest
kind: framework
coverage: [5]
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
