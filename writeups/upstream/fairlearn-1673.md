# Fairlearn #1673 — Clarify adversarial fitted-state checking

> Estimator lifecycle maintenance · 2 files · +6/−6 · merged August 17, 2026

## Outcome

[PR #1673](https://github.com/fairlearn/fairlearn/pull/1673) replaced exception-driven fitted-state probing in Fairlearn's adversarial mitigation base class with a direct check of the estimator's canonical `_is_setup` flag. Maintainer `riedgar-ms` approved the final change, all 38 reported checks passed, and `romanlutz` merged it.

## Problem

`_AdversarialFairness._validate_input` needed to decide whether the estimator was already fitted before initializing its backend. The existing code called `check_is_fitted`, caught `NotFittedError`, and converted the exception into a Boolean. A stale `# TODO check this` signaled that the intent was unclear, while the class already defined `__sklearn_is_fitted__` in terms of `_is_setup`.

The behavior was not visibly broken, but the implementation obscured the estimator lifecycle and imported an exception solely for control flow.

## Investigation and refinement

My first patch proposed checking for `backendEngine_`, because backend initialization appeared to be the practical state that `_validate_input` cared about. That was too implementation-specific: the class already had a canonical fitted flag, `_is_setup`, and `__sklearn_is_fitted__` used it as the source of truth.

The final patch therefore uses `hasattr(self, "_is_setup")`. This preserves the semantics of `check_is_fitted(self)` while making the local decision explicit and removing the unused `NotFittedError` import. A maintainer-authored refinement also added the release-note entry and aligned the probe with the class contract rather than a downstream backend detail.

After upstream changes caused a conflict in the release-note file, I synchronized the branch with `upstream/main` at commit `7737b3c`, resolved the conflict without altering the functional scope, and let the full CI matrix rerun.

## Validation and final result

The original targeted adversarial suite reported **288 passing tests**. The final synchronized commit reported **38 passing GitHub checks**, including Fairlearn's test, lint, documentation, and compatibility jobs. The merged diff remained deliberately small:

- `fairlearn/adversarial/_adversarial_mitigation.py`: remove `NotFittedError`; replace the `try`/`except` probe with the canonical flag check.
- `docs/user_guide/installation_and_version_guide/v0.15.0.rst`: record the maintenance improvement and contributor credit.

## What I learned

A clearer implementation is not automatically a better one unless it checks the right abstraction. `backendEngine_` described one implementation consequence; `_is_setup` expressed the estimator's actual fitted-state contract. Reading `__sklearn_is_fitted__` was the key step that turned a plausible cleanup into a semantically aligned one.

This PR also reinforced that maintenance work includes lifecycle discipline after the code is written: keeping a reviewed branch synchronized, resolving a documentation conflict carefully, and re-establishing CI evidence were necessary parts of getting six changed lines merged.

## Evidence

- [Issue #1657](https://github.com/fairlearn/fairlearn/issues/1657)
- [Merged PR #1673](https://github.com/fairlearn/fairlearn/pull/1673)
- Merge commit: `8daa86ed7282b1dd0a79581a35de8638c4be021e`
- Maintainer approval: `riedgar-ms`, August 12, 2026
- Merge: `romanlutz`, August 17, 2026
