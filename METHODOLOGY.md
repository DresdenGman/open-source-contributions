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

For the October 9, 2026 (America/Los_Angeles) snapshot, the 14 unique upstream repositories total **253,097 stars**. They have 13 distinct owners: 11 GitHub organizations and 2 individual accounts. Owners and organizations are not interchangeable counts.

The curated writeup index contains 25 records: 20 upstream and 5 selected project-engineering entries. Separately, the public `author:DresdenGman is:pr is:merged` search returns 46 records: 20 upstream and 26 in repositories controlled by DresdenGman or dresdengoehner. The historical GERT #2 writeup is retained in the curated project subset; it is not added to that authored-search total. See `data/github-merge-audit.json` for the complete authored-search inventory and repository-star snapshot.

GitHub timestamps remain UTC in JSON. The newest merge is October 10 at 00:27:08 UTC, equivalent to October 9 at 17:27:08 PDT. Existing historical table dates retain their source dates; the newest local-date row is labeled PT.

## Status definitions

| Status | Meaning |
| --- | --- |
| Merged | GitHub records a non-null merge timestamp |
| Active | Open or draft PR still under development or review |
| Fork only | Code published on a personal fork without an upstream PR |
| Local patch | Unsubmitted local work; neither an upstream PR nor a merge |
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
