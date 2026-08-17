<!-- markdownlint-disable -->

# Hardening Report: cpcloud--flake-update-action/v1.0.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cpcloud--flake-update-action/v1.0.2** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The `run:` block directly interpolates the attacker-controlled expression `${{ inputs.dependency }}` into a shell command string. The command `nix flake lock --update-input ${{ inputs.dependency }}` substitutes the input value before the shell ever sees it, enabling arbitrary command injection. An attacker who controls the `dependency` input can inject shell metacharacters (e.g. `;`, `|`, `$(...)`) to execute arbitrary commands. Fix: move the value into an env var and quote it — e.g. `env: DEPENDENCY: ${{ inputs.dependency }}` then `run: nix flake lock --update-input "$DEPENDENCY"`.

Locations:

- `action.yml:46`

### unpinned-uses (severity: high)

All five `uses:` references in action.yml are pinned to mutable version tags rather than immutable 40-character commit SHAs. If any of these upstream repositories is compromised or a tag is moved, malicious code could be injected into this action's execution. Failing references:
- `cpcloud/flake-dep-info-action@v1.1.0` (lines 42 and 51)
- `cpcloud/compare-commits-action@v5.0.10` (line 55)
- `peter-evans/create-pull-request@v3` (line 66)
- `peter-evans/enable-pull-request-automerge@v1` (line 80)
Each should be replaced with the full 40-character SHA digest of the intended commit, e.g. `uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `action.yml:42`
- `action.yml:51`
- `action.yml:55`
- `action.yml:66`
- `action.yml:80`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.dependency }}" appears directly in run: block of step "Update ${{ inputs.dependency }}"; move to env: map

Locations:

- `action.yml:53`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection, unpinned-uses

**Notes:**

Fixed all three findings in action.yml: (1) Script injection on line 46 — moved `${{ inputs.dependency }}` into an `env:` block as `DEPENDENCY` and referenced it as `"$DEPENDENCY"` in the shell command, eliminating direct expression interpolation in the run block. (2) Static-inline-injection on line 53 was the same issue, resolved by the same fix. (3) All five unpinned `uses:` references were pinned to their full 40-character commit SHAs: cpcloud/flake-dep-info-action@v1.1.0 → c030a6fd3878b08f3c7b1632b96484a13eaf4dd0, cpcloud/compare-commits-action@v5.0.10 → 2ab0d2acc6c5c58840da6bc522ae87b3e3ccf6af, peter-evans/create-pull-request@v3 → 18f7dc018cc2cd597073088f7c7591b9d1c02672, peter-evans/enable-pull-request-automerge@v1 → 21d45e1c52f5d111d2019b5d33f953ed2e735c46. Original tags are preserved as inline comments.

