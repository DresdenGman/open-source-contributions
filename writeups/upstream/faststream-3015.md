# FastStream #3015 — Explicit Redis stream declaration

> Redis lifecycle control · 4 files · +55/−1 · merged August 13, 2026

## Outcome

[PR #3015](https://github.com/ag2ai/faststream/pull/3015) added `StreamSub(declare: bool = True)`, allowing a Redis consumer group to require an externally managed stream instead of always creating a missing stream. Maintainer `IvanKirpichnikov` approved and merged the contribution.

## Problem and investigation

FastStream created Redis consumer groups with `XGROUP CREATE ... MKSTREAM` unconditionally. That default is convenient, but it can hide deployment mistakes when another system owns stream provisioning. I traced the behavior from the public `StreamSub` schema to the hard-coded `mkstream=True` argument in the subscriber startup path.

The design needed to preserve existing behavior and positional callers. I therefore appended `declare: bool = True` to the constructor, stored it on the schema, and forwarded it as `mkstream=stream.declare`. When `declare=False` is supplied without a consumer group, FastStream now warns because the setting has no effect in that configuration.

## Testing and iteration

The tests cover behavior rather than only implementation wiring. The compatible default verifies that starting a grouped subscriber creates a missing stream. The opt-out case expects Redis `ResponseError` and confirms the missing stream remains absent. A configuration test verifies the no-group warning.

The minimal local checkout supported Ruff, compilation, schema checks, and whitespace validation but not the connected Redis environment. The final upstream matrix supplied that evidence: Python 3.10–3.14, Windows, macOS, real and smoke Redis tests, the broader broker suites, analysis, CodeQL, CLA, and project checks passed. An earlier Windows failure was superseded by the successful final run.

## What I learned

Small configuration flags require API-design discipline: default value, parameter placement, ineffective combinations, and system-boundary tests all matter. The strongest regression test was the observable Redis state—whether a stream exists—not merely whether an internal function received a Boolean.

## Evidence

- [Issue #3013](https://github.com/ag2ai/faststream/issues/3013)
- [Merged PR #3015](https://github.com/ag2ai/faststream/pull/3015)
- Final scope: 4 files, +55/−1, one commit
- Approved and merged by `IvanKirpichnikov`
