# Backend Breakdown

## Framework and Setup

- FastAPI app initialized in `backend/app/main.py`
- Router imported from `backend/app/routes.py`
- CORS is fully open for development convenience (`*` origins/methods/headers)

## Domain Models

`backend/app/routes.py` defines typed domain contracts with Pydantic and `Literal` values:

- `FinancialMovement`
- `MetricsFacets`
- `MetricsSummaryItem`
- `TopCategoryItem`
- `MetricsComparison`
- `MetricsAlert`

Core domain constraints:

- `operation_type`: `income | outcome`
- `category`: `suppliers | sales | operational | administrative | others`
- `business_type`: `B2B | B2C`

## Data Strategy

Data is generated in memory by `generate_mock_movements(seed=42)`:

- 12 months
- 30 movements per month
- 360 records total
- Chronological ordering before return

This keeps API responses deterministic and test-friendly.

## Processing and Analytics Functions

Core processing helpers include:

- Date filtering: `filter_movements_by_date`
- Multi-criteria filtering: `filter_movements`
- Sorting: `ensure_chronological_order`
- Facets extraction: `build_metrics_facets`
- Group aggregation: `summarize_movements` (`day | week | month`)
- Ranking: `build_top_categories`
- Net calculation: `calculate_net_value`
- Alerting: `detect_outcome_alerts`

## API Endpoint Breakdown

- `GET /health`
  - status check
- `GET /api/metrics`
  - raw movement list with optional filtering
- `GET /api/metrics/facets`
  - available dimensions and date bounds
- `GET /api/metrics/summary`
  - grouped timeline summary
- `GET /api/metrics/categories/top`
  - top categories by operation type
- `GET /api/metrics/comparison`
  - current vs previous period comparison
- `GET /api/metrics/alerts`
  - outcome spike alerts vs rolling baseline
- `GET /api/metrics/b2b`
  - B2B-only movement feed
- `GET /api/metrics/b2c`
  - B2C-only movement feed

## Testing Coverage

Backend tests validate:

- generation count/order
- date filter boundaries
- health and primary route responses
- category/operation/business filters
- facets, summaries, top categories
- comparison and alerts payload shape

Coverage is strong for main behavior and route contracts, with room to improve for edge-case combinations.

## TypeScript Contract Snippet

```ts
export interface FinancialMovement {
  create_date: string;
  amount: number;
  operation_type: "income" | "outcome";
  category: "suppliers" | "sales" | "operational" | "administrative" | "others";
  business_type: "B2B" | "B2C";
}

export interface MetricsComparison {
  current_period: number;
  previous_period: number;
  delta_abs: number;
  delta_pct: number | null;
}
```
