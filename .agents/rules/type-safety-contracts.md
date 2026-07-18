# Type Safety and Contracts

## Standard

The backend response schema is the single source of truth for the data contract.
Frontend TypeScript types MUST stay in sync with backend Pydantic models, and both
MUST use closed unions for domain enums rather than loose strings.

## Preserve: typed domain models mirrored across the stack

The project already models the domain with precise types on both sides.

- Backend defines closed unions and Pydantic models in
  [backend/app/routes.py](../../backend/app/routes.py):
  `OperationType = Literal["income", "outcome"]`,
  `Category = Literal["suppliers", "sales", "operational", "administrative", "others"]`,
  `BusinessType = Literal["B2B", "B2C"]`, and models such as `FinancialMovement`,
  `MetricsSummaryItem`, `MetricsComparison`, `MetricsAlert`.
- Frontend mirrors these in
  [frontend/src/lib/financial-types.ts](../../frontend/src/lib/financial-types.ts)
  (`FinancialMovement`, `KPIMetrics`, `MonthlyDataPoint`).
- Endpoints declare `response_model=...`, so FastAPI validates and documents the
  contract.

Rules:

- Domain enums MUST be `Literal` unions in Python and string-literal union types in
  TypeScript. Do not widen these to bare `str` / `string`.
- Every endpoint MUST declare an explicit `response_model`.

## Address: keeping the two sides in sync

Contracts are currently kept aligned by hand, which drifts silently over time.

Rules:

- Any change to a backend model field (name, type, nullability) or a `Literal`
  union MUST be accompanied by the matching change to the frontend type in
  [frontend/src/lib/financial-types.ts](../../frontend/src/lib/financial-types.ts).
  Nullable backend fields (e.g. `delta_pct: float | None`) MUST map to
  `number | null` in TypeScript.
- When the frontend begins consuming an endpoint, add the corresponding TS type for
  its response and use it in the API client (see
  [architecture.md](architecture.md)).
- Prefer field names that match exactly across the boundary (the backend uses
  snake_case such as `create_date`, `operation_type`; keep the frontend types using
  the same wire names to avoid ad-hoc remapping).

## Future

- Add contract tests that assert the serialized backend payload shape matches the
  frontend's expected type (see [testing.md](testing.md)) so drift fails CI rather
  than surfacing at runtime.
