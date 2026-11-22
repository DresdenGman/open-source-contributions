# pandapower #3351 — One colormap name for both the matplotlib and plotly registries

> Plotting and registry compatibility · 7 files · +268/−35 · merged November 18, 2026

## Outcome

[PR #3351](https://github.com/e2nIEE/pandapower/pull/3351) made the `cmap` argument of `pf_res_plotly` accept names from either the matplotlib or the plotly registry. Before the fix, `cmap='algae'` (a plotly-only colorscale) raised a `KeyError` from the matplotlib registry on the line traces, while `cmap='gnuplot'` (matplotlib-only) failed on the bus markers with a plotly `ValueError`. Maintainer `KS-HTK`, who had diagnosed the root cause in the issue, approved the PR — "Looks like a great solution to me" — and merged it.

## Diagnosis

As `KS-HTK` diagnosed in issue [#2567](https://github.com/e2nIEE/pandapower/issues/2567), the same `cmap` argument was interpreted by two different registries: bus markers pass the name straight to plotly, while line and transformer traces looked it up in matplotlib via `get_plotly_cmap`. Only names present in *both* registries worked.

On the issue, maintainer `vogt31337` commented that this could not really be repaired because it comes from plotly. The narrower reading turned out to be the productive one: the `KeyError` itself is raised inside pandapower, not plotly — plotly only lacks native colormaps for *line* objects (plotly/plotly.py#2059), which is exactly why pandapower samples one color per line itself. Sampling a plotly-named colorscale the same way needs no plotly change.

## Fix

`pandapower/plotting/plotly/get_colors.py` is the single choke point, so one `cmap` name now resolves the same way on both paths:

- `get_plotly_cmap`: if the name is not a matplotlib colormap, it is sampled from plotly's own registry (`plotly.colors.get_colorscale` + `sample_colorscale`, so `_r` reversal works too). A name unknown to *both* registries now raises `ValueError: Colormap 'x' is not a known matplotlib or plotly colormap` instead of matplotlib's misleading `KeyError`.
- New exported `get_plotly_colorscale(cmap_name)`: returns what plotly's `colorscale` property accepts — the name unchanged when plotly knows it, otherwise the matplotlib colormap sampled to 11 `[position, color]` stops. Non-string input passes through.
- `traces.py`: the bus-marker colorscale and the branch colorbar now go through `get_plotly_colorscale`. The colorbar change also fixes the silent drop of the line-loading colorbar for matplotlib-only names, which used to be swallowed by a bare `try/except: pass`.
- The `rgb(...)` strings from the matplotlib path are now formatted with integer components: float formatting could emit scientific notation (e.g. `'rgb(15.9,1.5e-05,6.2)'` for `gnuplot`), which plotly rejects as a line color. Same colors up to rounding (max 0.5/255).
- Out-of-range values are clamped to the end colors on the plotly path too (`sample_colorscale` raises `IndexError` outside `[0, 1]`; overloaded lines routinely exceed `climits_load`), and `get_plotly_cmap` no longer requires matplotlib at all for plotly-only names.

| `cmap` | Before | After |
| --- | --- | --- |
| `'algae'` (plotly-only) | `KeyError` (line traces) | works, 188 traces |
| `'algae_r'` (plotly-only, reversed) | `KeyError` | works, 188 traces |
| `'gnuplot'` (matplotlib-only) | `ValueError` (bus markers) | works, 188 traces |
| `'viridis'`, `'jet'` (both) | works | works, unchanged |
| `'not_a_colormap'` | `ValueError` / `KeyError` depending on path | `ValueError` naming both registries |

`pf_res_plotly` and the trace docstrings now say `cmap` is "a matplotlib or plotly colormap". There is no behavior change for names both registries know: they take the same path as before.

## Validation

Nine new tests in `test/plotting/test_simple_plotly.py` (4 end-to-end on `mv_oberrhein`, 5 unit-level): line colors for `'algae'` are checked to equal plotly's own `sample_colorscale` output exactly, and out-of-range and overloaded-line cases are covered. Mutation check: reverting the production fix makes 8 of the 9 new tests fail. The `test/plotting/` suite went from 5 failed / 10 passed to 5 failed / 19 passed — the 5 failures are pre-existing environment gaps on the test machine (`igraph` missing for the three-winding-transformer plot tests, geo dependencies missing for `test_geo.py`), identical before and after. A CHANGELOG entry was added under `[upcoming 4.0.0]`.

## What I learned

When one argument is interpreted by two registries, the fix belongs at the single choke point where the name is resolved, not at each call site — that is what makes the two paths agree permanently instead of patching whichever one a bug report happens to hit. The other lesson is diagnostic: a maintainer's "this can't be fixed" was a statement about the upstream library's scope, and checking where the exception is actually raised — inside pandapower, not plotly — is what turned it into a fixable local bug.

## Evidence

- [Issue #2567](https://github.com/e2nIEE/pandapower/issues/2567)
- [Merged PR #3351](https://github.com/e2nIEE/pandapower/pull/3351)
- Merge commit `5e2a58628504d0376078b2e3dfa6d6349309d4bc`
