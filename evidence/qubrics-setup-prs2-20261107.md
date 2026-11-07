# Setup PR #2 audit cause and verification

PR: https://github.com/qubrics/website-feedback-widget/pull/2

The original head `a1da95f68ad313f1483b098042832e5883c18ced` fails the unchanged CI audit, `bun audit --audit-level=moderate`. [Run 41396133490](https://github.com/qubrics/website-feedback-widget/actions/runs/41396133490) reports 16 vulnerabilities (6 high, 10 moderate): Undici 7.30.0, Vite 8.3.2, Vitest mocker 4.1.11 and source-map-js 1.2.1. The same command reproduced locally with exit 1 on that exact head.

The dependency origin was already corrected by [PR #4](https://github.com/qubrics/website-feedback-widget/pull/4), merged as `51cad1764ac6665a0b46e416595ffe3c28368dfc`. Its patched dependency minimums and lockfile are preserved by merging current main `2b15ed40e9f18957786983e4ea23db7401f83d6a` into this stale PR. No additional dependency, audit-threshold, test or workflow edits were needed. Existing source evidence: `evidence/rebrand-qubrics-897-widget-deps-20261102.md`.

Verification after refresh:

- `bun audit --audit-level=moderate`: exit 0; `No vulnerabilities found (checked 113 packages)`.
- `bun install --frozen-lockfile`: exit 0; no lockfile modification.
- `bun run typecheck`: exit 0.
- `git diff --check`: exit 0.
- Existing Verify workflow supplies unit/build/browser/audit/package checks for the refreshed PR head; its result is reported by the coordinator before merge.

The new PR diff remains the requested agent setup docs plus this evidence. No release, tag or publication. Rollback: normal git revert of the PR merge.
