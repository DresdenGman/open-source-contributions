# Fairlearn #1674 — Auto-detect `MetricFrame` confidence intervals

> Fairness evaluation and plotting API · 3 files · +322 · merged July 11, 2026

## Outcome

[PR #1674](https://github.com/fairlearn/fairlearn/pull/1674) lets `plot_metric_frame` use bootstrap confidence intervals already computed by `MetricFrame`. Explicitly supplied intervals still take precedence. Maintainer `romanlutz` approved the feature after a review-driven architectural rewrite.

## Problem

`MetricFrame` could calculate group-level confidence intervals through `n_boot` and `ci_quantiles`, but the primary plotting helper did not consume them automatically. Users had to extract and pass the same data again, creating friction and opportunities for misalignment.

## Initial approach and architectural feedback

My first helper read `_result_cache` directly. It worked, but it crossed a private API boundary and represented intervals in a way that could silently mismatch reordered metrics. Roman's changes-requested review required using public `by_group_ci` and `ci_quantiles` properties and preserving metric-to-interval identity.

I rewrote the bridge to rely on public API, map metrics explicitly, normalize single-metric Series results to a DataFrame, sort reversed quantiles, and handle more than two quantiles by selecting the outer pair with a warning. Explicit `conf_intervals` remain an override rather than being overwritten by auto-detection.

## Tests and final result

The final test expansion covers single and multiple metrics, missing bootstrap results, explicit overrides, quantile ordering, multiple quantiles, and Series/DataFrame shape differences. A release-note entry documents the behavior. Roman verified the focused suite and formatting before approval.

## What I learned

A functionally correct implementation can still be architecturally wrong. Private cache access would have coupled plotting to internals; the public-property rewrite produced a more stable integration. Explicit metric mappings also prevent silent visualization errors that positional lists can introduce. Reviewer pushback improved the design more than a quick approval would have.

## Evidence

- [Issue #1487](https://github.com/fairlearn/fairlearn/issues/1487)
- [Merged PR #1674](https://github.com/fairlearn/fairlearn/pull/1674)
- Final files: plotting implementation, 247 lines of tests, release note
