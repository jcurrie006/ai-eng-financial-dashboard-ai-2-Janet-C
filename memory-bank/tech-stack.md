# Tech Stack

## Frontend

- Language: TypeScript
- Framework: React 19
- Build/dev server: Vite 8
- Styling: Tailwind CSS v4 + CSS custom properties (light/dark tokens)
- Charting: Recharts
- Icons: Lucide React
- Utilities: `clsx`, `tailwind-merge`
- Testing: Vitest (+ `@vitest/coverage-v8`)
- Linting: ESLint (typescript-eslint, react-hooks, react-refresh)
- Path alias: `@` -> `frontend/src`

## Backend

- Language: Python 3.13
- Framework: FastAPI
- ASGI server: Uvicorn
- Data validation: Pydantic (models + `Literal` domain enums)
- Debugging: debugpy (port 5678)
- Testing: pytest, pytest-cov, httpx (via `fastapi.testclient`)

## Orchestration and dev environment

- Containerization: Docker, orchestrated with Docker Compose.
- Frontend image base: `node:24-alpine`, served by Vite on `5173`.
- Backend image base: `python:3.13-slim`, Uvicorn on `8000` (+ debugpy `5678`).
- Ports (host:container): frontend `5173:5173`, backend `8000:8000` and `5678:5678`.
- `frontend` depends on `backend` in `[../docker-compose.yml](../docker-compose.yml)`.

## Runtime integration

- The Vite dev server proxies `/api` to `http://backend:8000`
  (see `[../frontend/vite.config.ts](../frontend/vite.config.ts)`), so no extra env
  vars are needed locally.
- To target a different backend origin, set `VITE_API_BASE_URL` (copy
  `frontend/.env.example` to `.env`).

## Key commands

- Root: `docker compose up --build`
- Frontend (`frontend/`): `npm run dev`, `npm run build`, `npm run lint`,
  `npm run test`, `npm run test:watch`, `npm run test:coverage`
- Backend (`backend/`): `uvicorn app.main:app --reload --host 0.0.0.0 --port 8000`,
  `pytest`

## URLs (local)

- Frontend: http://localhost:5173
- Backend: http://localhost:8000
- API docs (Swagger): http://localhost:8000/docs

## Deeper context

- Full tech stack doc:
  [../docs/context/04-tech-stack.md](../docs/context/04-tech-stack.md)
- Frontend deps: [../frontend/package.json](../frontend/package.json)
