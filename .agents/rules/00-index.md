# Financial Dashboard Rule Set

This directory codifies the conventions for the financial dashboard project. The
rules were derived from a review of the current codebase (backend FastAPI service
and React + TypeScript frontend) and the risks documented in
[docs/context/05-risks-gaps-improvements.md](../../docs/context/05-risks-gaps-improvements.md).

Each rule file states the standard, the good pattern to preserve, and the risk it
prevents, with concrete references to the code that motivated it.

## How to use these rules

Before analyzing code, modifying files, or generating outputs, review the relevant
rule file(s) below and keep future changes compliant. Rules are advisory guidance
for future iterations; they do not, by themselves, change runtime behavior.

## Review summary

The review identified five good practices to preserve and five risky practices to
address, grouped by category.

### Good practices (preserve)

| Category | Practice | Evidence |
| --- | --- | --- |
| Architecture | Pure business logic isolated from I/O and presentation | `backend/app/routes.py` helpers (`filter_movements`, `summarize_movements`, `calculate_net_value`, `detect_outcome_alerts`); `frontend/src/lib/financial-utils.ts` (`computeKPIs`, `computeMonthlyData`) |
| Type safety / contracts | Pydantic `Literal` domain models mirrored by TS types | `OperationType`/`Category`/`BusinessType` in `backend/app/routes.py` mirrored in `frontend/src/lib/financial-types.ts` |
| Testing / determinism | Seeded, deterministic mock data enables repeatable contract tests | `generate_mock_movements(seed=42)`; `backend/tests/test_routes.py` |
| Validation at the boundary | FastAPI `Query` constraints enforce safe inputs | `limit=Query(default=5, ge=1, le=20)`, `threshold=Query(default=0.3, ge=0)` in `backend/app/routes.py` |
| UX resilience / a11y | Explicit loading/error/empty states and semantic `aria-label`s | `frontend/src/App.tsx`; `hasData` empty state in `frontend/src/components/dashboard/income-outcome-chart.tsx` |

### Risky practices (address)

| Category | Practice | Evidence |
| --- | --- | --- |
| Security / config | Wildcard CORS combined with credentials; hardcoded, no env config | `allow_origins=["*"]` + `allow_credentials=True` in `backend/app/main.py` |
| Consistency / localization | Mixed English/Spanish user-facing copy; hardcoded strings | Spanish error string in `frontend/src/App.tsx` amid English UI |
| UX / theming | Forced dark mode; hardcoded period contradicting derived data years | `className="dark"` and `period="2024 - Full Year"` in `frontend/src/App.tsx` vs `date.today()` in `backend/app/routes.py` |
| Data / performance & DRY | Full dataset regenerated per request; duplicated filter blocks | `generate_mock_movements(seed=42)` per endpoint; repeated `if business_type is not None` in `backend/app/routes.py` |
| Testing gaps / dead code / parity | No FE component/integration tests; dead module; inline fetch; unused endpoints; missing date-range validation | `frontend/src/lib/mock-data.ts` (unreferenced); inline `fetch` in `frontend/src/App.tsx`; `get_metrics_comparison` lacks `start_date <= end_date` check |

## Rule files

- [architecture.md](architecture.md) - separation of concerns, shared filter/aggregation layer, frontend API client layer.
- [type-safety-contracts.md](type-safety-contracts.md) - keep backend and frontend domain contracts in sync.
- [testing.md](testing.md) - preserve deterministic contract tests, require frontend component/integration tests, ban dead code.
- [security-config.md](security-config.md) - CORS/config hardening and request-semantics validation.
- [ux-consistency-i18n.md](ux-consistency-i18n.md) - single language strategy, theme preference, no contradicting hardcoded labels.
- [data-performance.md](data-performance.md) - avoid per-request regeneration, centralize filtering, path toward persistence.
