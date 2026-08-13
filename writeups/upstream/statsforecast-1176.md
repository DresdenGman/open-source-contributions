# StatsForecast #1176 — PEP 561 typed-package marker

> Packaging interoperability · 1 marker file · merged July 11, 2026

## Outcome

[PR #1176](https://github.com/Nixtla/statsforecast/pull/1176) added `py.typed` to the StatsForecast package so external type checkers can recognize the distributed inline annotations under PEP 561. Maintainer `nasaul` approved the contribution.

## Problem

A Python package may contain annotations yet still be treated as untyped by downstream tools if its distribution does not advertise typing support. The absence of `py.typed` meant consumers could not reliably benefit from StatsForecast's inline type information.

## Implementation and validation

The implementation is intentionally an empty marker file at `python/statsforecast/py.typed`. The meaningful work was packaging validation: I built and inspected both distribution formats and verified that the marker appeared in the source distribution and wheel. No runtime code or API behavior changed.

GitHub reports zero textual additions because the marker is empty, but its presence in the built artifacts changes downstream type-checker behavior. This is why diff line count is an incomplete measure of contribution value.

## What I learned

Packaging metadata is executable interoperability. A source-tree file matters only if the build configuration carries it into published artifacts, so inspecting the sdist and wheel was the essential test. Small packaging changes should be validated from a consumer's perspective, not merely by confirming that a file exists locally.

## Evidence

- [Issue #1121](https://github.com/Nixtla/statsforecast/issues/1121)
- [Merged PR #1176](https://github.com/Nixtla/statsforecast/pull/1176)
- Verified artifact paths: sdist and wheel `statsforecast/py.typed`
