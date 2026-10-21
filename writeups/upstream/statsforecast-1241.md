# StatsForecast #1241 — Expose an optional fixed theta parameter

> Upstream engineering · 2 files · +47/−0 · merged October 20, 2026 UTC

[PR #1241](https://github.com/Nixtla/statsforecast/pull/1241) exposes a lower-level capability through the public AutoTheta, OptimizedTheta and DynamicOptimizedTheta constructors: callers may provide a fixed theta rather than estimate it from the data.

## Problem and investigation

[Issue #860](https://github.com/Nixtla/statsforecast/issues/860) asked for a manually chosen theta to avoid repeated optimization when fitting many series. The lower-level auto_theta function already accepted that parameter, but the public model constructors did not expose it. The useful change was therefore a narrow API bridge, not a replacement optimizer.

## Implementation

The merged diff adds an optional theta argument, defaulting to None, and forwards it through fitting, memory-efficient forecasting and direct simulation fitting. A constructor guard accepts only finite values in the same [1, 1e10] range as the internal optimizer.

The default continues to estimate theta. The supplied value controls optimized Theta candidates; standard Theta candidates retain their defined value of 2. Existing model defaults are not replaced by an arbitrary fixed value.

## Regression and review evidence

The new regression file checks that:
- AutoTheta with the optimized model, OptimizedTheta and DynamicOptimizedTheta use theta=2.5;
- fit/predict and memory-efficient forecast agree, including requested interval outputs;
- non-finite and out-of-range theta values raise ValueError.

GitHub reports successful pytest, Lint, CI and build-docs workflows on the PR head 08d7f2e374d6439cb4d75360cc60426b332ec9ca. Mariana Menchero García (MMenchero) [approved the change](https://github.com/Nixtla/statsforecast/pull/1241#pullrequestreview-5592407418) and [confirmed the review](https://github.com/Nixtla/statsforecast/pull/1241#issuecomment-6302889418).

The PR description reports 235 local passing tests and an illustrative five-run benchmark. Those historical runs were not rerun for this writeup. The benchmark is not a general performance guarantee; the existing benchmark bot reported 38 untouched benchmarks, not validation of a fixed-theta speedup.

## Outcome and limits

GitHub records the merge at 2026-10-20T23:02:22Z, commit 0205851f29a9b5e70eb6e2507cf2f6fa697b0ee2. The linked feature request closed as completed one second later.

This is a reviewed and merged upstream API contribution with validation and regression coverage. It does not establish that the feature is in a published release, adopted by users, more accurate, or faster on every workload. Other open StatsForecast PRs remain separate work.

## Lesson

When an internal capability is already present, the contribution can be a small, testable public contract: preserve defaults, validate against the implementation's real bounds, and verify agreement across the multiple execution paths users rely on.

## Primary evidence

- [Merged PR and final diff](https://github.com/Nixtla/statsforecast/pull/1241/files)
- [Original request #860](https://github.com/Nixtla/statsforecast/issues/860)
- [Maintainer approval](https://github.com/Nixtla/statsforecast/pull/1241#pullrequestreview-5592407418)
- [pytest](https://github.com/Nixtla/statsforecast/actions/runs/35638276674)
- [Lint](https://github.com/Nixtla/statsforecast/actions/runs/35638276223)
- [CI](https://github.com/Nixtla/statsforecast/actions/runs/35638276210)
- [build-docs](https://github.com/Nixtla/statsforecast/actions/runs/35638276091)
- [Merge commit](https://github.com/Nixtla/statsforecast/commit/0205851f29a9b5e70eb6e2507cf2f6fa697b0ee2)
