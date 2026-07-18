# Architecture

## Standard

Keep pure business logic separate from I/O (HTTP routes, data fetching) and from
presentation (React components). Data access must flow through a single, shared
layer rather than being duplicated per endpoint or per component.

## Preserve: separation of concerns

The codebase already isolates calculations from I/O. Keep it this way.

- Backend analytics helpers are pure and route-free: `filter_movements`,
  `filter_movements_by_date`, `summarize_movements`, `build_top_categories`,
  `calculate_net_value`, and `detect_outcome_alerts` in
  [backend/app/routes.py](../../backend/app/routes.py) take data in and return data
  out with no framework coupling.
- Frontend calculations live in
  [frontend/src/lib/financial-utils.ts](../../frontend/src/lib/financial-utils.ts)
  (`computeKPIs`, `computeMonthlyData`, `formatCurrency`, `formatPercent`), not
  inside components.

Rules:

- New business calculations MUST be pure functions in a `lib`/helper module, unit
  testable without a server or a DOM.
- Route handlers and React components SHOULD orchestrate (fetch, wire props,
  render) and delegate computation to helpers.

## Address: duplicated filtering (DRY)

The same filtering block is copy-pasted across endpoints, e.g. the
`if business_type is not None: movements = [...]` snippet appears in
`get_metrics_summary`, `get_top_categories`, `get_metrics_comparison`, and
`get_metrics_alerts` in [backend/app/routes.py](../../backend/app/routes.py).

Rules:

- Extend `filter_movements` (or add a single `apply_filters` helper) to accept all
  dimensions including `business_type`, and call that one function from every
  endpoint. Do not re-implement per-field filtering inline in a handler.
- When adding a new filter dimension, add it to the shared filter helper only.

## Address: missing frontend API client layer

The frontend calls `fetch` inline inside the component. See `fetchFinancialData`
and the raw `fetch(\`${API_BASE_URL}/api/metrics\`)` in
[frontend/src/App.tsx](../../frontend/src/App.tsx). Only `/api/metrics` is consumed
even though richer endpoints exist.

Rules:

- Introduce a dedicated API client module (e.g. `frontend/src/lib/api-client.ts`)
  that owns the base URL, request construction, response `ok` checks, and JSON
  typing. Components MUST call the client, not `fetch` directly.
- Each backend endpoint the UI uses SHOULD have a corresponding typed client
  function returning the shared contract types (see
  [type-safety-contracts.md](type-safety-contracts.md)).
