# Argo CD #29173 — Direct config-management-plugin link

> Documentation integrity · 1 file · +1/−1 · merged August 13, 2026

## Outcome

[PR #29173](https://github.com/argoproj/argo-cd/pull/29173) replaced an obsolete, version-specific documentation URL with the canonical repository-relative config-management-plugin page. Maintainer `nitishfy` approved the change and merged it into Argo CD.

## Problem and investigation

The Kustomize guide directed readers to create a custom plugin, but its link first landed on a moved-page notice. I checked the current source rather than treating the issue's proposed replacement as authoritative. The same page already used `../operator-manual/config-management-plugins.md`, and the target file existed in the repository. That established both the canonical destination and the local linking convention.

Before implementation, I also confirmed that the issue was unassigned and had no competing pull request. The result was a deliberately narrow one-line correction rather than a broader documentation cleanup.

## Implementation and validation

The absolute `stable/user-guide` URL was replaced by the relative operator-manual path. A relative link is more resilient across preview, stable, and release documentation builds. The signed commit followed Argo's DCO requirement, and the PR disclosed AI assistance in issue discovery and workflow checking.

Local checks confirmed the target existed, the obsolete URL disappeared from the edited page, and the diff had no whitespace errors. Upstream DCO, documentation preview, title, generated-code, security, and workflow checks passed.

## What I learned

Small documentation fixes still require engineering judgment: the durable answer came from source conventions, not merely from exchanging one URL for another. Policy compliance, collision checking, and a minimal diff made a fast review possible.

## Evidence

- [Issue #29172](https://github.com/argoproj/argo-cd/issues/29172)
- [Merged PR #29173](https://github.com/argoproj/argo-cd/pull/29173)
- Final scope: `docs/user-guide/kustomize.md`, +1/−1
