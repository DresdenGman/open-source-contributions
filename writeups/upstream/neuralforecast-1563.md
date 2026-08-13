# NeuralForecast #1563 — FreDF forecasting loss

> Time-series ML · 2 files · +121/−1 · merged July 4, 2026

## Outcome

[PR #1563](https://github.com/Nixtla/neuralforecast/pull/1563) implemented the FreDF loss, combining time-domain mean squared error with frequency-domain error derived through `torch.fft.rfft`. The feature was approved after review-driven test restructuring.

## Problem and design

Forecasting losses commonly optimize pointwise time-domain error, but that objective can underrepresent periodic structure. FreDF adds a frequency-domain component so training can balance direct value accuracy with spectral behavior. The implementation exposes an `alpha` parameter: `alpha=0` reduces to time-domain MSE, while `alpha=1` isolates the frequency-domain term.

The loss was implemented within NeuralForecast's existing point-loss hierarchy rather than as a standalone training special case. This preserved the library's normal loss API and export mechanism.

## Review iteration

The first revision introduced a new test file. Maintainer `marcopeix` requested that the coverage be placed in the existing PyTorch loss test module instead. I reorganized the tests accordingly and kept the feature focused on the established test architecture. The final PR was approved with thanks.

## Validation and lessons

Regression cases covered the combined result and the two alpha boundaries. Boundary identities are especially useful here because they validate both the weighting logic and each constituent domain independently.

This contribution reinforced two lessons: mathematical implementations should expose testable limiting behavior, and fitting a mature repository's test organization is part of maintainability. A technically valid new file can still be the wrong structural choice upstream.

## Evidence

- [Issue #1301](https://github.com/Nixtla/neuralforecast/issues/1301)
- [Merged PR #1563](https://github.com/Nixtla/neuralforecast/pull/1563)
- Final files: `neuralforecast/losses/pytorch.py`, `tests/test_losses/test_pytorch.py`
