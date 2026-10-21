# Methodology and Evidence Policy

## Inclusion rules

A contribution receives a full writeup only when its pull request is merged. Open, draft, closed-unmerged, duplicate, or comment-only lines may appear in the active pipeline but are not counted as completed outcomes.

The portfolio separates:

1. **Upstream engineering** — code, tests, documentation or CI changes merged into repositories not controlled by the contributor through the project's normal process.
2. **Curated-resource listings** — accepted recommendations of the contributor's projects in independently maintained directories. These establish inclusion, not software validation or adoption, and are counted separately from engineering.
3. **Project engineering** — merged into repositories under direct control. These demonstrate implementation work but are excluded from upstream reach and independent-review claims.

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

## Current count and historical star snapshot

The contribution status audit is dated **October 21, 2026 (UTC)**. It verifies 84 public authored PR records: **48 merged, 9 open, and 27 closed without merge**. The merged total separates **21 upstream engineering PRs, 1 curated-resource listing, and 26 PRs in contributor-controlled repositories**.

The engineering contributions span 14 repositories and 13 owners: 11 GitHub organizations and 2 individual accounts. Including the separately categorized awesome-quant listing gives 22 independent-repository merges across 15 repositories and 14 owners. Owners and organizations are not interchangeable counts.

The curated writeup index contains 27 records: 21 upstream engineering, 1 resource-listing evidence record, and 5 selected project-engineering entries. The historical GERT #2 writeup is retained in the curated project subset; it is not added to the DresdenGman authored-search total.

The repository-star values are retained from **October 9, 2026 (America/Los_Angeles)**, when the 14 engineering recipient repositories totaled **253,097 stars**. They were not remeasured for this update. No star total is claimed for the expanded set including awesome-quant. Each JSON file identifies the star snapshot separately from the contribution-status date.

GitHub merge timestamps remain UTC in JSON, and the merged contribution table uses UTC dates. For example, SALib #693 merged October 10 at 00:27:08 UTC, equivalent to October 9 at 17:27:08 PDT. The new StatsForecast #1241 merge is October 20 at 23:02:22 UTC.

The audit uses authored-PR search followed by each returned PR's direct metadata. Search coverage is limited to the connected account's visible GitHub records. Open-PR status and available head CI were read, but no software tests or benchmarks were rerun for this documentation update. An empty workflow result does not establish a passing check, and historical workflow failures are not described as newly introduced failures. See [the audit data](data/github-merge-audit.json).

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
