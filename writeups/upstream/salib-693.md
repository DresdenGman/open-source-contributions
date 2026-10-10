# SALib #693 — Finite endpoint validation for Morris sampling

> Scientific-software reliability · 2 files · +125/−0 · merged October 9, 2026 PDT (October 10 UTC)

Morris sensitivity designs can include the unit-interval endpoints 0 and 1. Applying an unbounded distribution's inverse CDF at those endpoints can produce infinite model inputs. [PR #693](https://github.com/SALib/SALib/pull/693) makes this incompatibility explicit instead of allowing it to surface downstream as a numerical failure.

## Initial diagnosis and final implementation

The initial proposal checked generated samples after distribution scaling. Maintainer ConnectedSystems reviewed and changed the implementation before merging it, explicitly noting an intention to expand checks to other SALib methods. The merged design validates endpoint compatibility **before generating Morris trajectories**, so rejection does not depend on a random seed happening to sample an endpoint.

The final `_check_endpoints_finite` helper constructs a two-row 0/1 probe for every parameter and uses SALib's existing distribution scaler. It rejects non-finite transformed endpoints, names the affected factors and distributions, checks distribution-count consistency, and directs users to an explicit bounded distribution such as `truncnorm`. It does not silently truncate the requested distribution or change the common scaler for other sampling methods.

## Regression evidence

The final diff covers normal, lognormal and both Weibull parameterizations; a seed/level combination whose generated trajectories avoid endpoints; mixed bounded/unbounded factors; grouped inputs; and finite bounded-distribution controls. The seed control is important: the model's requested distribution determines validity, rather than which sample happens to be drawn.

The final PR head (`4225e9918e9ad256838d0686a44e9f673dec0e17`) passed upstream lint and pytest jobs on Python 3.10, 3.12 and 3.14. The initial PR description's local test totals describe the original proposal and are not presented as a fresh test run of the maintainer's final revision. This writeup records public CI evidence; no new local test run was performed for this documentation update.

## Outcome and significance

ConnectedSystems merged the PR as `34c2fbb0b23758f99e272086b6653fac4f45b33d` at `2026-10-10T00:27:08Z` (October 9, 17:27:08 PDT). This is the fifth merged SALib contribution and the twentieth merged contribution to independent upstream repositories in the portfolio.

Its scientific value is a clear validity boundary for sensitivity-analysis inputs, with reproducible edge-case coverage. It does not establish calibration improvements, general support for unbounded Morris inputs, or checks across all SALib methods. The final endpoint-first design and extended coverage include maintainer-authored changes; they are not represented as solely contributor-authored work.

## Primary evidence

- [Merged PR and final diff](https://github.com/SALib/SALib/pull/693/files)
- [Maintainer's review and modification note](https://github.com/SALib/SALib/pull/693#issuecomment-6091602118)
- [Original issue #515](https://github.com/SALib/SALib/issues/515)
- [Final pytest run](https://github.com/SALib/SALib/actions/runs/38008604019)
- [Final lint run](https://github.com/SALib/SALib/actions/runs/38008604130)
- [Merge commit](https://github.com/SALib/SALib/commit/34c2fbb0b23758f99e272086b6653fac4f45b33d)
