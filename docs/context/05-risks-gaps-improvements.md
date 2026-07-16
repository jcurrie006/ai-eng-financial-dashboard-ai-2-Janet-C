# Risks, Gaps, and Improvement Opportunities

## Current Risks

- Data persistence risk:
  - All metrics are generated in memory; no database or external source integration.
- Security/configuration risk:
  - CORS is fully open; acceptable in local dev, risky for production deployment.
- Product parity risk:
  - Frontend currently consumes only `/api/metrics` while richer backend endpoints remain unused.
- Validation risk:
  - Limited handling of invalid date ranges or unusual parameter combinations.
- UX consistency risk:
  - Root component forces dark mode despite dual-theme token support.
- Localization consistency risk:
  - UI mixes English labels with Spanish error messaging.

## Testing Gaps

- No frontend component tests for KPI cards, charts, loading, and error states.
- No e2e flow covering frontend-backend integration.
- Backend edge-case depth can improve (complex query combinations and numeric boundary checks).

## Improvement Opportunities

### Short-Term

- Add frontend integration tests for fetch-success/fetch-failure states.
- Add component tests around chart empty/loading paths.
- Add stricter backend request validation for date-range semantics.

### Medium-Term

- Implement API client layer in frontend to consume:
  - `/api/metrics/summary`
  - `/api/metrics/comparison`
  - `/api/metrics/alerts`
  - `/api/metrics/categories/top`
- Replace hardcoded dark-mode enforcement with user/system preference handling.
- Align UI copy language strategy (all English, all Spanish, or i18n support).

### Long-Term

- Introduce persistent data storage and ingestion pipeline.
- Add production CORS policies, auth strategy, and observability.
- Add contract tests to protect backend-to-frontend response compatibility.

## Priority Suggestion

1. Testing and validation hardening
2. Frontend usage of advanced analytics endpoints
3. Security and deployment readiness
4. Data persistence architecture

## TypeScript Roadmap Snippet

```ts
export interface ImprovementItem {
  id: string;
  area: "frontend" | "backend" | "platform";
  priority: 1 | 2 | 3 | 4;
  effort: "small" | "medium" | "large";
  summary: string;
}
```
