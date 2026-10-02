<!-- markdownlint-disable -->

# Hardening Report: cpcloud--flake-update-action/v1.0.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cpcloud--flake-update-action/v1.0.3** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a) violation: The `run:` block directly interpolates the caller-controlled expression `${{ inputs.dependency }}` into a shell command string. An attacker who controls the `dependency` input can inject arbitrary shell commands (e.g., via semicolons, backticks, or `$(...)` subshells). The offending line is: `run: nix flake lock --update-input ${{ inputs.dependency }}`. Fix: move the value into an `env:` variable and double-quote it in the shell, e.g. `env: { DEP: "${{ inputs.dependency }}" }` then `run: nix flake lock --update-input "$DEP"`.

Locations:

- `action.yml:52`

### unpinned-uses (severity: high)

All five `uses:` references in action.yml are pinned to mutable version tags rather than immutable 40-character SHA commit hashes, making the action vulnerable to supply-chain attacks if any of those tags are moved or overwritten. Failing references:
- `uses: cpcloud/flake-dep-info-action@v2.0.10` (line 46)
- `uses: cpcloud/flake-dep-info-action@v2.0.10` (line 56)
- `uses: cpcloud/compare-commits-action@v5.0.27` (line 61)
- `uses: peter-evans/create-pull-request@v3` (line 75)
- `uses: peter-evans/enable-pull-request-automerge@v1` (line 89)

Locations:

- `action.yml:46`
- `action.yml:56`
- `action.yml:61`
- `action.yml:75`
- `action.yml:89`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.dependency }}" appears directly in run: block of step "Update ${{ inputs.dependency }}"; move to env: map

Locations:

- `action.yml:53`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection, unpinned-uses

**Notes:**

Fixed all three findings in action.yml: (1) Script injection: moved `${{ inputs.dependency }}` from the `run:` shell string into an `env:` block as `DEPENDENCY`, then referenced it as `"$DEPENDENCY"` in the nix command. (2) Pinned all five `uses:` references to immutable 40-character commit SHAs: cpcloud/flake-dep-info-action@v2.0.10 → 6817d58e7ac2c6e435c25d533469c16018858c4f, cpcloud/compare-commits-action@v5.0.27 → 92437d53c25093bfc3a7fa5355cd2625aa85cc7b, peter-evans/create-pull-request@v3 → 18f7dc018cc2cd597073088f7c7591b9d1c02672, peter-evans/enable-pull-request-automerge@v1 → 21d45e1c52f5d111d2019b5d33f953ed2e735c46. Original tags preserved as inline comments.

