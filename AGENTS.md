# AGENTS.md — thermal-print

TypeScript/pnpm workspace for React-based ESC/POS and PDF thermal-print output. This is the Nuvel-maintained fork. `AGENTS.md` is the sole canonical instruction source; Claude loads it through the tracked SessionStart hook, and client settings must not copy its policies.

## Bootstrap and Git identity

- Resolve this repository with `git rev-parse --show-toplevel`. For shared Nuvel resources, prefer a validated `NUVEL_WORKSPACE_ROOT`; otherwise derive candidates from the common Git directory/original checkout and accept only a directory containing `.claude/workflows/issue-orchestrator.js`. Never assume `../docs` works from a detached worktree.
- **Commit with the machine's GLOBAL git identity, exactly as configured. Never set it, never override it, never "fix" it.** Never write or override `user.name`, `user.email`, signing settings, author/committer/config/email environment variables, or `.git/config`; never disable signing. Derive branch prefixes and paths instead of hardcoding a developer identity or home path. Stop if the configured identity is incompatible.
- Preserve user changes in dirty checkouts. Use focused branches and squash PR merges. Never run version, release, tag, or publish commands without explicit authorization.

## Commands and full checks

Use pnpm 9, matching the Pages workflow, with the repository lockfile; do not substitute npm commands.

```bash
pnpm install --frozen-lockfile
pnpm run build
pnpm run playground:build
pnpm run docs:build
git diff --check
```

Run all commands above before committing. The current default branch has no automated test, lint, or formatter gate: root `pnpm test` is a successful placeholder, so never report it as a passing suite. Package TypeScript builds are the available static check. Printer behavior still requires the manual workflow in [`docs/TESTING.md`](docs/TESTING.md).

Pushes to `main` trigger GitHub Pages build and deployment. Verify any deployment that actually runs.

## Architecture and invariants

- Packages live under `packages/`: shared contracts in `core`, byte generation in `escpos`, React integration in `react`, PDF output in `pdf`, and the `playground` and `docs` consumers. Start at [`docs/README.md`](docs/README.md).
- Extend existing package contracts and renderers before adding parallel abstractions. Keep public types backward-compatible unless a breaking release is explicitly authorized.
- ESC/POS output must preserve printer state, supported encodings, paper-width calculations, row layout, line spacing, and cutting semantics. Consult [`docs/STYLING.md`](docs/STYLING.md), [`docs/CUT_COMMANDS.md`](docs/CUT_COMMANDS.md), and [`docs/TROUBLESHOOTING_CUT.md`](docs/TROUBLESHOOTING_CUT.md) before changing them.
- CP860 is the default encoding for Brazilian Portuguese. Unsupported characters become `?`; preserve byte-level behavior and verify changes on relevant physical printers.
- PDF layout uses measured coordinates and style inheritance. Inspect artifacts through the text/render procedure in [`docs/TESTING.md`](docs/TESTING.md), never by treating the binary as text.
- Keep examples and published docs aligned with public APIs. Read [`docs/USAGE.md`](docs/USAGE.md) and [`docs/USAGE_EXAMPLES.md`](docs/USAGE_EXAMPLES.md) before changing examples.

## Publishing

Packages publish only through explicit `publish:*` scripts; version scripts mutate package versions. GitHub Pages deployment is separate from package publishing. Do not infer release authorization from implementation or merge authorization.
