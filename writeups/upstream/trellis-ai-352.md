# Trellis AI #352 — Make the `save_experience` schema executable documentation

> MCP contract documentation and regression testing · 2 files · +36/−1 · merged August 27, 2026

## Outcome

[PR #352](https://github.com/ronsse/trellis-ai/pull/352) replaced an underspecified `save_experience` tool description with a valid, minimal Trace payload and a regression test that executes the example embedded in the docstring. Maintainer `ronsse` rebased and merged the contribution after the complete Python 3.11–3.13, lint, type-check, OpenAPI, and CodeQL matrix passed.

The maintainer connected the change to production evidence: `save_experience` was the project's highest-volume write surface on its reference deployment, and malformed payloads were being rejected as whole traces. They specifically highlighted the executable-docstring test as a pattern worth reusing.

## Problem

The MCP tool accepted a structured `Trace`, but its public description did not teach a caller how that structure was nested. A caller had to infer the schema and could easily send plausible-looking fields in the wrong place. Because validation is all-or-nothing, one unknown or misplaced key could discard the entire trace rather than only the offending field.

The important distinction was not merely which field names existed. It was where they belonged:

- `source`, `intent`, and `context` are required at the top level;
- agent, domain, and timing information belongs inside `context`;
- each step needs `step_type` and `name`;
- additional context belongs in top-level `metadata` or `outcome.metrics`, rather than as arbitrary keys.

## Investigation and refinement

I first traced the MCP function into the `Trace` model instead of treating the existing prose as authoritative. That exposed a mismatch between an intuitive flat payload and the actual nested contract. I then checked the neighboring `record_feedback` surface and deliberately left it unchanged because it already documented its `0.0`–`1.0` rating range; broadening the patch would not have solved the same problem.

The first documentation pass was still vulnerable to drift: a JSON example can look convincing while becoming invalid after a model change. I strengthened the patch by making the example itself part of the test contract. The new test obtains the `save_experience` docstring through `inspect`, extracts the JSON after a stable marker, parses it, calls `save_experience`, and verifies that the trace is saved successfully.

That design tests the user-facing artifact rather than duplicating it in a fixture. If the documentation and implementation diverge later, the test fails at the exact boundary an MCP caller depends on.

## Validation and review

Local validation covered the focused MCP suite and static checks:

- `pytest tests/unit/mcp/test_server.py::TestSaveExperience -v` — **8 passed**;
- Ruff checks passed for the changed test and for the production file after excluding pre-existing complexity findings;
- Ruff formatting checks passed for both changed files.

Upstream CI then passed across Python 3.11, 3.12, and 3.13, plus lint, type checking, OpenAPI validation, CodeQL, and Python security analysis.

The maintainer's review added useful real-world context. On the reference deployment, the tool had accepted 406 writes over seven days, while callers were still losing traces to schema guesses. The maintainer also corrected their own initial suggestion about `record_feedback` after checking its current docstring, reinforcing the value of verifying the actual public surface before expanding scope.

## What I learned

Documentation for a structured tool is part of its interface, not an annotation around it. The most reliable version of that documentation is an example that is continuously exercised against the same validation path used in production.

This contribution also sharpened my approach to scope. Reading the underlying model found the real nesting error; checking adjacent tools prevented an unnecessary follow-up; and turning the final example into executable evidence made a small documentation patch carry a stronger maintenance guarantee than a larger prose-only rewrite.

## Evidence

- [Issue #349](https://github.com/ronsse/trellis-ai/issues/349)
- [Merged PR #352](https://github.com/ronsse/trellis-ai/pull/352)
- Merge commit: `1466eb981da6dd1d5be56e50f639924fa19e4f23`
- Merged by `ronsse` on August 27, 2026
- Final scope: `src/trellis/mcp/server.py` and `tests/unit/mcp/test_server.py`
