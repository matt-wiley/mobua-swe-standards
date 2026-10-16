---
name: typescript
kind: language
coverage: [6]
---
# TypeScript standards

## Base (researched at 6.0, 2026-10-16)

- TS 6.0 defaults to `strict: true`, `module: esnext`, `target` set to the latest ES (es2025), `types: []`, `rootDir: "."` and `noUncheckedSideEffectImports: true`. Don't restate these defaults in tsconfig. Do list global types explicitly, e.g. `"types": ["node"]`. Source: https://devblogs.microsoft.com/typescript/announcing-typescript-6-0/
- In Vite-bundled apps, use `"module": "preserve"` with `"moduleResolution": "bundler"`. Source: same.
- Don't use options deprecated in 6.0 and removed in 7.0: `baseUrl`, `moduleResolution: node`/`node10`/`classic`, `target: es5`, `downlevelIteration`, `module: amd`/`umd`/`systemjs`/`none`, `esModuleInterop: false`, `allowSyntheticDefaultImports: false`, `alwaysStrict: false`, the `module` keyword for namespaces, and `asserts` on imports (use `with`). Never set `ignoreDeprecations`. Why: this keeps the code ready for 7.0. Source: same.
- Use `paths` or subpath imports (`#...` in `package.json` `imports`) instead of `baseUrl` for aliases. Source: same.
- `lib: ["dom"]` now covers DOM iterables; don't add `dom.iterable`. Source: same.
- Don't pass file paths to `tsc` when a `tsconfig.json` is present; it's an error in 6.0. Source: same.

## Note on 7.0 (not adopted)
TypeScript 7.0 (the Go-native compiler, released 2026-07-08) has no programmatic API until 7.1. Tools that use the API, including typescript-eslint, svelte-check and the Svelte, Vue and Astro language tools, must stay on 6.0. Add a v7 delta once the toolchain supports it. Source: https://devblogs.microsoft.com/typescript/announcing-typescript-7-0/
