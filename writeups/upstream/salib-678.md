# SALib #678 — Constant columns in multi-output Sobol analysis

> Numerical correctness · 2 files · +92/−12 · merged July 17, 2026

## Outcome

[PR #678](https://github.com/SALib/SALib/pull/678) fixed Sobol analysis when a multi-output result contains a mixture of constant and varying columns. The final change updated the analyzer and added regression coverage.

## Problem and root cause

SALib's earlier zero-variance guard used a scalar `np.ptp(y)` decision. That is sufficient for a single output, but it can miss a constant column inside a multi-output array because variation in another column makes the aggregate range nonzero. The constant column then proceeds through normalization and index computation, producing invalid values instead of the expected stable result.

The key reasoning step was to move the zero-variance decision from the whole matrix to each output column. This preserved normal behavior for varying outputs while handling constant outputs independently.

## Implementation and iteration

The analyzer now applies constant-output handling at column granularity. Tests cover mixed constant/varying multi-output inputs rather than only the all-constant case, because the mixed case is what exposed the flaw in the original scalar check.

The PR evolved across five commits and a maintainer review. The merged diff contains 30 additions and 12 deletions in `src/SALib/analyze/sobol.py` plus 62 lines of regression coverage in `tests/test_sobol.py`.

## Validation and lessons

The important validation property is isolation: correcting a constant output must not change the indices for adjacent varying outputs. This contribution reinforced that vectorized scientific code needs tests for heterogeneous columns, not just uniform fixtures. An aggregate statistic can hide per-output degeneracy even when its scalar result looks reasonable.

## Evidence

- [Issue #619](https://github.com/SALib/SALib/issues/619)
- [Merged PR #678](https://github.com/SALib/SALib/pull/678)
- Final scope: analyzer and Sobol regression tests
