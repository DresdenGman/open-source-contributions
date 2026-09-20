# SALib #685 — Deterministic discrepancy regression testing

> Scientific-software reliability · 1 file · +1/−1 · merged September 18, 2026

## Outcome

[PR #685](https://github.com/SALib/SALib/pull/685) removed a source of intermittent CI failures from SALib's discrepancy regression test. The merged patch supplies a fixed seed to the Latin hypercube sampler while preserving the algorithm, sample count, expected values, and numerical tolerance.

## Problem and diagnosis

The test generated 1,000 Latin hypercube samples without a seed and compared their discrepancy values with `[0.33, 0.33, 0.33]` using an absolute tolerance of `0.005`. Because `latin.sample` creates a fresh unseeded NumPy generator when `seed=None`, the input design changed on every run. One unrelated PR recorded `0.335064` for the first output—only `0.000064` beyond the assertion boundary—despite making no changes to the sampler or discrepancy calculation.

The useful distinction was between a numerical regression and sampling variance. Loosening the tolerance would have hidden the symptom without making the regression reproducible. Seeding the stochastic fixture instead preserves the strength of the existing assertion and makes a future failure attributable to code or dependency changes.

## Implementation and validation

The final diff changes only the sampler call in `tests/test_discrepancy.py`, passing `seed=123456`. Local validation covered the target test, the complete discrepancy test file, repeatability across process runs, and the project's lint/formatting checks. No production code was changed.

The contribution was intentionally kept separate from SALib's other stochastic regression failure: #687/#688 concerns a legacy global random-state bootstrap in the delta analyzer, whereas this fix controls the modern generator created by Latin sampling.

## What I learned

Tests for randomized scientific methods should distinguish distributional uncertainty from software regression. A fixed seed is appropriate when the purpose is to lock down a deterministic numerical reference; broader statistical properties belong in separate invariant or convergence tests. Narrow CI repairs can carry real value when they prevent unrelated scientific contributions from being blocked by noise.

## Evidence

- [Issue #681](https://github.com/SALib/SALib/issues/681)
- [Merged PR #685](https://github.com/SALib/SALib/pull/685)
- Merge commit `000a2a91b953f0068209dce5d0a1e0a21298866e`
