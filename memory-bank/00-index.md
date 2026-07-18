# Memory Bank

This is the project memory bank for the financial metrics dashboard. It is the
canonical, distilled context an agent should read at the start of a task before
analyzing code, modifying files, or generating outputs.

## How and when to use it

- Read this index first, then the files in the reading order below.
- Treat these files as the fast-path summary. For deeper detail, follow the links
  into [../docs/context/](../docs/context/) (long-form docs) and
  [../.agents/rules/](../.agents/rules/) (conventions the code must follow).
- Keep the memory bank current: when the product, stack, architecture, or status
  changes, update the relevant file here as part of the same change.

## Reading order

1. [product-description.md](product-description.md) - what the product is and why.
2. [tech-stack.md](tech-stack.md) - languages, frameworks, tooling, commands.
3. [architecture.md](architecture.md) - services, ports, and end-to-end data flow.
4. [domain-and-api.md](domain-and-api.md) - domain enums, KPI definitions, endpoint catalog.
5. [project-status.md](project-status.md) - current state, recent changes, next steps.
6. [roadmap.md](roadmap.md) - prioritized backlog (short/medium/long-term).

## Related sources (do not duplicate; link out)

- Long-form context: [../docs/context/](../docs/context/)
  - [01-overview.md](../docs/context/01-overview.md),
    [02-frontend-breakdown.md](../docs/context/02-frontend-breakdown.md),
    [03-backend-breakdown.md](../docs/context/03-backend-breakdown.md),
    [04-tech-stack.md](../docs/context/04-tech-stack.md),
    [05-risks-gaps-improvements.md](../docs/context/05-risks-gaps-improvements.md)
- Project conventions/rules: [../.agents/rules/](../.agents/rules/)
  (see [00-index.md](../.agents/rules/00-index.md))
- Repo entry point: [../README.md](../README.md)
