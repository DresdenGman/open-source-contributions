# SALib #694 — Retained bootstrap resamples for Morris analysis

> Global sensitivity analysis · 4 files · +278/−16 · merged October 29, 2026

## Outcome

[PR #694](https://github.com/SALib/SALib/pull/694) closed issue [#579](https://github.com/SALib/SALib/issues/579) by adding an opt-in `keep_resamples` argument to Morris analysis: the bootstrap `mu_star` estimates are retained and returned as `mu_star_conf_all`, so users can run their own ranking and robustness analysis on coherent per-resample replicates instead of only the summary confidence interval. The PR went through a changes-requested review round, was revised, and was merged by maintainer `ConnectedSystems`. Maintainer `willu47` also brought the original issue reporter into the loop, asking whether the submission met the need behind #579.

## Design

The retained matrix has shape `(num_resamples, number_of_groups)`. Without groups, its columns follow the problem parameter order. With groups, each bootstrap replicate is aggregated using the same group mean as the other Morris `mu_star` outputs.

Making the retained rows coherent required a change underneath the feature. Morris previously generated independent trajectory indices inside the parameter loop. That was sufficient for marginal confidence widths, but it cannot produce a coherent per-resample ranking across parameters. The draw was moved outside the loop, so one trajectory-resample index matrix is shared across parameters and each retained row is a single bootstrap replicate. The estimator itself is preserved, and the storage flag does not change confidence results: both paths use the same shared trajectory draws for a given seed. One visible consequence is that exact seeded confidence values can change from earlier versions; the CLI regression fixtures were updated to record that intentional numerical change.

For grouped analyses, the existing `mu_star_conf` definition remains unchanged — it is the mean of the parameter-level confidence widths — so it is not generally identical to a confidence width recomputed from an already-grouped `mu_star_conf_all` column. DataFrame, plotting, and `print_to_console` behavior are preserved by excluding the two-dimensional resample matrix from tabular conversion.

## Review iteration

`ConnectedSystems` requested changes with specific points: always return `(mu_star_conf, mu_star_resamples)` and let `_compute_statistical_outputs` drop the resamples when they are not needed, simplifying the return-type contract; add a note that the bootstrapping behavior has changed (joint bootstrap over trajectories); and a minor performance note that the result collation could be a single operation (`resamples @ groups / groups.sum(axis=0)`) rather than a list comprehension. `willu47` asked that the change be added to the changelog as a notification to users who upgrade. The review also flagged a minor caveat — the result dictionary would no longer be picklable with `pickle` — while noting that SALib does not generally support pickling (`dill` works), so it did not block the change.

The PR was revised in response, including the changelog notification; the revised head passed the Test, Linting, and ReadTheDocs checks while awaiting re-review, and the PR was then merged.

## Validation

The full test run reported 245 passed, 1 xfailed, with `ruff` and `black` checks on the touched files. Regression coverage verifies the opt-in/default contract, confidence invariance, shared trajectory alignment, reconstruction from retained estimates, grouped aggregation, default key compatibility, and DataFrame/console conversion.

## What I learned

A "just return the intermediate values" feature can require changing the computation underneath it: the per-parameter independent draws were fine for the summary statistic the library had always returned, but retained data only means something if every row is the same bootstrap replicate across parameters. Exposing internals is also an API commitment — the matrix shape, the grouping semantics, and the deliberate difference from the existing `mu_star_conf` all had to be pinned down in tests and documentation rather than left for users to discover.

## Evidence

- [Issue #579](https://github.com/SALib/SALib/issues/579)
- [Merged PR #694](https://github.com/SALib/SALib/pull/694)
- Merge commit `820ffdb105c169ffcef7cd5ae1ecbf6a2900afdb`
