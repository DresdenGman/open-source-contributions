# SALib #716 — Group-aware Shapley effects

> Global sensitivity analysis · 9 files · +212/−44 · merged November 21, 2026 PST (November 22 UTC)

## Outcome

[PR #716](https://github.com/SALib/SALib/pull/716) implemented item 2 of issue [#708](https://github.com/SALib/SALib/issues/708): the Shapley effects analyzer now handles grouped inputs instead of warning and ignoring the grouping. A group's Shapley effect is the sum of its members' effects (the bundle property), and its confidence interval accounts for the covariance of member contributions within each trajectory. Maintainer `ConnectedSystems` approved the PR as submitted and merged it.

This removes a limitation that an earlier contribution, [#686](https://github.com/SALib/SALib/pull/686), had deliberately left in place: the original Goda estimator implementation validated grouped problems and rejected them as unsupported. Group support arrived later, as its own change with its own equivalence tests.

## Design

The analyzer previously followed the per-factor path regardless of grouping. The fix aggregates per-trajectory contributions by group (`contributions @ compute_groups_matrix`) before the existing mean, variance, and delta-method machinery runs. Aggregating the contributions first — rather than averaging member confidence intervals afterwards — is what makes the group interval coherent: members of a group are evaluated on the same trajectories, so their contributions covary, and the interval has to reflect that.

Following the Morris μ/μ* convention, the analyzer now also reports `shapley_abs` and `shapley_abs_conf` — the mean absolute per-trajectory contribution and its confidence half-width — for grouped and ungrouped problems alike. The signed/absolute pair makes cancellation between member contributions visible instead of letting a near-zero signed value look like an unimportant group. `Si["names"]` now holds the group names, a single-group problem collapses to one row, and identity grouping reproduces the previous output exactly, which anchors backward compatibility.

The sampler is unchanged: the sampling design is per-factor and group-independent. Its warning now states the actual limitation — grouping does not change the sampling design, there is no group co-sampling, and interactions between members of the same group are not resolved — and the docstrings on both sides describe the aggregation convention.

## Validation and scope control

The old warn-and-ignore tests were replaced with group-support tests: a group effect equals the sum of member effects on identical data, group totals preserve the ungrouped total, single-group collapse, identity-grouping equivalence, and a cancellation case where `shapley_abs` stays visible while the signed value cancels. A new Shapley case was added to `tests/test_group_analysis.py`, the support matrix was updated in `README-advanced.md` and `docs/user_guide/advanced.rst`, and a CHANGELOG entry was added. The affected test files went from 40 passed on baseline main to 44 passed; the full suite passed (378 passed, 1 xfailed).

Scope was held to item 2 of #708. Item 1 (a shared aggregation helper and `Si["ungrouped"]`), the item 3 cleanups, and group co-sampling were deliberately left out.

## What I learned

When an aggregate statistic replaces per-member statistics, the equivalence cases — identity grouping, single-group collapse, totals preserved — are the tests that make the change safe, because they pin the new path to the old behavior wherever the two should agree. Implementing group support separately from the original estimator, rather than in the same PR, kept both changes reviewable.

## Evidence

- [Issue #708](https://github.com/SALib/SALib/issues/708) (item 2)
- [Merged PR #716](https://github.com/SALib/SALib/pull/716)
- Merge commit `981c45147f817d40a75ef697ad60e88341e0fd83`
