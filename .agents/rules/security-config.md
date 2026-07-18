# Security and Configuration

## Standard

Security-relevant configuration MUST come from the environment, not hardcoded
values, and MUST be safe by default for non-local environments. Request parameters
MUST be validated for semantic correctness, not just type.

## Address: CORS configuration

[backend/app/main.py](../../backend/app/main.py) currently sets:

```python
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```

Wildcard `allow_origins=["*"]` together with `allow_credentials=True` is unsafe and
not honored by browsers for credentialed requests. It is also hardcoded with no way
to tighten it for production.

Rules:

- Never combine `allow_origins=["*"]` with `allow_credentials=True`.
- Allowed origins MUST be read from configuration/environment (e.g. an
  `ALLOWED_ORIGINS` variable), defaulting to an explicit local dev origin list
  rather than `*`.
- Only set `allow_credentials=True` when there is a concrete need; otherwise leave
  it off. Prefer explicit method/header lists over `*` for anything beyond local
  dev.

## Address: request-semantics validation

`Query` constraints already validate individual params well (e.g.
`limit=Query(default=5, ge=1, le=20)`, `threshold=Query(default=0.3, ge=0)` in
[backend/app/routes.py](../../backend/app/routes.py)), but cross-field semantics are
not checked. For example, `get_metrics_comparison` accepts `start_date` and
`end_date` without verifying `start_date <= end_date`.

Rules:

- Preserve the existing per-parameter `Query` bounds; keep validating new numeric
  params with `ge`/`le`.
- Endpoints that accept a date range MUST validate `start_date <= end_date` and
  return a 4xx (e.g. `HTTPException(status_code=422/400)`) on violation, rather than
  silently producing empty or nonsensical results.

## General configuration

Rules:

- Do not hardcode environment-specific values (origins, hosts, debug ports,
  secrets). Read them from environment variables with safe defaults.
- Keep debug-only tooling (e.g. `debugpy`, `--reload`) confined to the dev
  container configuration, not production startup paths.
