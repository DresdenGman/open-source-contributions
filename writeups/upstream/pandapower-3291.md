# pandapower #3291 — Preserving transformer branches in MATPOWER round trips

> Power-system data conversion · 7 files · +238/−18 · merged November 3, 2026

## Outcome

[PR #3291](https://github.com/e2nIEE/pandapower/pull/3291) fixed the MATPOWER round trip that silently converted the two star-point branches of three-winding transformers into `impedance` elements. On `case14`, exporting with `to_mpc` and re-importing with `from_mpc` went from 5 transformers / 0 impedances to 3 / 2. The grids named in the issue previously lost 2–66 transformers per round trip; all now round-trip with exact element counts. Maintainer `vogt31337` approved and merged the fix.

## Root cause: one convention mismatch, three flattening points

MATPOWER and pandapower use different TAP conventions. In MATPOWER, TAP = 0 means "no transformer" and any other value is the turns ratio (1 = a transformer without a tap changer). In pandapower's internal ppc, TAP defaults to 1 for every branch. Three places flattened that distinction:

- `to_mpc._ppc2mpc` rewrote every TAP == 1 to 0, including genuine transformers.
- `from_mpc._change_ppc_TAP_value` rewrote every TAP = 0 coming from a file back to 1.
- `from_ppc._branch_to_which` (since commit `5c5ab5dc`, v3.0) classified branches with TAP in {0, 1}, no phase shift, and different voltage levels as `impedance`. In v2.14.11 there was no impedance classification in `from_ppc`, which matches the report that the round trip worked in 2.14.10.

The affected star-point branches carry no tap data, so their ppc ratio is exactly 1.0000 and they hit all three paths.

## Fix

- `from_ppc._branch_to_which`: a cross-voltage branch is now a transformer when `(TAP != 0) | (SHIFT != 0)`; only TAP = 0 with no phase shift is an impedance. This keeps the 2024 impedance feature working for the branches it was meant for.
- `from_mpc`: removed `_change_ppc_TAP_value` and its call sites, so the TAP values from the file reach `from_ppc` unchanged.
- `to_mpc._ppc2mpc`: the TAP 1 → 0 rewrite now skips transformer branches, so exported files also label transformers correctly for external tools (the issue reporter saw the same misclassification through an external importer).
- `to_ppc`: branches sourced from `impedance` elements are marked TAP = 0 in the returned ppci, so a ppc round trip cannot reclassify them as ratio-1 transformers. `makeYbus` treats TAP = 0 as 1, so power flow results are unaffected.
- Element membership for both export paths is now resolved in full ppc branch coordinates and projected through the same `branch_is` filter that builds the ppci, so filtered-out branches (out of service or disconnected) cannot shift the masks onto the wrong rows.

One deliberate extra change: `test_from_ppc_simple_against_target` in `test/converter/test_from_ppc.py` was marked `xfail` and now passes with this fix, so the marker is removed in this PR. Leaving a non-strict xfail in place would let a future regression pass silently as XFAIL. The test passes consistently (5 consecutive runs, and within the full converter suite).

## Verification

- `case14`: round trip preserves all 5 transformers with parameters equal (max vk difference 2.3e-13) and power flow vm/va max difference 0.0.
- The grids named in the issue (`case14`, `case30`/`ieee30`, `case57`, `case89pegase`, `case118`, `case145`, `case300`, CIGRE HV) previously lost 2–66 transformers on a round trip; all now round-trip with exact element counts.
- New regression tests cover the original case plus branches filtered out before an impedance or a unity-ratio transformer (out-of-service and disconnected variants), asserting element identity and parameters, not only counts.
- Converter test suite: the only failure is the pre-existing JAO ImportError (an optional dependency not installed), identical to the baseline before this change.

Known limitations are unchanged: a same-voltage, ratio-1, no-shift transformer still round-trips as a line (as in 2.14), and `trafo3w` elements cannot be reconstructed by `from_ppc`.

## What I learned

The visible bug sat at one classification point, but the cause was a convention mismatch spread across the whole conversion pipeline. Fixing only the classifier would have left the exporter mislabeling transformers for external tools and the importer rewriting the file's TAP values before classification ever saw them. Tracing the value through every stage that touches it — export, import, internal conversion — was the actual diagnosis work.

## Evidence

- [Issue #2620](https://github.com/e2nIEE/pandapower/issues/2620)
- [Merged PR #3291](https://github.com/e2nIEE/pandapower/pull/3291)
- Merge commit `242f5bc12acf090830ef64a5199e81c63171a3b8`
