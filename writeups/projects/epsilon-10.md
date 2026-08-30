# EPSILON #10 — Harden the public decision-lab release

> Project engineering · 22 files · +1,023/−802 · merged August 30, 2026

## Classification

This pull request was merged into a project repository under direct control. It is presented separately from independently reviewed upstream work and is excluded from upstream-repository reach and maintainer-review claims.

## Outcome

[PR #10](https://github.com/DresdenGman/EPSILON-trading-simulator/pull/10) consolidated EPSILON around one canonical public web experience and hardened the release across product behavior, research-state persistence, accessibility, metadata, security headers, and dependency hygiene. The Vercel preview deployment completed successfully after merge.

EPSILON is a quantitative decision lab rather than a trading-advice engine. The product keeps a hypothesis, its test inputs, computed outputs, and provenance visible so that a result can be challenged and retested instead of being presented as an unsupported prediction.

## Investigation and problem framing

The release work began by auditing the public route graph and the boundary between the website and the research services. The repository had accumulated multiple presentation surfaces and state transitions: landing, dashboard, backtest, AI interrogation, download, and legacy routes. The hardening goal was therefore broader than a visual refresh:

- one understandable entry point should lead users into the research loop;
- a guest should not lose a complete research artifact after a reload;
- stale or failed retests must not silently replace the last valid evidence;
- failures, missing provenance, and unsupported assumptions should remain explicit;
- the public release should have a clean dependency and security baseline.

The PR description records this as four concrete objectives: consolidate the canonical web experience, persist guest research artifacts, harden AI error handling/accessibility/metadata/security headers, and upgrade dependencies while removing known npm audit findings.

## Design and implementation

The final diff touched 22 files across the Next.js website and its tests.

### A single public product surface

The landing, dashboard, strategy-lab, interrogation, download, impact, and video surfaces were aligned around the current decision-lab narrative. Legacy routes remain compatible through redirects rather than competing with the canonical experience. This preserves existing links while making the intended research path legible to a first-time visitor.

### Durable, truthful research state

`ResearchContext` was strengthened so that complete guest artifacts survive reloads and remain associated with the current subject and hypothesis. Retesting is treated as a state transition: changing the question makes an earlier result stale, a failed retest cannot erase the last successful artifact, and only a successful retest matching the current experiment becomes current evidence.

### Safer public behavior

The change added explicit AI-route error handling, authentication-error presentation, response metadata, and security headers. The product continues to distinguish a local guest heuristic from a configured server-side model integration; neither is represented as financial advice or as proof of predictive performance.

### Dependency and verification hygiene

The website dependency tree was upgraded and the lockfile was regenerated. New and updated tests cover AI-route behavior, download-surface truthfulness, backtest form behavior, route configuration, and presentation invariants. The resulting release remains inspectable from the repository, release record, and Vercel preview.

## Debugging and iteration

The work required checking more than whether the page rendered. The important iteration was to follow the complete guest journey: enter a hypothesis, run a test, inspect the returned evidence, reload, change the question, retest, and verify that stale and failed states remained visible without corrupting the prior successful artifact. The test additions encode these boundaries so later UI changes cannot accidentally turn a research record into an opaque “latest result.”

The same discipline was applied to the public surface: route convergence was tested, metadata and security configuration were made explicit, and the dependency update was accepted only after the production build and audit checks were clean.

## Validation and final result

The merged PR reports the following evidence:

- 75 Vitest tests passed;
- the TypeScript check passed;
- the production build passed;
- `npm audit` reported 0 vulnerabilities;
- the online mobile guest journey was verified on the Vercel preview;
- the post-merge Vercel deployment was ready.

These checks demonstrate release integrity and state-management behavior. They do not claim profitability, statistical significance, historical-market validation, or general strategy robustness.

## Lessons learned

The main lesson is that a quantitative product earns trust through boundaries as much as through features. A polished interface is not enough if it loses the experiment, hides provenance, or lets a failed retest overwrite a valid result. The release therefore treats persistence, stale-state handling, error paths, metadata, and security configuration as part of the research method itself.

This project line complements the upstream portfolio: upstream PRs demonstrate work reviewed by independent maintainers, while EPSILON demonstrates end-to-end ownership of a scientific product—from hypothesis framing and data boundaries to user-facing validation and deployment.

## Evidence

- [Merged PR #10](https://github.com/DresdenGman/EPSILON-trading-simulator/pull/10)
- [EPSILON repository](https://github.com/DresdenGman/EPSILON-trading-simulator)
- [Live decision lab](https://epsilon-livid.vercel.app/landing)
