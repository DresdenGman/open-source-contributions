# StatsForecast #1175 — `conformal_error` prediction intervals

> Forecast uncertainty · 4 files · +81/−5 · merged July 13, 2026

## Outcome

[PR #1175](https://github.com/Nixtla/statsforecast/pull/1175) added a `conformal_error` prediction-interval method based on quantiles of conformity scores. Maintainer `nasaul` requested corrections to the public enablement path and interval logic before approving the final contribution.

## Problem and implementation

StatsForecast already supported `conformal_distribution`; the requested companion method needed to calculate an error margin from conformity-score quantiles and expose it through the same public `ConformalIntervals` configuration.

The first implementation added the interval routine, method dispatch, and focused tests. Review identified an important integration miss: the constructor's `allowed_methods` still rejected `conformal_error`, so the new code could not be reached through the public API even though its internal function existed. The maintainer also requested docstring updates, corrections in the interval code, and an end-to-end model path.

I updated the allowlist and documentation, corrected the reviewed logic, and expanded coverage through real forecast configuration rather than testing only a private helper. This ensured the full route—from user-supplied method name to model forecast output—worked.

## Lessons

Adding an implementation is not the same as adding a feature. Dispatch tables, validation allowlists, configuration objects, and documentation are all part of the public execution path. The missed allowlist was particularly instructive: unit-level correctness can coexist with a completely unusable public feature. End-to-end tests are the right defense for that class of integration error.

## Evidence

- [Merged PR #1175](https://github.com/Nixtla/statsforecast/pull/1175)
- Maintainer sequence: changes requested, corrected, approved
- Final files: model logic, conformal configuration, core tests, model tests
