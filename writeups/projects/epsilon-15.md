# EPSILON #15 — Make public evidence metrics verifiable

> Project engineering · 22 files · +618/−86 · merged September 2, 2026

## Classification

This pull request was merged into a project repository under direct control. It is presented separately from independently reviewed upstream work and is excluded from upstream-repository reach and maintainer-review claims.

## Outcome

[PR #15](https://github.com/DresdenGman/EPSILON-trading-simulator/pull/15) tightened the boundary between a completed historical experiment and an unverified browser event. The public impact record now counts an experiment only after the server completes a real-data run; client-authored telemetry cannot manufacture a verified result.

The change also made evidence artifacts easier to audit: stable evidence identifiers remain usable across the product, while checksums cover the complete exported artifact rather than a partial representation.

## Investigation and design

The central problem was not merely input validation. A public research product needs a defensible answer to three questions:

- Which events represent completed computational work?
- Which fields can an untrusted client claim?
- Can a downloaded result be traced back to the exact artifact that was evaluated?

The implementation separates verified server-side experiments from ordinary browser signals, rejects forged `verified_historical_run` events, bounds telemetry and evidence request bodies, and adds storage write budgets and retention cleanup. This keeps engagement analytics from being mistaken for research evidence.

## Validation

The merged PR records the following checks:

- `npm ci` completed with 0 reported vulnerabilities;
- `npm run check` passed 11 tests, TypeScript, and oxlint;
- the production build passed;
- a production Massive-SPY historical run returned an `epsilon.evidence.v2` artifact;
- a forged `verified_historical_run` request returned HTTP 400;
- both Vercel status checks completed successfully before merge.

These results validate the evidence boundary and release mechanics. They do not establish investment performance or generalize a backtest beyond its stated data and assumptions.

## Application relevance

This milestone connects the project’s quantitative work to a broader reliability theme: evidence is useful only when its provenance, trust boundary, and failure conditions are explicit. It complements independently reviewed upstream contributions by demonstrating end-to-end ownership of a deployed research system, while remaining clearly labeled as project engineering.

## Evidence

- [Merged PR #15](https://github.com/DresdenGman/EPSILON-trading-simulator/pull/15)
- [EPSILON repository](https://github.com/DresdenGman/EPSILON-trading-simulator)
- [Public decision lab](https://epsilonfield.space)
