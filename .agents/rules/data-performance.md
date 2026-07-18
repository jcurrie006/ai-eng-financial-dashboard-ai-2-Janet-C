# Data and Performance

## Standard

Data generation/access MUST be efficient and centralized. Preserve determinism, but
avoid recomputing the full dataset on every request, and route all reads through one
shared source so a future persistent store can be swapped in cleanly.

## Preserve: determinism

`generate_mock_movements(seed=42)` in
[backend/app/routes.py](../../backend/app/routes.py) produces stable, repeatable
data (360 records, chronologically sorted). This underpins reliable tests and demos
and MUST be preserved.

Rules:

- Keep data generation deterministic (seeded) so tests and demos stay stable.
- Keep the chronological-ordering guarantee (`ensure_chronological_order`) for any
  data source.

## Address: regeneration per request

Every endpoint calls `generate_mock_movements(seed=42)` on each request - e.g.
`get_metrics`, `get_metrics_facets`, `get_metrics_summary`, `get_top_categories`,
`get_metrics_comparison`, `get_metrics_alerts`, `get_b2b_metrics`,
`get_b2c_metrics` in [backend/app/routes.py](../../backend/app/routes.py). The full
360-record set is rebuilt every time even though it is identical.

Rules:

- Generate the dataset once and reuse it (module-level constant or an
  `@lru_cache`/cached accessor), rather than regenerating inside each handler.
- Provide a single accessor function (e.g. `get_movements()`) that all endpoints
  call; endpoints MUST NOT call the raw generator directly.

## Address: centralize filtering

Reads should go through one filtering path. See also
[architecture.md](architecture.md) - the `business_type` filter is duplicated inline
across endpoints.

Rules:

- All endpoints MUST filter through the shared filter helper (extended to cover
  `business_type`), not with inline per-endpoint list comprehensions.
- The B2B/B2C endpoints SHOULD be expressed as the shared filter with a
  `business_type` argument rather than bespoke comprehensions.

## Path toward persistence

The system is entirely in-memory today (a documented risk in
[docs/context/05-risks-gaps-improvements.md](../../docs/context/05-risks-gaps-improvements.md)).

Rules:

- Keep data access behind the single accessor/repository function so an in-memory
  source can later be replaced by a database or ingestion pipeline without changing
  endpoint code.
- Any future persistence layer MUST preserve the same contract types and ordering
  guarantees described in [type-safety-contracts.md](type-safety-contracts.md).
