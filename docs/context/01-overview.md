# Context and Overview

## Project Snapshot

This repository is a financial metrics dashboard composed of:

- A FastAPI backend that produces deterministic mock financial movement data and exposes analytics endpoints.
- A React + TypeScript frontend that fetches metrics and renders KPI cards and charts.
- Docker Compose orchestration for local development.

Primary use case: visualize full-year business performance (income, outcome, profit, margin) and support filtering, summaries, category ranking, period comparison, and alerts.

## High-Level Architecture

- Backend service:
  - Runtime: Python 3.13 (container image)
  - Framework: FastAPI
  - Responsibilities: data generation, filtering, aggregation, analytics API responses
- Frontend service:
  - Runtime: Node 24 (container image)
  - Framework: React 19 + TypeScript + Vite
  - Responsibilities: fetch API data, transform into KPI/chart models, render dashboard UI
- Connectivity:
  - Frontend proxies `/api` to `http://backend:8000` in Vite dev config.
  - Docker Compose binds frontend to `5173` and backend to `8000`.

## End-to-End Data Flow

1. Backend route generates deterministic movement data using seed 42.
2. Optional query parameters filter data server-side.
3. Frontend fetches `/api/metrics`.
4. Frontend utilities compute KPI totals and monthly chart points.
5. Dashboard components render KPI cards and chart visualizations.

This deterministic flow provides stable outputs for repeatable testing and demos.

## Runtime and Workflow

### Main Commands

- Root:
  - `docker compose up --build`
- Frontend:
  - `npm run dev`
  - `npm run build`
  - `npm run test`
  - `npm run test:watch`
  - `npm run test:coverage`
- Backend:
  - `uvicorn app.main:app --reload --host 0.0.0.0 --port 8000`
  - `pytest`

### Container Notes

- Backend container:
  - Base image `python:3.13-slim`
  - Starts via `debugpy` + `uvicorn --reload`
  - Exposes `8000` and `5678`
- Frontend container:
  - Base image `node:24-alpine`
  - Runs Vite on `5173`
