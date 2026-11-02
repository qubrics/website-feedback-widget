# Widget dependency audit prerequisite

Authorised by Ops Watch for qubrics/qubrics#897: separate dependency remediation PR, merge on green, then rebase website-feedback-widget PR #3. Base: `3ef30f906050a7ec0a098b4a41b3aa7c1cfe83e2`. This change contains no A15 repository-link edits, public/source changes, release tag, package version bump or publication/deployment.

## Root cause and correction

The existing frozen lockfile resolves vulnerable development dependencies. The exact CI command, `bun audit --audit-level=moderate`, reproduced 16 advisories (6 high, 10 moderate), exit 1:

- `vitest@4.1.11` pins vulnerable `@vitest/mocker@4.1.11`; Vitest 4.1.12 resolves patched mocker 4.1.12.
- `vite@8.3.2` falls in its reported advisory ranges; compatible Vite 8.3.5 is outside those ranges.
- `jsdom -> undici@7.30.0` is below the fixed 7.30.1 version.
- Vite/PostCSS and jsdom/css-tree resolve source-map-js 1.2.1, below fixed 1.2.2.

Canonical commands: [Bun audit](https://bun.com/docs/pm/cli/audit), [compatible update](https://bun.com/docs/pm/cli/update) and [transitive overrides](https://bun.com/docs/pm/overrides). Native `bun update --lockfile-only` refreshed allowed dependency resolutions and Vite/Vitest minimums without crossing majors. Installed Bun 1.4.2 also wrote files during its dry-run; the subsequent normal command reported no additional changes. An unrelated Playwright refresh was rolled back with Bun, preserving its original version and range.

The first refreshed audit retained one high advisory: source-map-js 1.2.1 survived via the lockfile's css-tree exact edge, alongside patched 1.2.2. Registry and installed css-tree manifests declare `^1.2.1`, but the locked edge still says `1.2.1`. The existing root `overrides` pattern now sets source-map-js `^1.2.2` across both paths and raises Undici's existing minimum to `^7.30.1`; native `bun install --lockfile-only` generates the resulting lockfile. This replaces vulnerable installed versions, with no audit ignores, exception, threshold or CI change.

## Verification

- Before: `bun audit --audit-level=moderate` exit 1, 16 advisories.
- After final dependency resolution: same command exit 0, `No vulnerabilities found (checked 113 packages)`.
- `bun install --frozen-lockfile`: exit 0 against the final lockfile.
- `bun run typecheck`: exit 0.
- `git diff --check`: exit 0. Playwright remains 1.63.0; package version remains 0.1.3.
- Existing required CI will run unit tests, build, browser tests, the unchanged audit and package dry-run before merge. No new harness or tests added; heavy/build/browser checks stay in CI.

Raw receipts: control-plane MAIN `.claude/artifacts/rebrand-qubrics-897/widget-deps-{audit-red,audit-green,audit-final-green,fix-dry-run,update-dry-run,update,retain-playwright,patched-lock,final-install,typecheck,tree}.log`. The intermediate `audit-green.log` name is a failed first attempt, explicitly retained; only `audit-final-green.log` proves the final clean signal. Source docs and resolver evidence remain alongside them.

No merge, deployment or publication is claimed by this pre-CI evidence. Independent review, exact-head CI and final GitHub readback are recorded in the coordinator's task handoff. Rollback for the source change is a normal revert; no production state changes.
