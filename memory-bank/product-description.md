# Product Description

## What it is

A financial metrics dashboard that visualizes full-year business performance. A
FastAPI backend produces financial movement data and exposes analytics endpoints; a
React + TypeScript frontend fetches that data and renders KPI cards and charts.

## Why it exists

To give a clear, at-a-glance view of business financial health - income vs. outcome,
profit, and margin over time - and to support deeper analysis through filtering,
grouped summaries, category ranking, period comparison, and outcome-spike alerts.

## Primary users and use cases

- Business/finance stakeholders reviewing overall performance for a period.
- Analysts drilling into trends by date range, category, operation type, and
  business type (B2B/B2C).
- Typical tasks:
  - See headline KPIs: total income, total outcome, profit, profit margin.
  - Track monthly income vs. outcome and profit-percent trends.
  - Compare a period against the preceding equivalent period.
  - Identify top categories and outcome anomalies.

## Core value

- Fast comprehension: KPIs plus trend charts on one screen.
- Analytical depth: server-side filtering and aggregation across several dimensions.
- Deterministic, demo-friendly data for stable outputs and repeatable testing.

## Current scope and stage

- Demo/prototype stage: all data is generated in memory (deterministic seed), with
  no database or external data source.
- The frontend currently consumes only the raw `/api/metrics` endpoint; richer
  analytics endpoints exist on the backend but are not yet used by the UI.
- Not production-hardened (open CORS, no auth, no persistence).

## Deeper context

- Overview: [../docs/context/01-overview.md](../docs/context/01-overview.md)
- Repo README: [../README.md](../README.md)
- Known risks and gaps:
  [../docs/context/05-risks-gaps-improvements.md](../docs/context/05-risks-gaps-improvements.md)
