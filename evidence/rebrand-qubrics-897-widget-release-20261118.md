# Widget 0.1.4 release preparation

Goal: release the corrected GitHub links in `@sendmux/feedback-widget@0.1.4` for [qubrics/qubrics#897](https://github.com/qubrics/qubrics/issues/897). Operator decision 3A on 18 November 2026 approves the single-advisory exception below, green CI, merge of PR #5 and the existing tag-triggered widget publication path. No service deployment or other publication is included.

Fixed review baseline: `eb5ca064f64b798225592f755579917435809fb4` (`main` before preparation). This PR changes `package.json` version to `0.1.4`, its matching changelog entry, this evidence and the same narrow audit exception in PR/release CI. Package identity, runtime source, tests and dependencies remain unchanged.

## Observed publication gap

On 2026-11-18, registry latest and the actual `@sendmux/feedback-widget@0.1.3` tarball both contained:

- Homepage: `https://github.com/Sendmux/website-feedback-widget#readme`.
- Source: `git+https://github.com/Sendmux/website-feedback-widget.git`.
- Issues: `https://github.com/Sendmux/website-feedback-widget/issues`.

The current source has `qubrics` in all three fields (`package.json:8-14`), merged by [PR #3](https://github.com/qubrics/website-feedback-widget/pull/3). Registry versions were `0.1.0`, `0.1.2`, `0.1.3`; GitHub tags ended at `v0.1.3`. Version `0.1.4` and tag `v0.1.4` were unused. Reusing `0.1.3` would skip publication under `.github/workflows/publish.yml:77-79`.

Read-only proof:

```sh
curl -fsSL https://registry.npmjs.org/@sendmux%2ffeedback-widget/latest | jq '{name,version,homepage,repository,bugs}'
curl -fsSL https://registry.npmjs.org/@sendmux/feedback-widget/-/feedback-widget-0.1.3.tgz | tar -xzOf - package/package.json | jq '{name,version,homepage,repository,bugs}'
```

## Whole release delta since v0.1.3

The new artifact builds all merged changes, not only this PR. `git diff v0.1.3..eb5ca064f64b798225592f755579917435809fb4 -- src tests` is empty: no runtime feature, widget source or test changes.

Already-merged changes included in this release:

- Corrected package GitHub metadata and README raw-asset links to the `qubrics` owner.
- Moved contributor scripts, lockfile, browser-test server command and CI/release workflows from pnpm to Bun `1.4.2`; pinned third-party Actions to immutable revisions.
- Updated development dependencies: Playwright `^1.60.0` to `^1.63.0`, Node types `^25.9.1` to `^25.9.9`, Vite `^8.0.14` to `^8.3.5`, Vitest `^4.1.7` to `^4.1.12`; added `source-map-js ^1.2.2` and `undici ^7.30.1` overrides. These are the [PR #4](https://github.com/qubrics/website-feedback-widget/pull/4) dependency prerequisite and earlier merged build-tool changes, not edits in this PR.
- Added agent issue/domain documentation, local-instruction ignore and repository evidence. These are excluded from the package's explicit `files` list.

The distributable is rebuilt with the updated tooling. Unchanged source does not promise byte-identical JavaScript. Package exports and output filenames remain unchanged.

## Verification and review

Initial exact-head CI [44251062131](https://github.com/qubrics/website-feedback-widget/actions/runs/44251062131) passed typecheck, all 13 unit tests, build and all eight browser tests, then failed audit on `picomatch@4.0.7`; package dry-run was skipped. The existing lock resolves this package through Vite, Vitest and tinyglobby. [GHSA-fc5q-39xc-vjpf](https://github.com/advisories/GHSA-fc5q-39xc-vjpf) covers `<=4.0.7` and currently names no patched version. All 35 published versions are affected; latest Vite/Vitest retain the dependency. It belongs to the developer build/test tree, not the widget's local-only runtime import graph. Removing it breaks mandatory tooling imports; replacing both frameworks would require a build/test migration.

Operator 3A permits **only** `--ignore GHSA-fc5q-39xc-vjpf` beside the unchanged `--audit-level=moderate` in both audit steps. [Tracking issue #6](https://github.com/qubrics/website-feedback-widget/issues/6), cited beside each command, requires removal once a patched dependency makes the unfiltered audit pass. No other ignored IDs, production-only audit, development exclusion, severity change or shell failure suppression is added. Canonical option: [Bun 1.4.2 audit documentation](https://github.com/oven-sh/bun/blob/bun-v1.4.2/docs/pm/cli/audit.mdx), `--ignore <id>`; Bun's JSON report remains unfiltered, with the option affecting the exit code.

Local command `bun audit --audit-level=moderate --ignore GHSA-fc5q-39xc-vjpf` exits 0: `checked 113 packages, 1 ignored`. The JSON variant also exits 0 and retains the sole high-severity GHSA in its unfiltered report. The original unfiltered audit exit1 and exact-head CI failure above are the red receipt; no dependency or lockfile changed between those inputs and the approved exception. Logs: control-plane MAIN `.claude/artifacts/rebrand-qubrics-897/widget-audit-exception-{green-20261118.log,json-20261118.json}`. Ordinary CI must still pass every original build/test/package step before merge/release.

- Minimal local checks: parse manifest, assert `0.1.4` and canonical URL fields, confirm matching changelog version, `git diff --check`, and compare runtime source/tests against the prior release.
- Existing native CI verifies typecheck, unit tests, build, browser tests, dependency audit and `npm pack --dry-run` for the exact PR head. No local full-suite duplicate is required; the prior baseline CI [42322953483](https://github.com/qubrics/website-feedback-widget/actions/runs/42322953483) was already successful.
- No new test: this PR introduces no runtime logic. Existing package/build/browser checks cover packaging failure modes.
- Two-axis review compares `git diff eb5ca064f64b798225592f755579917435809fb4...HEAD`; Standards and Spec findings are kept separate in the task release packet.
- Changelog copy passed the fresh-reader polish critique. One humanize pass retained the factual line, followed by protected-token verification (`0.1.4`, `2026-11-18`, `qubrics/website-feedback-widget`). Mechanical count: zero em dashes, semicolons, curly quotes, banned words or comparative/negation pivots.

## Publication and undo boundary

Main-branch pushes run verification only. Only a matching `v*.*.*` tag push enters `.github/workflows/publish.yml`, which rebuilds and rechecks before publishing to npm and creating a GitHub release. Under operator 3A, pin the green merged release commit before creating and pushing `v0.1.4`; no tag is created by this source PR itself.

Before publication, an ordinary PR reverting this source preparation restores the previous version metadata. After publication, `0.1.4` is immutable: moving `latest` back to `0.1.3` affects unpinned consumers only; explicit `0.1.4` installs remain. A corrective patch release would be required to replace content. Removing a Git tag or GitHub release does not retract the npm artifact.

Status at preparation: source release candidate, not published. Unrelated brand/package renames and marketplace changes remain outside this PR.
