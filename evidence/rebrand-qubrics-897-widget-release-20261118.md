# Widget 0.1.4 release preparation

Goal: prepare the source release that carries corrected GitHub links into the next npm artifact for [qubrics/qubrics#897](https://github.com/qubrics/qubrics/issues/897). Tagging, release creation, npm publication and deployment remain held for the operator's separate publication decision.

Fixed review baseline: `eb5ca064f64b798225592f755579917435809fb4` (`main` before preparation). This PR changes only `package.json` version to `0.1.4`, its matching changelog entry and this evidence. It preserves package identity, source, tests, dependencies and workflow configuration.

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

- Minimal local checks: parse manifest, assert `0.1.4` and canonical URL fields, confirm matching changelog version, `git diff --check`, and compare runtime source/tests against the prior release.
- Existing native CI verifies typecheck, unit tests, build, browser tests, dependency audit and `npm pack --dry-run` for the exact PR head. No local full-suite duplicate is required; the prior baseline CI [42322953483](https://github.com/qubrics/website-feedback-widget/actions/runs/42322953483) was already successful.
- No new test: this PR introduces no runtime logic. Existing package/build/browser checks cover packaging failure modes.
- Two-axis review compares `git diff eb5ca064f64b798225592f755579917435809fb4...HEAD`; Standards and Spec findings are kept separate in the task release packet.
- Changelog copy passed the fresh-reader polish critique. One humanize pass retained the factual line, followed by protected-token verification (`0.1.4`, `2026-11-18`, `qubrics/website-feedback-widget`). Mechanical count: zero em dashes, semicolons, curly quotes, banned words or comparative/negation pivots.

## Publication and undo boundary

Main-branch pushes run verification only. Only a matching `v*.*.*` tag push enters `.github/workflows/publish.yml`, which rebuilds and rechecks before publishing to npm and creating a GitHub release. The final release packet must pin the merged release commit before proposing the tag command. No tag is created by this source PR.

Before publication, an ordinary PR reverting this source preparation restores the previous version metadata. After publication, `0.1.4` is immutable: moving `latest` back to `0.1.3` affects unpinned consumers only; explicit `0.1.4` installs remain. A corrective patch release would be required to replace content. Removing a Git tag or GitHub release does not retract the npm artifact.

Status at preparation: source release candidate, not published. Unrelated brand/package renames and marketplace changes remain outside this PR.
