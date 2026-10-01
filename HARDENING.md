<!-- markdownlint-disable -->

# Hardening Report: cpcloud--flake-update-action/v1.0.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cpcloud--flake-update-action/v1.0.4** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a) violation: A ${{ }} expression is interpolated directly inside a `run:` shell command. The step `run: nix flake lock --update-input ${{ inputs.dependency }}` injects the caller-controlled `inputs.dependency` value directly into the shell command string before the shell ever sees it. An attacker who controls this input can inject arbitrary shell commands (e.g., by supplying a value like `foo; malicious-command`). Fix: move the value into an `env:` variable and double-quote it in the script: `env:\n  DEPENDENCY: ${{ inputs.dependency }}\nrun: nix flake lock --update-input "$DEPENDENCY"`

Locations:

- `action.yml:47`

### unpinned-uses (severity: high)

All 5 `uses:` references in action.yml are pinned to mutable version tags rather than immutable 40-character commit SHAs. If any of these upstream actions is compromised or the tag is moved, malicious code will run in all workflows that use this action. Failing references:
- `cpcloud/flake-dep-info-action@v2.0.10` (line 43)
- `cpcloud/flake-dep-info-action@v2.0.10` (line 51)
- `cpcloud/compare-commits-action@v5.0.27` (line 54)
- `peter-evans/create-pull-request@v3` (line 67)
- `peter-evans/enable-pull-request-automerge@v1` (line 82)
Fix: replace each tag with the full 40-character commit SHA, e.g. `uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v2.0.10`.

Locations:

- `action.yml:43`
- `action.yml:51`
- `action.yml:54`
- `action.yml:67`
- `action.yml:82`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.dependency }}" appears directly in run: block of step "Update ${{ inputs.dependency }}"; move to env: map

Locations:

- `action.yml:53`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, static-inline-injection

**Notes:**

Fixed all three findings in action.yml:
1. script-injection / static-inline-injection: Moved `${{ inputs.dependency }}` from the `run:` shell command into an `env:` block as `DEPENDENCY: ${{ inputs.dependency }}`, then referenced it as `"$DEPENDENCY"` in the shell script to prevent shell injection.
2. unpinned-uses: Pinned all 5 `uses:` references to full 40-character commit SHAs:
   - cpcloud/flake-dep-info-action@v2.0.10 → @6817d58e7ac2c6e435c25d533469c16018858c4f (used twice)
   - cpcloud/compare-commits-action@v5.0.27 → @92437d53c25093bfc3a7fa5355cd2625aa85cc7b
   - peter-evans/create-pull-request@v3 → @18f7dc018cc2cd597073088f7c7591b9d1c02672
   - peter-evans/enable-pull-request-automerge@v1 → @21d45e1c52f5d111d2019b5d33f953ed2e735c46

