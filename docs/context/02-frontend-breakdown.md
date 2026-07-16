# Frontend Breakdown

## Stack and Tooling

- React 19 + TypeScript
- Vite 8
- Tailwind CSS v4
- Recharts
- Vitest
- ESLint
- Utility helpers: `clsx`, `tailwind-merge`

## Boot Process and Data Lifecycle

The app initializes in `frontend/src/main.tsx` and mounts `App` in React Strict Mode.

In `frontend/src/App.tsx`:

1. On mount, it requests `/api/metrics` (prefixed by optional `VITE_API_BASE_URL`).
2. Success path:
   - computes KPI aggregates via `computeKPIs`
   - computes monthly chart points via `computeMonthlyData`
3. Failure path:
   - displays an error banner (`No se pudo cargar la informacion financiera...`)
4. Loading path:
   - renders skeleton placeholders for KPI cards and charts.

## Component Architecture

Dashboard components:

- `DashboardHeader`
  - Title/subtitle plus period badge.
- `KPIRow`
  - Composes 4 KPI cards: income, outcome, profit, margin.
- `KPICard`
  - Variant-based styles and skeleton state.
- `IncomeOutcomeChart`
  - Recharts line chart with custom tooltip and empty state.
- `ProfitPercentChart`
  - Recharts margin line chart with zero reference line and empty state.

UI primitives:

- `components/ui/card.tsx`
- `components/ui/skeleton.tsx`

## Data Contracts and Utilities

Primary types (`frontend/src/lib/financial-types.ts`):

- `FinancialMovement`
- `KPIMetrics`
- `MonthlyDataPoint`

Utility functions (`frontend/src/lib/financial-utils.ts`):

- `computeKPIs`
- `computeMonthlyData`
- `formatCurrency`
- `formatPercent`

These utility functions keep business calculations separate from presentational components.

## Styling System

Defined in `frontend/src/index.css`:

- Theme tokens for light/dark palettes, chart colors, KPI badge colors, and radius scale.
- Tailwind theme mapping through `@theme inline` variables.
- Current app layout enforces dark mode (`className="dark"` on root `main`).

## Testing Coverage

Frontend tests (Vitest) currently cover utility logic only:

- KPI totals and margin calculations
- Zero-income profit margin edge case
- Chronological monthly aggregation over unsorted/cross-year input
- Currency/percent formatting

Current gap: no component tests or browser-level integration tests.

## TypeScript Snippet

```ts
export interface KPIMetrics {
  totalIncome: number;
  totalOutcome: number;
  profit: number;
  profitPercent: number;
}

export interface MonthlyDataPoint {
  month: string;
  income: number;
  outcome: number;
  profitPercent: number;
}
```
