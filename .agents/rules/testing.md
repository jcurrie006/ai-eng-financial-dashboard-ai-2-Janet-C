# Testing

## Standard

Business logic and API contracts MUST be covered by fast, deterministic tests. The
frontend MUST test behavior (states and rendering), not just utility math. The
codebase MUST NOT carry unreferenced/dead modules.

## Preserve: determinism and backend contract tests

- Mock data is deterministic via `generate_mock_movements(seed=42)` in
  [backend/app/routes.py](../../backend/app/routes.py), which makes assertions
  stable and demos repeatable.
- [backend/tests/test_routes.py](../../backend/tests/test_routes.py) covers status
  codes, exact payload key sets (e.g. `{"period", "income", "outcome", "net"}`),
  filter correctness, and date-boundary edges.

Rules:

- Tests MUST rely on the seeded generator (or an equally deterministic fixture);
  never depend on real wall-clock randomness for assertions.
- Every endpoint MUST have at least one test asserting status code and response
  shape (key set), plus one per meaningful filter/branch.

## Address: frontend test gaps

Frontend tests currently cover only utility logic
([frontend/src/lib/financial-utils.test.ts](../../frontend/src/lib/financial-utils.test.ts)).
There are no component or integration tests, despite the UI having distinct
loading, error, empty, and success states in
[frontend/src/App.tsx](../../frontend/src/App.tsx) and
[frontend/src/components/dashboard/income-outcome-chart.tsx](../../frontend/src/components/dashboard/income-outcome-chart.tsx).

Rules:

- Components with conditional states MUST have component tests covering each state:
  loading (skeletons), error banner, empty (`hasData === false`), and success.
- Add at least one integration test for the fetch-success and fetch-failure paths
  (mock the API client / network), asserting the rendered outcome.
- Preserve the existing edge-case coverage style (e.g. zero-income margin,
  cross-year aggregation) when adding new utilities.

## Address: dead code

[frontend/src/lib/mock-data.ts](../../frontend/src/lib/mock-data.ts) is not
referenced anywhere in the app.

Rules:

- Do not keep unreferenced modules. Either wire a module into the app/tests or
  remove it. New sample/fixture data MUST live under test files or a clearly named
  fixtures location, not in shipped `src/lib`.
