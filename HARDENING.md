<!-- markdownlint-disable -->

# Hardening Report: cpcloud--flake-update-action/v1.0.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cpcloud--flake-update-action/v1.0.3** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A `${{ inputs.dependency }}` expression is directly interpolated into a `run:` shell command without being routed through an `env:` variable. An attacker who controls the `dependency` input can inject arbitrary shell commands. The offending line is: `run: nix flake lock --update-input ${{ inputs.dependency }}`

Locations:

- `action.yml:47`

### unpinned-uses (severity: high)

All five `uses:` references in action.yml are pinned to mutable version tags rather than immutable 40-character SHA commit hashes, making the action vulnerable to supply-chain attacks if any of those tags are moved or the upstream repositories are compromised. Failing references: `cpcloud/flake-dep-info-action@v2.0.10` (×2), `cpcloud/compare-commits-action@v5.0.27`, `peter-evans/create-pull-request@v3`, `peter-evans/enable-pull-request-automerge@v1`.

Locations:

- `action.yml:44`
- `action.yml:55`
- `action.yml:59`
- `action.yml:71`
- `action.yml:84`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.dependency }}" appears directly in run: block of step "Update ${{ inputs.dependency }}"; move to env: map

Locations:

- `action.yml:53`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection, unpinned-uses

**Notes:**

Fixed all three findings in action.yml: (1) Moved ${{ inputs.dependency }} from the run: shell command into an env: variable (DEPENDENCY) and referenced it as "$DEPENDENCY" to prevent shell injection. (2) Pinned all five uses: references to immutable 40-character commit SHAs: cpcloud/flake-dep-info-action@6817d58e7ac2c6e435c25d533469c16018858c4f (×2), cpcloud/compare-commits-action@92437d53c25093bfc3a7fa5355cd2625aa85cc7b, peter-evans/create-pull-request@18f7dc018cc2cd597073088f7c7591b9d1c02672, peter-evans/enable-pull-request-automerge@21d45e1c52f5d111d2019b5d33f953ed2e735c46, each with the original tag preserved in a comment.

