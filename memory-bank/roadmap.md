# Roadmap

Prioritized backlog distilled from
[../docs/context/05-risks-gaps-improvements.md](../docs/context/05-risks-gaps-improvements.md).
Ordering reflects the documented priority: (1) testing/validation, (2) frontend use
of advanced endpoints, (3) security/deployment, (4) persistence.

## Short-term

- Add frontend integration tests for fetch-success and fetch-failure states.
- Add component tests for KPI cards and charts (loading, empty, and error paths).
- Add stricter backend request validation for date-range semantics
  (e.g. `start_date <= end_date` in `/api/metrics/comparison`).
- Remove dead code (`frontend/src/lib/mock-data.ts`).

## Medium-term

- Introduce a frontend API client layer and consume advanced endpoints:
  `/api/metrics/summary`, `/api/metrics/comparison`, `/api/metrics/alerts`,
  `/api/metrics/categories/top`.
- Replace forced dark mode with user/system theme preference handling.
- Align UI copy language strategy (all English, all Spanish, or full i18n).
- Centralize backend filtering (including `business_type`) into one shared helper;
  generate the dataset once instead of per request.

## Long-term

- Introduce persistent data storage and an ingestion pipeline (replace in-memory
  generation behind a single data accessor).
- Add production CORS policy, auth strategy, and observability.
- Add contract tests to protect backend-to-frontend response compatibility.

## Priority summary

1. Testing and validation hardening
2. Frontend usage of advanced analytics endpoints
3. Security and deployment readiness
4. Data persistence architecture

## Related conventions

Implementation of these items should follow the rules in
[../.agents/rules/](../.agents/rules/).
