---
name: sqlalchemy
kind: framework
coverage: [2]
---
# SQLAlchemy standards

## Base (researched at 2.1, 2026-10-16)

The 2.0 typed ORM style (`DeclarativeBase`, `Mapped[...]`, `mapped_column()`, `select()` with `Session.execute()`) is assumed as the baseline. These rules record what 2.1 changes on top of it.

- SQLAlchemy 2.1 requires Python 3.11 or later; 3.10 was dropped in August 2026. Source: https://github.com/sqlalchemy/sqlalchemy/blob/main/doc/build/changelog/migration_21.rst ("Python version compatibility starts at version 3.11")
- The asyncio extension needs `sqlalchemy[asyncio]`, because greenlet is no longer installed by default. Source: same ("Asyncio greenlet dependency no longer installs by default")
- Autoflush now runs before every execution, including `text()` and Core statements. Use `with session.no_autoflush:` where a flush must not happen yet. Source: same ("Session autoflush behavior simplified to be unconditional")
- For raw SQL on Python 3.14+, prefer `tstring(t"...")` over `text()` with manual binds. Interpolated Python values become bound parameters and SQLAlchemy expressions are embedded. Never build SQL with f-strings. Source: same ("Template String (t-string) Support")
- Use `unmapped_column()` instead of the deprecated `exclude_properties`. Source: same.
- Mapped dataclass defaults are now served by descriptors rather than set in `__dict__`, so code must not inspect `obj.__dict__` for defaults. Source: same ("ORM Mapped Dataclasses no longer populate implicit default")
- `filter_by()` now searches every FROM entity and raises an error on ambiguous names. Use explicit `where()` in multi-entity queries. Source: same (``filter_by()`` section)
- `Row` types use PEP 646 (`Select[int, str]` rather than `Select[Tuple[int, str]]`). Update type annotations accordingly. Source: same (``Row`` section)
- The PostgreSQL URL `postgresql://` now defaults to psycopg 3. Depend on `psycopg`, not `psycopg2`. Source: same ("Default PostgreSQL driver changed to psycopg")
- SQLite has a `JSONB` type (SQLite 3.45 or later). When code must stay portable to Postgres, keep the generic `JSON` type unless the binary format is needed. Source: same ("Added JSONB JSON format for SQLite")
- Alembic (1.20 at research time) is the migration tool. No version-specific rules have been researched yet.
