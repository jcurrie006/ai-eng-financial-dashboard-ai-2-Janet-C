# Project Status

_Last updated: 2026-07-17._

## Current state

Working full-stack demo running on deterministic, in-memory mock data. The backend
exposes a full analytics API; the frontend renders a KPI + charts dashboard but
currently consumes only the raw metrics endpoint.

## What exists

- Backend ([../backend/app/routes.py](../backend/app/routes.py)):
  - Deterministic data generation `generate_mock_movements(seed=42)` (360 records,
    12 months, chronologically sorted).
  - Pure helpers for filtering, summarizing, ranking, net calculation, and alerts.
  - 9 endpoints: `/health`, `/api/metrics`, `/api/metrics/facets`,
    `/api/metrics/summary`, `/api/metrics/categories/top`,
    `/api/metrics/comparison`, `/api/metrics/alerts`, `/api/metrics/b2b`,
    `/api/metrics/b2c` (see [domain-and-api.md](domain-and-api.md)).
  - Pytest contract tests in
    [../backend/tests/test_routes.py](../backend/tests/test_routes.py).
- Frontend ([../frontend/src/App.tsx](../frontend/src/App.tsx)):
  - Fetches `/api/metrics`, computes KPIs and monthly chart data, renders header,
    KPI row, and two charts with loading/error/empty states.
  - Utility unit tests in
    [../frontend/src/lib/financial-utils.test.ts](../frontend/src/lib/financial-utils.test.ts).
- Tooling: Docker Compose orchestration; Vite `/api` proxy to the backend.

## Recent changes

- Added a project rule set under [../.agents/rules/](../.agents/rules/) covering
  architecture, type-safety/contracts, testing, security/config, UX/i18n, and
  data/performance.
- Created this `memory-bank/`.

## Known limitations / risks

Summary (full detail in
[../docs/context/05-risks-gaps-improvements.md](../docs/context/05-risks-gaps-improvements.md)):

- No persistence; data is regenerated in memory per request.
- CORS is fully open; not production-ready (no auth/observability).
- Frontend uses only `/api/metrics`; advanced endpoints are unused.
- Limited request validation (e.g. no `start_date <= end_date` check).
- Mixed English/Spanish UI copy; dark mode is force-enabled.
- No frontend component/integration tests; `frontend/src/lib/mock-data.ts` is
  unreferenced.

## Immediate next steps

1. Testing and validation hardening (frontend component/integration tests; stricter
   backend date-range semantics).
2. Frontend consumption of advanced analytics endpoints (via an API client layer).
3. Security and deployment readiness (scoped CORS, config via env).

See [roadmap.md](roadmap.md) for the prioritized backlog.
