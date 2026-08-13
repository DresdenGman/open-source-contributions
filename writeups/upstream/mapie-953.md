# MAPIE #953 — AUROC and AUARC uncertainty metrics

> Statistical feature development · 5 files · +349 · merged July 10, 2026

## Outcome

[PR #953](https://github.com/scikit-learn-contrib/MAPIE/pull/953) added two task-agnostic uncertainty-evaluation metrics, `aucroc_score` and `auarc_score`, together with tests, exports, history, and contributor records. It moved through a substantive changes-requested review to approval.

## Problem and API design

Issue #551 requested metrics that evaluate how well confidence or uncertainty separates correct from incorrect predictions and supports rejection-based evaluation. The initial implementation followed terminology from the issue, but review required a clearer public API. Maintainer `allglc` preferred `correctness` and `confidence`, names that align more directly with the statistical interpretation.

I adopted the new vocabulary and revised validation, documentation, and examples together so the public contract stayed coherent.

## Implementation and debugging

The new module implements:

- AUROC over correctness labels and confidence scores;
- AUARC, measuring accuracy as progressively less-confident samples are rejected;
- validation for binary correctness, finite values, compatible shapes, and confidence orientation;
- public exports, docstrings, examples, and regression coverage.

The review cycle exposed issues that would not have been caught by a happy-path test alone: incorrect illustrative output in a doctest, uncovered validation branches under near-total coverage requirements, and an inaccurate academic citation. Each was corrected before merge. The final PR accumulated ten commits across implementation and review refinement.

## What I learned

Statistical APIs require precision at several levels simultaneously: formulas, naming, input semantics, examples, and citations. A doctest is executable specification, not decorative prose. High coverage standards also forced explicit reasoning about malformed inputs rather than relying on natural-use assumptions. Most importantly, correcting the citation reinforced that scholarly references must be independently verified.

## Evidence

- [Issue #551](https://github.com/scikit-learn-contrib/MAPIE/issues/551)
- [Merged PR #953](https://github.com/scikit-learn-contrib/MAPIE/pull/953)
- Final scope: new metrics module, tests, exports, history, and authorship record
