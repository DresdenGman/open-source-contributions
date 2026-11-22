# linearmodels #862 — Not materializing weighted data when all weights are 1

> Estimator memory efficiency · 2 files · +213/−3 · merged November 6, 2026

## Outcome

[PR #862](https://github.com/bashtage/linearmodels/pull/862) stopped the IV estimators from materializing full-size `float64` copies of the input data when every weight is 1 — including the default unweighted case. On the reproduction case from issue [#607](https://github.com/bashtage/linearmodels/issues/607), peak memory dropped from 21.2 MiB to 10.7 MiB. Maintainer `bashtage` merged the PR.

## Problem

In `IV2SLS` (and the other IV estimators sharing the same base class), the weighted arrays `_wy`, `_wx`, and `_wz` were always constructed by multiplying the input data by the square root of the weights. That multiplication promotes the arrays to `float64` and allocates full-size copies, even when every weight is `1` and the weighted data is identical to the input — including the default unweighted case, where the weights are populated with `1.0` internally. For large low-dtype inputs, this is the memory blow-up reported in #607: full-size `float64` copies materialized unconditionally before estimation begins.

## Change

In `linearmodels/iv/model.py`, when all weights are `1`, `_wy`, `_wx`, and `_wz` now alias the original arrays instead of allocating multiplied `float64` copies, preserving input dtypes and avoiding the extra allocations. Weights are normalized to have mean `1.0` before these arrays are built, so any constant weight vector reaches this check as all ones. Any non-constant weight vector takes the existing materializing path unchanged.

Multiplying by a weight of exactly `1` is exact, so for `float64` and integer inputs the estimation results are unchanged. One deliberate consequence: `float32` inputs now stay in `float32` through the weighting step instead of being silently promoted to `float64`, so a `float32` fit agrees with the `float64` fit of the same data within `float32` precision rather than exactly (the tests use rtol `1e-6` for parameters and `1e-5` for the covariance).

Scope note: #607 also mentioned `PanelOLS`. `PanelOLS` does not unconditionally materialize weighted copies — it already branches on `np.any(weights != 1.0)` to take a fast path for unweighted data, the same idiom used here — so this PR is scoped to the IV models, where the unconditional copy occurred.

## Validation

Nine new test functions in `linearmodels/tests/iv/test_model.py`:

- Unit weights alias the original arrays (no copy, dtypes preserved); non-constant weights still materialize.
- Results with no weights and with constant weights are identical (parameters and covariance).
- A `float32` fit with all-ones `float32` weights matches the `float64` fit of the same data within the tolerances above; an integer-input fit with constant integer weights matches the `float64` results exactly.
- Equivalence against an independently constructed reference: 2SLS computed by hand from materialized `float64` arrays using `numpy.linalg.pinv` agrees with the new path to `1e-8` (measured deviation ~1e-13).
- Boundary behavior is unchanged from baseline: a single non-unit weight takes the materializing path, with the stored arrays scaled by the square root of the weights; `NaN` weights follow the existing missing-value contract (the observation is dropped, verified against a manual row deletion).

The IV test suite went from a baseline of 9521 passed / 2400 skipped / 0 failed to 9530 passed / 2400 skipped / 0 failed — the delta is exactly the 9 new test functions; the affected subset (`test_model`, `test_weight_config`, `test_data`, `test_gmm`) is 140 passed. The Panel suite was re-run at 3680 passed (`PanelOLS` code untouched). An A/B comparison of 20 result items between unweighted and all-ones-weighted runs of the new code is bit-identical.

Known limitation (performance only): weights that are constant only up to floating-point rounding (within 1 ulp of a constant after normalization) still take the materializing path. Results are correct; the shortcut is simply not taken.

## What I learned

The default path is a cost every user pays: an estimator that internally fabricates unit weights for the unweighted case and then pays the weighted path's full materialization cost makes the common case subsidize a special case. Checking for the identity case before materializing — the same idiom `PanelOLS` already used in the same codebase — halved peak memory on the reproduction case without changing any result for `float64` or integer inputs.

## Evidence

- [Issue #607](https://github.com/bashtage/linearmodels/issues/607)
- [Merged PR #862](https://github.com/bashtage/linearmodels/pull/862)
- Merge commit `2be3f6cf69a573a565064d979e575d2a74717c08`
