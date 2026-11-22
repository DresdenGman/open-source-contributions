# black #5660 — Keeping commented expressions together when the comment trails

> Code formatter preview style · 4 files · +103/−9 · merged November 19, 2026

## Outcome

[PR #5660](https://github.com/psf/black/pull/5660) completed the preview feature `keep_commented_expressions_together` for the trailing-comment case and closed issue [#638](https://github.com/psf/black/issues/638). The feature ([#5455](https://github.com/psf/black/pull/5455)) kept a commented expression together when the comment came before the expression, but an expression whose comment trails it — a comment after the expression, before the closing bracket — was still split apart, in preview as well as stable style. Maintainer `cobaltt7` merged the PR.

## Implementation

In `delimiter_split`, when the last standalone comment sits after the last highest-priority delimiter at the same bracket depth, and the split point is not a comprehension, the split now yields to `standalone_comment_split`. Following black's stability policy, stable style output is unchanged: the change takes effect only under `--preview`, and when the feature is promoted, the remaining #638 shapes are covered with it.

One bookkeeping detail from the PR itself: the CHANGES.md entry initially referenced the issue (#638), and a follow-up commit replaced it with this PR's number, per the convention at the top of that file.

## Validation

The full suite matched the baseline exactly (762 passed, 2 skipped, 61 subtests, no new failures), including 7 new preview data cases: comments before, after, and between operands, plus guards that overlong expressions still split and that magic trailing comma behavior is unchanged. The self-hosting check also held: black's own source tree is unchanged when formatted with the patched build.

The project's diff-shades run confirmed the stability boundary from the outside. Across 22 projects, 14,241 files, and 3,198,708 lines, stable style produced no changes at all, while preview style changed 10 files across 5 projects (41 changes, +13/−28) — the intended shapes only.

## What I learned

In a formatter, "the feature works" is only half the contract; the other half is proving nothing else moved. The evidence that mattered here was negative: an exact baseline match for the test suite, an unchanged self-format, and zero stable-style diff across a 3.2-million-line corpus. Working under a preview flag also shapes the design — behavior can be completed incrementally behind the flag without breaking the stability promise, as long as the flag boundary is respected exactly.

## Evidence

- [Issue #638](https://github.com/psf/black/issues/638)
- [Merged PR #5660](https://github.com/psf/black/pull/5660)
- Merge commit `2d7d76d3746783ab4ec1987d188f0ae1f3623ac6`
