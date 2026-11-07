# GitHub live references — website feedback widget

Part of qubrics/qubrics#897, spec A15. Source-only PR; no merge, package publication, deployment or GitHub settings change.

Package homepage/repository/issues metadata and README raw-asset URLs now point at `qubrics/website-feedback-widget`. Package/version/artifact names, product copy and service hosts remain unchanged. Local-only AGENTS.md is excluded from this commit; operator explicitly authorised this task's commit, push and ready PR.

Local checks passed with the unchanged bun.lock:

- `bun install --frozen-lockfile`.
- `bun run typecheck`.
- `bun run test`: 4 files, 13 tests passed.
- `git diff --check` and live old-owner/repository reference scan.

Local logs: control-plane MAIN `.claude/artifacts/rebrand-qubrics-897/widget-{install,check}.log`.

Existing PR CI owns build/browser/audit/package checks. Tag-driven publication remains unexecuted. Independent review is coordinated across the batch; no review-settled claim is made here.
