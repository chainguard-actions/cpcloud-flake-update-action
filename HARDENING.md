<!-- markdownlint-disable -->

# Hardening Report: cpcloud--flake-update-action/v1.0.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cpcloud--flake-update-action/v1.0.4** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A GitHub Actions expression is interpolated directly inside a run: shell command string. The step 'Update ${{ inputs.dependency }}' runs: `nix flake lock --update-input ${{ inputs.dependency }}`. The value of `inputs.dependency` is controlled by the caller and is substituted into the shell command before the shell ever sees it, enabling arbitrary command injection (e.g., a dependency name containing `;`, `$(...)`, or other shell metacharacters).

Locations:

- `action.yml:46`

### unpinned-uses (severity: high)

Multiple uses: references are pinned to mutable version tags rather than immutable 40-character commit SHAs, making the action vulnerable to supply-chain attacks if any referenced tag is moved or the upstream repository is compromised.

In action.yml:
- cpcloud/flake-dep-info-action@v2.0.10 (×2)
- cpcloud/compare-commits-action@v5.0.27
- peter-evans/create-pull-request@v3
- peter-evans/enable-pull-request-automerge@v1

In .github/workflows/ci.yml:
- actions/checkout@v2 (×2)
- cachix/install-nix-action@v16 (×2)
- tibdex/github-app-token@v1 (×2)
- actions/setup-node@v2
- cycjimmy/semantic-release-action@v2.7.0

In .github/workflows/auto-rebase.yml:
- tibdex/github-app-token@v1
- Label305/AutoRebase@v0.1

Locations:

- `action.yml:43`
- `action.yml:51`
- `action.yml:55`
- `action.yml:68`
- `action.yml:82`
- `.github/workflows/ci.yml:22`
- `.github/workflows/ci.yml:24`
- `.github/workflows/ci.yml:37`
- `.github/workflows/ci.yml:39`
- `.github/workflows/ci.yml:45`
- `.github/workflows/ci.yml:62`
- `.github/workflows/ci.yml:70`
- `.github/workflows/ci.yml:75`
- `.github/workflows/auto-rebase.yml:17`
- `.github/workflows/auto-rebase.yml:22`

### missing-permissions (severity: medium)

Neither workflow file defines a top-level permissions: key, and no individual job within either file defines a job-level permissions: key. This means all jobs run with the default GitHub Actions permissions, which include write access to repository contents and other sensitive scopes. Explicit minimal permissions should be declared.

Locations:

- `.github/workflows/ci.yml:1`
- `.github/workflows/auto-rebase.yml:1`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.dependency }}" appears directly in run: block of step "Update ${{ inputs.dependency }}"; move to env: map

Locations:

- `action.yml:53`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all 4 findings across 3 files:

1. action.yml - script-injection/static-inline-injection: Moved `${{ inputs.dependency }}` from the `run:` shell command into an `env:` block as `DEPENDENCY`, referenced as `"$DEPENDENCY"` in the shell script.

2. action.yml - unpinned-uses: Pinned all 5 action references to full commit SHAs (cpcloud/flake-dep-info-action×2, cpcloud/compare-commits-action, peter-evans/create-pull-request, peter-evans/enable-pull-request-automerge).

3. .github/workflows/ci.yml - unpinned-uses: Pinned all 7 action references (actions/checkout×3, cachix/install-nix-action×2, tibdex/github-app-token×2, actions/setup-node, cycjimmy/semantic-release-action). Added top-level `permissions: contents: read` and job-level permissions (contents: write + pull-requests: write for flake-update and release jobs; contents: read for get-flakes).

4. .github/workflows/auto-rebase.yml - unpinned-uses + missing-permissions: Pinned tibdex/github-app-token and Label305/AutoRebase. Added top-level `permissions: contents: read` and job-level `contents: write, pull-requests: write` for the auto-rebase job.

