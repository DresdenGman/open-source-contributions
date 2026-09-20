# SALib #688 — Isolate delta regression from ambient RNG state

> Reproducible numerical testing · 1 file · +2/−1 · merged September 18, 2026

## Outcome

[PR #688](https://github.com/SALib/SALib/pull/688) made SALib's delta SVM regression deterministic by connecting it to the test module's existing random-seed fixture. The patch changed neither the analyzer nor the bootstrap count, expected results, or tolerance.

## Root cause and evidence

`delta.bias_reduced_delta` bootstraps through NumPy's legacy global random state. The regression test did not request the file's `set_seed` fixture, so tests executed earlier in the same process could change its bootstrap draws. This produced an actual unrelated CI failure on #686: an estimate near `0.570223` fell outside the unchanged reference tolerance around `0.633510`.

Local probes deliberately separated reproducibility from failure frequency. Ambient seeds 7 and 999 produced different estimates, although both happened to pass. Applying the existing fixture after either ambient state produced the same result, approximately `(0.639711, 0.036199)`. The evidence therefore established hidden state dependence without claiming that a five-seed sample measured the probability of failure.

## Implementation and validation

The merged test requests `set_seed`, whose established seed is 123456, and adds a comment explaining the legacy global-RNG dependency. Targeted delta tests passed under Python 3.10 and 3.14 environments. Upstream lint and the Python 3.10, 3.12, and 3.14 test matrix passed before merge; each test job reported 208 passed and one expected failure.

The stale GitHub documentation status was not bypassed with an empty commit or workflow change. The corresponding Read the Docs build reported success for the exact head commit, allowing the status mismatch to be recorded accurately rather than misdiagnosed as a documentation failure.

## What I learned

Reproducibility work requires tracing which random-number API a method actually uses. Supplying a modern `Generator` elsewhere in the suite does not control code that still reads NumPy's legacy global state. The smallest correct fix was to reuse the established fixture, keeping the scientific expectation and tolerance meaningful.

## Evidence

- [Issue #687](https://github.com/SALib/SALib/issues/687)
- [Merged PR #688](https://github.com/SALib/SALib/pull/688)
- Merge commit `6869261e9a8e927b2ee76d4f70c14151eeaac4bc`
