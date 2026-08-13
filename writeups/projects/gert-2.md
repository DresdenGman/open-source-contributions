# GERT #2 — Provenance-aware model and load sources

> Project engineering · 10 files · +71/−15 · merged August 2, 2026

## Classification

This pull request was merged into a project repository under direct control. It demonstrates implementation and validation work but is intentionally separated from independently reviewed upstream contributions.

## Outcome

[PR #2](https://github.com/dresdengoehner/Grid-Extreme-Risk-Toolkit-GERT-/pull/2) separated forecasting-model provenance from grid-load provenance across the Grid Extreme Risk Toolkit's backend, API contract, shared types, and frontend.

## Problem and design

The application previously represented “source” too broadly. A risk estimate can depend on a model produced by one component and load data supplied by an independent grid operator. Collapsing both into one label obscures what is modeled, what is observed, and which organization is authoritative for each value.

The change treated provenance as a data-contract concern. Source fields were distinguished in data adapters, propagated through API responses, represented in TypeScript types, and rendered separately in the UI. Carrying the distinction end to end prevented a backend-only rename that the user interface could accidentally flatten again.

## Validation and lessons

The synchronized change was validated with 66 Python tests, 23 frontend tests, and a production Next.js build. The main lesson was that trustworthy analytical presentation requires provenance to be typed and transported consistently. A disclaimer at the UI layer cannot compensate for an ambiguous backend schema.

## Evidence

- [Merged PR #2](https://github.com/dresdengoehner/Grid-Extreme-Risk-Toolkit-GERT-/pull/2)
- Final scope: API, four data adapters, shared frontend types, UI, and tests
