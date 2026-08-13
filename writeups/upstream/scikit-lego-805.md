# Scikit-lego #805 — Complete equal-opportunity warning

> Diagnostic clarity · 2 files · +5/−2 · merged July 5, 2026

## Outcome

[PR #805](https://github.com/koaning/scikit-lego/pull/805) corrected the warning emitted by `equal_opportunity_score` so it describes both data conditions that cause the metric to return zero. Maintainer `koaning` approved the focused change.

## Problem

The metric has a zero-return path when the data lacks the positive-target cases necessary to compute the score. The previous warning described only part of that condition, which could send users investigating the wrong input. The runtime behavior was intentional; the defect was incomplete diagnostic information.

## Implementation

I traced the conditional and aligned the warning text with its full boolean trigger, covering both `y_true` and predicted-label conditions. The corresponding regression expectation was updated. No metric math, public signature, or control flow changed.

The final scope was four additions and two deletions in `sklego/metrics.py`, plus a one-line test update. Keeping the change this narrow made it explicit that the contribution repaired observability rather than changing fairness semantics.

## What I learned

Warnings are part of a library's user interface. A message should explain the actual branch condition closely enough that a user can diagnose the input without reading source code. Tests for warning text are valuable when wording encodes materially different causes, not merely editorial preference.

## Evidence

- [Merged PR #805](https://github.com/koaning/scikit-lego/pull/805)
- Final files: `sklego/metrics.py`, `tests/test_metrics/test_equal_opportunity.py`
