<!-- markdownlint-disable -->

# Hardening Report: cpcloud--flake-update-action/v2.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cpcloud--flake-update-action/v2.0.0** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A ${{ }} expression is directly interpolated into a run: shell command. In action.yml, the step 'Update ${{ inputs.dependency }}' contains: `run: nix flake update ${{ inputs.dependency }}`. The value of `inputs.dependency` is controlled by the calling workflow and is substituted directly into the shell command before the shell parses it, enabling command injection (e.g., a dependency name containing shell metacharacters or newlines).

Locations:

- `action.yml:44`

### unpinned-uses (severity: high)

Multiple uses: references across action.yml and workflow files use mutable tags instead of full 40-character SHA digests, making them vulnerable to supply-chain attacks if the referenced tag is moved or the repository is compromised. Failing references include:
- action.yml: cpcloud/flake-dep-info-action@v2.0.10 (×2), cpcloud/compare-commits-action@v5.0.28, peter-evans/create-pull-request@v5, peter-evans/enable-pull-request-automerge@v3
- .github/workflows/auto-rebase.yml: tibdex/github-app-token@v2, Label305/AutoRebase@v0.1
- .github/workflows/ci.yml: actions/checkout@v4 (×3), cachix/install-nix-action@v24 (×2), tibdex/github-app-token@v2 (×2), actions/setup-node@v4, cycjimmy/semantic-release-action@v4.0.0

Locations:

- `action.yml:42`
- `action.yml:49`
- `action.yml:53`
- `action.yml:62`
- `action.yml:76`
- `.github/workflows/auto-rebase.yml:15`
- `.github/workflows/auto-rebase.yml:21`
- `.github/workflows/ci.yml:22`
- `.github/workflows/ci.yml:24`
- `.github/workflows/ci.yml:37`
- `.github/workflows/ci.yml:40`
- `.github/workflows/ci.yml:44`
- `.github/workflows/ci.yml:55`
- `.github/workflows/ci.yml:63`
- `.github/workflows/ci.yml:72`
- `.github/workflows/ci.yml:80`

### missing-permissions (severity: medium)

Neither .github/workflows/auto-rebase.yml nor .github/workflows/ci.yml declares a top-level `permissions:` key, and no individual job within either file declares its own `permissions:` block. Without explicit permissions, workflows run with the default repository token permissions, which may be overly broad (write access to contents, pull-requests, etc.).

Locations:

- `.github/workflows/auto-rebase.yml:1`
- `.github/workflows/ci.yml:1`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.dependency }}" appears directly in run: block of step "Update ${{ inputs.dependency }}"; move to env: map

Locations:

- `action.yml:53`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all four findings:
1. script-injection/static-inline-injection: Moved `${{ inputs.dependency }}` from the run: shell command into the step's env: block as DEPENDENCY, referenced as "$DEPENDENCY" in the shell.
2. unpinned-uses: Pinned all 10 unique action references across action.yml, auto-rebase.yml, and ci.yml to full 40-character SHA digests with tag comments for readability.
3. missing-permissions: Added `permissions: {}` at the top level of both workflow files, with minimal job-level permissions (contents: read for get-flakes; contents: write + pull-requests: write for flake-update and auto-rebase; contents: write + pull-requests: write + issues: write for release).

