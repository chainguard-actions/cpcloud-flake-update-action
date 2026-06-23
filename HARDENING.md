<!-- markdownlint-disable -->

# Hardening Report: cpcloud--flake-update-action/v2.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **cpcloud--flake-update-action/v2.0.1** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The run: block directly interpolates the attacker-controlled expression `${{ inputs.dependency }}` into a shell command: `run: nix flake lock --update-input ${{ inputs.dependency }}`. An attacker who controls the `dependency` input can inject arbitrary shell commands (e.g., by passing a value containing semicolons, backticks, or other shell metacharacters). The value must be passed via an environment variable and properly quoted instead.

Locations:

- `action.yml:52`

### unpinned-uses (severity: high)

All `uses:` references in action.yml are pinned to mutable version tags rather than immutable 40-character commit SHAs, making the action vulnerable to supply-chain attacks if any of those tags are moved or the upstream repositories are compromised. Failing references: `cpcloud/flake-dep-info-action@v2.0.11` (lines 46, 56), `cpcloud/compare-commits-action@v5.0.37` (line 61), `peter-evans/create-pull-request@v5` (line 72), `peter-evans/enable-pull-request-automerge@v3` (line 84).

Locations:

- `action.yml:46`
- `action.yml:56`
- `action.yml:61`
- `action.yml:72`
- `action.yml:84`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.dependency }}" appears directly in run: block of step "Update ${{ inputs.dependency }}"; move to env: map

Locations:

- `action.yml:53`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection, unpinned-uses

**Notes:**

Fixed all three findings in action.yml: (1) Moved `${{ inputs.dependency }}` from the run: block into an env: variable (DEPENDENCY) and referenced it as "$DEPENDENCY" in the shell command to prevent script injection. (2) Pinned all five uses: references to immutable 40-character commit SHAs: cpcloud/flake-dep-info-action@v2.0.11 → 9897b26be9888c610fbcb0ceb4322f535c2152b9, cpcloud/compare-commits-action@v5.0.37 → fad8cf390aedb7d442e9e9a8ed1a980d90f2f7e5, peter-evans/create-pull-request@v5 → 4e1beaa7521e8b457b572c090b25bd3db56bf1c5, peter-evans/enable-pull-request-automerge@v3 → a660677d5469627102a1c1e11409dd063606628d. Original tags preserved as comments.

