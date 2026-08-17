<!-- markdownlint-disable -->

# Hardening Report: cpcloud--flake-update-action/v2.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cpcloud--flake-update-action/v2.0.1** was hardened automatically. 7 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Direct expression interpolation of `inputs.dependency` inside a `run:` shell command. The line `run: nix flake lock --update-input ${{ inputs.dependency }}` passes the caller-controlled input directly into the shell command string before the shell ever sees it, enabling command injection. The value should be passed via an `env:` variable and double-quoted in the script.

Locations:

- `action.yml:47`

### unpinned-uses (severity: high)

All `uses:` references in action.yml are pinned to mutable version tags rather than immutable full 40-character SHA commit hashes, making the action vulnerable to supply-chain attacks if any upstream tag is moved or overwritten. Failing references: `cpcloud/flake-dep-info-action@v2.0.11` (×2), `cpcloud/compare-commits-action@v5.0.37`, `peter-evans/create-pull-request@v5`, `peter-evans/enable-pull-request-automerge@v3`.

Locations:

- `action.yml:43`
- `action.yml:51`
- `action.yml:57`
- `action.yml:68`
- `action.yml:82`

### unpinned-uses (severity: high)

All `uses:` references in ci.yml are pinned to mutable version tags rather than immutable full 40-character SHA commit hashes. Failing references: `actions/checkout@v4` (×3), `cachix/install-nix-action@v24` (×2), `tibdex/github-app-token@v2` (×2), `actions/setup-node@v4`, `cycjimmy/semantic-release-action@v4.0.0`.

Locations:

- `.github/workflows/ci.yml:22`
- `.github/workflows/ci.yml:23`
- `.github/workflows/ci.yml:37`
- `.github/workflows/ci.yml:41`
- `.github/workflows/ci.yml:49`
- `.github/workflows/ci.yml:57`
- `.github/workflows/ci.yml:68`
- `.github/workflows/ci.yml:76`
- `.github/workflows/ci.yml:83`

### unpinned-uses (severity: high)

All `uses:` references in auto-rebase.yml are pinned to mutable version tags rather than immutable full 40-character SHA commit hashes. Failing references: `tibdex/github-app-token@v2`, `Label305/AutoRebase@v0.1`.

Locations:

- `.github/workflows/auto-rebase.yml:16`
- `.github/workflows/auto-rebase.yml:21`

### missing-permissions (severity: medium)

The workflow file ci.yml has no top-level `permissions:` key and no job-level `permissions:` key on any of its jobs (get-flakes, flake-update, release). Without explicit permissions, the workflow runs with the default (potentially broad) GITHUB_TOKEN permissions, violating the principle of least privilege.

Locations:

- `.github/workflows/ci.yml:1`

### missing-permissions (severity: medium)

The workflow file auto-rebase.yml has no top-level `permissions:` key and no job-level `permissions:` key on its auto-rebase job. Without explicit permissions, the workflow runs with the default (potentially broad) GITHUB_TOKEN permissions.

Locations:

- `.github/workflows/auto-rebase.yml:1`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.dependency }}" appears directly in run: block of step "Update ${{ inputs.dependency }}"; move to env: map

Locations:

- `action.yml:53`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions, static-inline-injection

**Notes:**

Fixed all 7 findings across action.yml, ci.yml, and auto-rebase.yml:

1. script-injection / static-inline-injection (action.yml): Moved `${{ inputs.dependency }}` out of the `run:` shell command into an `env:` block as `DEPENDENCY`, referenced as `"$DEPENDENCY"` in the shell script.

2. unpinned-uses (action.yml): Pinned all 5 action references to full 40-char commit SHAs with tag comments: cpcloud/flake-dep-info-action@9897b26... (×2), cpcloud/compare-commits-action@fad8cf3..., peter-evans/create-pull-request@4e1beaa..., peter-evans/enable-pull-request-automerge@a660677...

3. unpinned-uses (ci.yml): Pinned all 9 action references: actions/checkout@11d5960... (×3), cachix/install-nix-action@7ac1ec2... (×2), tibdex/github-app-token@3beb63f... (×2), actions/setup-node@49933ea..., cycjimmy/semantic-release-action@61680d0...

4. unpinned-uses (auto-rebase.yml): Pinned both action references: tibdex/github-app-token@3beb63f..., Label305/AutoRebase@e2bf9fd...

5. missing-permissions (ci.yml): Added top-level `permissions: {}`, job-level `contents: read` for get-flakes and flake-update, and `contents: write` + `pull-requests: write` for release.

6. missing-permissions (auto-rebase.yml): Added top-level `permissions: {}` and job-level `contents: write` + `pull-requests: write` for auto-rebase.

