# Domain Model and API Reference

Source of truth: [../backend/app/routes.py](../backend/app/routes.py). Frontend
mirror types: [../frontend/src/lib/financial-types.ts](../frontend/src/lib/financial-types.ts).

## Domain enums

- `operation_type`: `income | outcome`
- `category`: `suppliers | sales | operational | administrative | others`
- `business_type`: `B2B | B2C`
- `group_by` (summary/alerts): `day | week | month`

Outcome-only categories for generated data: `suppliers`, `operational`,
`administrative`, `others`. Income is mostly `sales` (else `others`).

## Core record: `FinancialMovement`

- `create_date: date`
- `amount: float`
- `operation_type: income | outcome`
- `category: <category>`
- `business_type: B2B | B2C`

Data is generated deterministically via `generate_mock_movements(seed=42)`: 12
months x 30 movements = 360 records, sorted chronologically.

## KPI and metric definitions

- `totalIncome` = sum of `amount` where `operation_type == income`.
- `totalOutcome` = sum of `amount` where `operation_type == outcome`.
- `profit` = `totalIncome - totalOutcome`.
- `profitPercent` = `profit / totalIncome * 100` (0 when income is 0).
- `net` (summary) = `income - outcome` per period bucket.
- Comparison `delta_abs` = `current_net - previous_net`; `delta_pct` =
  `delta_abs / abs(previous_net) * 100` (null when previous is 0).
- Alert `increase_ratio` = `(outcome - baseline) / baseline`, where baseline is the
  running average of prior periods; emitted when `increase_ratio > threshold`.

## Endpoint catalog

| Method | Path | Query params | Response model |
| --- | --- | --- | --- |
| GET | `/health` | - | `{ "status": "ok" }` |
| GET | `/api/metrics` | `start_date?`, `end_date?`, `category?`, `operation_type?` | `list[FinancialMovement]` |
| GET | `/api/metrics/facets` | - | `MetricsFacets` |
| GET | `/api/metrics/summary` | `group_by=month`, `start_date?`, `end_date?`, `category?`, `operation_type?`, `business_type?` | `list[MetricsSummaryItem]` |
| GET | `/api/metrics/categories/top` | `operation_type=outcome`, `limit=5 (1..20)`, `start_date?`, `end_date?`, `business_type?` | `list[TopCategoryItem]` |
| GET | `/api/metrics/comparison` | `start_date` (required), `end_date` (required), `business_type?` | `MetricsComparison` |
| GET | `/api/metrics/alerts` | `threshold=0.3 (>=0)`, `group_by=month`, `start_date?`, `end_date?`, `business_type?` | `list[MetricsAlert]` |
| GET | `/api/metrics/b2b` | `start_date?`, `end_date?`, `category?`, `operation_type?` | `list[FinancialMovement]` |
| GET | `/api/metrics/b2c` | `start_date?`, `end_date?`, `category?`, `operation_type?` | `list[FinancialMovement]` |

## Response models

- `MetricsFacets`: `operation_types`, `business_types`, `categories`, `min_date`,
  `max_date`.
- `MetricsSummaryItem`: `period: str`, `income`, `outcome`, `net`.
- `TopCategoryItem`: `category`, `operation_type`, `total_amount`.
- `MetricsComparison`: `current_period`, `previous_period`, `delta_abs`,
  `delta_pct: float | null`.
- `MetricsAlert`: `period`, `outcome_total`, `baseline_average`, `increase_ratio`.

## Contract notes

- Wire field names are snake_case on both sides (e.g. `create_date`,
  `operation_type`); keep frontend types aligned. See
  [../.agents/rules/type-safety-contracts.md](../.agents/rules/type-safety-contracts.md).
- Every endpoint declares an explicit `response_model`.
