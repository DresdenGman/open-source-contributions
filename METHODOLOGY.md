# Methodology and Evidence Policy

## Inclusion rules

A contribution receives a full writeup only when its pull request is merged. Open, draft, closed-unmerged, duplicate, or comment-only lines may appear in the active pipeline but are not counted as completed outcomes.

The portfolio separates:

1. **Upstream contributions** — merged into repositories not controlled by the contributor and reviewed through the project's normal process.
2. **Project engineering** — merged into repositories under direct control. These demonstrate implementation work but are excluded from upstream reach and independent-review claims.

## Evidence hierarchy

Claims are grounded in public sources, in this order:

1. merged GitHub pull request and final diff;
2. maintainer reviews and approvals;
3. GitHub Actions or other reported validation;
4. linked issue and repository documentation;
5. local development notes, used only when consistent with public evidence.

Every writeup links to its merged PR. Exact file and line counts are taken from GitHub's merged pull-request record.

## Star-count methodology

Repository stars are contextual reach, not patch-level popularity.

- Stars are measured from the current GitHub repository record on the stated snapshot date.
- A repository is counted once even if it contains multiple merged PRs.
- Project repositories under direct control are excluded from upstream reach.
- Values naturally change over time; the snapshot date prevents false precision.
- The portfolio never claims that a contribution earned or owns the repository's stars.

For the August 13, 2026 snapshot, the 13 unique upstream repositories total **244,818 stars**.

## Status definitions

| Status | Meaning |
| --- | --- |
| Merged | GitHub records a non-null merge timestamp |
| Active | Open or draft PR still under development or review |
| Shelved | Closed without merge, duplicate, obsolete, or intentionally stopped |
| Watching | Issue or discussion monitored before implementation |

## Writeup structure

Canonical writeups describe:

- problem and user impact;
- investigation and root cause;
- design and implementation;
- debugging or review-driven iteration;
- validation and final outcome;
- lessons learned;
- primary evidence.

The narrative distinguishes the initial proposal from the final merged design. Reviewer-authored changes are credited rather than represented as solely contributor-authored work.

## AI-assistance policy

AI tools may assist with discovery, code navigation, drafting, mechanical edits, or test execution when permitted by the upstream project. Repository-specific disclosure rules take precedence. Public writeups do not contain private prompts, credentials, email, or unpublished conversations. The contributor remains responsible for validating and submitting every represented contribution.
