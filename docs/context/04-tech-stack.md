# Tech Stack

## Frontend

- Language: TypeScript
- Framework: React 19
- Build/dev server: Vite 8
- Styling: Tailwind CSS v4 + CSS custom properties
- Charting: Recharts
- Icons: Lucide React
- Testing: Vitest
- Linting: ESLint

## Backend

- Language: Python 3.13
- Framework: FastAPI
- ASGI server: Uvicorn
- Data validation: Pydantic
- Debugging: debugpy
- Testing: pytest, pytest-cov, httpx

## Dev Environment and Orchestration

- Containerization: Docker
- Multi-service orchestration: Docker Compose
- Frontend container base image: `node:24-alpine`
- Backend container base image: `python:3.13-slim`

## Runtime Integration

- Frontend Vite dev proxy forwards `/api` to backend service.
- Backend exposes REST endpoints consumed by frontend fetch calls.

## TypeScript Documentation Contracts

```ts
export type OperationType = "income" | "outcome";
export type BusinessType = "B2B" | "B2C";

export interface MetricsAlert {
  period: string;
  outcome_total: number;
  baseline_average: number;
  increase_ratio: number;
}
```
