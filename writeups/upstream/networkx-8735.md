# NetworkX #8735 — PageRank tolerance convergence caveat

> Numerical reasoning and scope adaptation · 1 file · +6 · merged July 7, 2026

## Outcome

[PR #8735](https://github.com/networkx/networkx/pull/8735) documented a PageRank tolerance edge case. The work began as a runtime-warning proposal and, following maintainer guidance, became a concise caveat placed directly in the public `tol` parameter description.

## Mathematical issue

NetworkX stops PageRank power iteration when the L1 difference is below `len(G) * tol`. Because the L1 distance between probability distributions is at most 2, a threshold satisfying `len(G) * tol >= 2` can make the convergence test ineffective: the iteration may appear converged after one step regardless of useful accuracy.

## Initial design and feedback

My first implementation added a `RuntimeWarning` to the Python and SciPy paths plus regression tests. Maintainer `dschult` agreed that the caveat was valid but preferred documentation: runtime warnings add overhead and are often ignored for a parameter-interpretation issue.

I removed the warning code and tests, reducing the proposal to documentation. Maintainer `rossbar` then improved discoverability by moving and shortening the note in the `tol` parameter description. Both maintainers approved the final version.

## Final result and lessons

The merged change adds six lines and no runtime behavior. It tells users to choose tolerance carefully for large graphs and explains the `len(G) * tol >= 2` boundary.

This was a useful lesson in mature-library judgment: a mathematically correct runtime intervention is not necessarily the best interface. Accepting a smaller final diff preserved the diagnosis while matching maintainer preferences for performance, discoverability, and API stability. Reviewer-authored refinement is credited as part of the collaborative result.

## Evidence

- [Issue #8651](https://github.com/networkx/networkx/issues/8651)
- [Merged PR #8735](https://github.com/networkx/networkx/pull/8735)
- Final scope: `networkx/algorithms/link_analysis/pagerank_alg.py`, documentation only
