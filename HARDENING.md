<!-- markdownlint-disable -->

# Hardening Report: cpcloud--flake-update-action/v1.0.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cpcloud--flake-update-action/v1.0.4** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The `run:` block directly interpolates the attacker-controlled expression `${{ inputs.dependency }}` into a shell command string without routing it through an env var or quoting it. An attacker who controls the `dependency` input can inject arbitrary shell commands. Offending line: `run: nix flake lock --update-input ${{ inputs.dependency }}`

Locations:

- `action.yml:47`

### unpinned-uses (severity: high)

All five `uses:` references in action.yml are pinned to mutable version tags rather than immutable 40-character commit SHAs. A compromised or malicious tag update could silently alter the action's behaviour. Unpinned references: `cpcloud/flake-dep-info-action@v2.0.10` (×2), `cpcloud/compare-commits-action@v5.0.27`, `peter-evans/create-pull-request@v3`, `peter-evans/enable-pull-request-automerge@v1`.

Locations:

- `action.yml:43`
- `action.yml:55`
- `action.yml:62`
- `action.yml:73`
- `action.yml:85`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.dependency }}" appears directly in run: block of step "Update ${{ inputs.dependency }}"; move to env: map

Locations:

- `action.yml:53`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection, unpinned-uses

**Notes:**

Fixed all three findings in action.yml: (1) Script injection: moved `${{ inputs.dependency }}` out of the `run:` shell string into an `env:` block as `DEPENDENCY`, then referenced it as `"$DEPENDENCY"` in the shell command. (2) Pinned all 5 unpinned `uses:` references to their full 40-character commit SHAs (cpcloud/flake-dep-info-action@6817d58e7ac2c6e435c25d533469c16018858c4f ×2, cpcloud/compare-commits-action@92437d53c25093bfc3a7fa5355cd2625aa85cc7b, peter-evans/create-pull-request@18f7dc018cc2cd597073088f7c7591b9d1c02672, peter-evans/enable-pull-request-automerge@21d45e1c52f5d111d2019b5d33f953ed2e735c46), preserving the original tag as a comment.

