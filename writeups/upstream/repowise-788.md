# Repowise #788 — Architectural decisions in Codex SessionStart

> Developer tooling · 2 files · +64/−15 · merged July 14, 2026

## Outcome

[PR #788](https://github.com/repowise-dev/repowise/pull/788) connected Repowise's standing architectural decisions to the Codex `SessionStart` hook, bringing the Codex integration in line with context already delivered to other coding agents.

## Problem and investigation

Repowise already had two relevant capabilities: relevance-ranked standing decisions and a Codex augmentation hook. The missing link was delivery. Codex sessions received the generic MCP usage context but not the decisions that were supposed to follow a user across agents and sessions.

I traced the hook payload through the augment command and identified where the existing `_session_decision_block()` could be composed without duplicating ranking logic. The design reused the established decision pipeline rather than introducing a Codex-specific store.

## Implementation

The change extracted the repeated MCP instructions into a module-level constant, appended the ranked decision block during `SessionStart`, and passed the hook's `session_id` into scoring. The latter was important: omitting it would have produced context, but not context ranked with the correct session signal.

The final diff touched the Codex integration and its command dispatch, with two commits. Maintainer `RaghavChamadiya` approved the result.

## What I learned

Cross-agent continuity is mainly a data-flow problem. It is not enough to persist decisions; the integration must deliver the right subset at the lifecycle boundary where a new agent session begins. Reusing ranking and passing session identity kept the feature consistent with the existing architecture.

## Evidence

- [Issue #786](https://github.com/repowise-dev/repowise/issues/786)
- [Merged PR #788](https://github.com/repowise-dev/repowise/pull/788)
- Final scope: Codex augment command and SessionStart dispatch
