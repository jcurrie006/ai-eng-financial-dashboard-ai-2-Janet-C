# UX Consistency and Localization

## Standard

User-facing copy MUST follow a single, consistent language strategy. Theming MUST
respect user/system preference. Displayed values MUST reflect the actual data, not
hardcoded assumptions.

## Preserve: resilient, accessible states

Keep the existing pattern of explicitly handling loading, error, and empty states
and labeling regions for assistive tech.

- [frontend/src/App.tsx](../../frontend/src/App.tsx) renders skeletons while
  loading, an error banner on failure, and uses `aria-label`ed `section`s.
- [frontend/src/components/dashboard/income-outcome-chart.tsx](../../frontend/src/components/dashboard/income-outcome-chart.tsx)
  guards with `hasData` and shows a clear empty state.

Rules:

- Every data-driven view MUST handle loading, error, empty, and success states.
- Interactive/landmark regions MUST retain semantic markup and `aria-label`s.

## Address: mixed languages

The UI mixes English labels with a Spanish error message. In
[frontend/src/App.tsx](../../frontend/src/App.tsx) the error string is
`"No se pudo cargar la informacion financiera. Revisa la API de backend."` while
the rest of the UI (titles, chart labels) is English.

Rules:

- Choose one language strategy for the project and apply it consistently. Until
  full i18n exists, all user-facing copy MUST be in the same language (default:
  English) - no mixed English/Spanish strings.
- Do not scatter hardcoded user-facing strings across components; centralize copy
  (a constants/strings module) so a future i18n layer can replace it in one place.

## Address: forced theme

The root element forces dark mode via `className="dark"` in
[frontend/src/App.tsx](../../frontend/src/App.tsx), even though light/dark tokens
both exist in `frontend/src/index.css`.

Rules:

- Do not hardcode a single theme when dual-theme tokens exist. Respect the user's
  system preference (`prefers-color-scheme`) and/or an explicit user toggle, and
  persist the choice.

## Address: hardcoded values that contradict data

The dashboard header is hardcoded to `period="2024 - Full Year"` in
[frontend/src/App.tsx](../../frontend/src/App.tsx), while the backend derives years
from `date.today()` in [backend/app/routes.py](../../backend/app/routes.py), so the
label can misrepresent the data.

Rules:

- Displayed period/labels MUST be derived from the data being shown (e.g. from the
  fetched movements or the `/api/metrics/facets` date range), not hardcoded.
