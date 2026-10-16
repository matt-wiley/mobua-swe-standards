---
name: python
kind: language
coverage: [3]
---
# Python standards

## Base (researched at 3.14, 2026-10-16)

### Annotations and typing
- Write forward references unquoted and do not add `from __future__ import annotations` to new code. Why: 3.14 defers annotation evaluation (PEP 649/749); the future import is now deprecated and slated for removal after 3.13's EOL (2029). Source: https://docs.python.org/3.14/whatsnew/3.14.html (Porting: `from __future__ import annotations`)
- Inspect annotations at runtime with `annotationlib.get_annotations()`, not `__annotations__`. Why: deferred annotations may reference not-yet-defined names; `annotationlib` offers `VALUE`/`FORWARDREF`/`STRING` formats. Source: https://docs.python.org/3.14/whatsnew/3.14.html (PEP 649)

### Syntax
- Use t-strings (`t"..."`, PEP 750) wherever static and interpolated parts must stay separate (SQL, shell, HTML); never pass a `Template` where a `str` is expected. Why: a t-string yields `string.templatelib.Template`, not `str`, so sanitizing consumers can treat interpolations differently. Source: https://docs.python.org/3.14/whatsnew/3.14.html (PEP 750)
- Never `return`/`break`/`continue` out of a `finally` block; run tests with warnings as errors so the `SyntaxWarning` fails CI. Why: PEP 765 makes it a warning because it silently swallows exceptions. Source: https://docs.python.org/3.14/whatsnew/3.14.html (PEP 765)
- Pick one style for multi-exception `except` clauses per repo. Unparenthesized `except A, B:` (PEP 758) is valid only on 3.14+ and only without `as`. Source: https://docs.python.org/3.14/whatsnew/3.14.html (PEP 758)

### Concurrency
- Do not rely on the GIL for atomicity; guard shared mutable state with locks. Why: the free-threaded build is officially supported (PEP 779) and libraries increasingly ship `3.14t` wheels. Source: https://docs.python.org/3.14/whatsnew/3.14.html (PEP 779)
- Choose the asyncio event loop with `asyncio.run(..., loop_factory=...)` or `asyncio.Runner`; do not use event-loop policies. Why: the policy system is deprecated, removal in 3.16. Source: https://docs.python.org/3.14/whatsnew/3.14.html (Deprecated: asyncio)
- Use `inspect.iscoroutinefunction`, not `asyncio.iscoroutinefunction`. Why: deprecated, removal in 3.16. Source: same.
- Pass picklable arguments explicitly to `multiprocessing`/`concurrent.futures` workers; request `get_context("fork")` only deliberately. Why: Linux now defaults to `forkserver` (macOS/Windows already `spawn`), breaking code that relied on inherited globals. Source: https://docs.python.org/3.14/whatsnew/3.14.html (multiprocessing)

### Standard library
- Use `uuid.uuid7()` for new time-ordered identifiers. Why: RFC 9562 v7 UUIDs sort by creation time, which suits database keys. Source: https://docs.python.org/3.14/whatsnew/3.14.html (uuid)
- Prefer `Path.copy()`, `copy_into()`, `move()`, `move_into()` over `shutil` for path-based file operations. Source: https://docs.python.org/3.14/whatsnew/3.14.html (pathlib)
- Use `open()`, not the deprecated `codecs.open()`. Source: https://docs.python.org/3.14/whatsnew/3.14.html (Deprecated: codecs)
- Do not depend on garbage-collector timing. Why: 3.14.0–3.14.4 shipped an incremental GC that 3.14.5 reverted. Source: https://docs.python.org/3.14/whatsnew/3.14.html (gc)
