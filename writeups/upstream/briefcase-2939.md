# Briefcase #2939 — GitHub Actions hardening with zizmor

> CI/CD security · 8 files · +78/−38 · merged July 29, 2026

## Outcome

[PR #2939](https://github.com/beeware/briefcase/pull/2939) pinned GitHub Actions to full commit SHAs, reduced credential persistence, established read-only default permissions, and enabled zizmor in pre-commit. Maintainer `freakboy3742` approved the result after multiple rounds of CI debugging and scope correction.

## Problem and threat model

The workflows used mutable action tags and checkout defaults that retained credentials. They also lacked consistent top-level permissions. These patterns increase supply-chain and token-exposure risk. The task was not simply to silence a scanner: each finding needed a fix compatible with Briefcase's reusable and caller workflows.

## Investigation and iteration

I pinned 27 action references to immutable SHAs, added `persist-credentials: false` where checkout did not need to push, and introduced `permissions: contents: read` at appropriate workflow boundaries. An early attempt used an empty permission set too broadly, which blocked legitimate job behavior. Comparing the failure with the project's related workflow work clarified that read-only contents access was the correct least-privilege baseline.

The first review also pointed out that adding configuration without actually enabling the pre-commit hook did not satisfy the issue. I revised `.pre-commit-config.yaml`, fixed every reported finding, and added the required changenote. The final contribution spanned six workflows, pre-commit configuration, and release notes across twelve commits.

## Validation and lessons

Pre-commit and upstream CI exposed formatting, pinning, permissions, and workflow-routing problems during development. The final merged state satisfied zizmor and the repository's checks.

This work demonstrated that security tooling should guide a threat-model review, not become a checkbox exercise. Least privilege must still preserve required behavior, reusable workflow permissions need to be understood at call boundaries, and an installed scanner has no value unless contributors actually run it. The PR transparently disclosed AI assistance under BeeWare's contribution policy.

## Evidence

- [Issue #2937](https://github.com/beeware/briefcase/issues/2937)
- [Merged PR #2939](https://github.com/beeware/briefcase/pull/2939)
- Final scope: six workflow files, `.pre-commit-config.yaml`, changenote
