---
name: fastapi
kind: framework
coverage: [0]
---
# FastAPI standards

FastAPI is still on 0.x; the version unit is the 0 series. Re-research when 1.0 ships, and read "Breaking Changes" sections in release notes on every minor upgrade (minors do break).

## Base (researched at 0.143, 2026-10-16)

### Platform
- Use Pydantic v2 only. Why: `pydantic.v1` support was dropped in 0.128.0; the floor is `pydantic>=2.9`. Source: https://fastapi.tiangolo.com/release-notes/ (0.128.0, 0.135.2)
- Python 3.10+ is required (3.9 dropped in 0.129.0); free-threaded 3.14t is supported from 0.136.0. Source: same.

### Routes and dependencies
- Declare parameters and dependencies with `Annotated[...]`, and group related query, header or cookie parameters into a Pydantic model (`Annotated[Model, Query()]`). Source: https://fastapi.tiangolo.com/release-notes/ (0.115.0)
- Give path operations a Pydantic return type or `response_model`. Why: since 0.130.0, responses are serialized by Pydantic in Rust when a return type is present. Source: same (0.130.0)
- Do not use `ORJSONResponse` or `UJSONResponse`. Why: deprecated in 0.131.0, since the Pydantic serializer supersedes them. Source: same (0.131.0)
- Use `scope="request"` on `yield` dependencies that must clean up before the response is sent, such as DB sessions that must commit first. Source: same (0.121.0)
- Use the `lifespan` context manager for startup and shutdown, not `on_event`. Why: `on_event` survives only as a backwards-compatibility shim for Starlette 1.0. Source: same (0.128.3, 0.133.0)
- Routers are kept by reference (0.137.0): routes added after `include_router` are reflected, and router-level dependencies, exception handlers and middleware are supported. Source: same (0.137.0)
- Send JSON request bodies with `Content-Type: application/json`. Why: `strict_content_type` checking became the default in 0.132.0. Source: same (0.132.0)

### Serving a built frontend
- Serve a static SPA build with `app.frontend("/", directory=...)` rather than hand-mounting `StaticFiles` with a catch-all route. Path operations always take precedence. The fallback page is served only to `GET`/`HEAD` requests that accept HTML. Missing assets return 404. `check_dir="auto"` fails at startup outside development if the directory is missing. App and router dependencies apply, for example cookie auth. Source: https://fastapi.tiangolo.com/tutorial/frontend/ (added 0.138.0–0.141.0)

### Observability
- OpenTelemetry is native as of 0.142.0. Automatic exporter setup from `OTEL_*` variables is opt-in since 0.143.0, via `FastAPI(telemetry={"auto_configure": True})` or `FASTAPI_OTEL_AUTO_CONFIGURE=true`. Source: https://fastapi.tiangolo.com/release-notes/ (0.142.0, 0.143.0)
