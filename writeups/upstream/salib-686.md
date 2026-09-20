# SALib #686 — Goda Shapley effects estimation

> Global sensitivity analysis · 11 files · +659 · merged September 18, 2026

## Outcome

[PR #686](https://github.com/SALib/SALib/pull/686) added an end-to-end implementation of Goda's Monte Carlo estimator for Shapley effects to SALib. The merged feature includes sampling, analysis, confidence intervals, normalized results, Python and command-line workflows, documentation, examples, validation, and regression tests.

The contribution moved SALib beyond variance-based indices that can be hard to interpret with interacting inputs by adding a cooperative-game-theoretic allocation of output variance. The initial implementation supports independent, ungrouped inputs—the assumptions of the published algorithm—rather than claiming dependent-input support without a compatible conditional-sampling contract.

## From paper to library contract

Goda's method draws two independent input vectors for each Monte Carlo replication and uses a random permutation to replace one coordinate at a time. Each trajectory therefore needs `D + 1` model evaluations, for `N × (D + 1)` rows. The analyzer recovers the changed factor at each step and estimates its marginal contribution to output variance.

The main design question was representation. The paper's estimator produces raw effects in output-variance units, while users often expect normalized shares summing to one. After discussion with maintainer `ConnectedSystems`, the final API preserves raw `Shapley` estimates and confidence half-widths and exposes normalized shares through `Si.normalized`. This keeps the estimator's variance interpretation intact without sacrificing the common unit-sum view. Normalization explicitly rejects a zero-total result instead of dividing silently.

## Validation and scope control

The implementation validates trajectory shape, one-factor-per-step transitions, complete factor permutations, finite inputs, confidence levels, minimum trajectory count, and unsupported groups. Tests cover the sampler, analyzer, command-line round trips, interface behavior, analytical Ishigami behavior, variance decomposition, deterministic seeding, malformed trajectories, and normalized-result edge cases. Documentation and runnable Python, shell, and Windows examples make the new method usable outside the test suite.

The dependent-input extension in SAShE.jl was investigated but deferred. It requires user-supplied conditional sampling during each permutation step, which does not fit cleanly into SALib's existing `sample → external model → analyze` separation without a broader API design. Keeping that out of the first PR prevented an unsupported claim from entering the public interface.

## Maintainer collaboration and final optimization

The original two commits supplied the method and normalized-result API. Before merge, `ConnectedSystems` added documentation alignment and vectorized the analyzer's nested trajectory/step loops. The final implementation identifies factor changes across all trajectories, computes contributions in arrays, and scatters them into factor order.

The maintainer reported approximately **15×–50× faster analysis** across several measured problem sizes, with a documented memory tradeoff for extremely high dimensions. Those timing results belong to the maintainer's final optimization and are recorded as such; they are not presented as performance measurements from my original patch.

This review cycle was particularly valuable: the contribution supplied the full mathematical and user-facing feature, while maintainer expertise improved its execution model before acceptance.

## What I learned

Implementing a published method is not just translating equations. The difficult work is defining result semantics, mapping the estimator into an existing sample/analyze architecture, validating structural assumptions, and making failure modes explicit. Leaving dependent factors out was a substantive engineering decision: scope honesty is more valuable than advertising a capability the library cannot yet support correctly.

## Evidence

- [Issue #682](https://github.com/SALib/SALib/issues/682)
- [Merged PR #686](https://github.com/SALib/SALib/pull/686)
- Goda, T. (2021), *A simple algorithm for global sensitivity analysis with Shapley effects*
- Merge commit `387bc4ed37e0e983d5f77f905478a97a89e77339`
