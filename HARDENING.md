<!-- markdownlint-disable -->

# Hardening Report: cpcloud--flake-update-action/v1.0.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **cpcloud--flake-update-action/v1.0.4** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A ${{ }} expression is directly interpolated inside a run: shell command. The step 'Update ${{ inputs.dependency }}' contains: `run: nix flake lock --update-input ${{ inputs.dependency }}`. The `inputs.dependency` value is attacker-controlled and is substituted directly into the shell command before execution, enabling arbitrary command injection. The fix is to pass the value via an env: variable and quote it: `env:\n  DEPENDENCY: ${{ inputs.dependency }}\nrun: nix flake lock --update-input "$DEPENDENCY"`

Locations:

- `action.yml:51`

### unpinned-uses (severity: high)

All 5 uses: references in action.yml are pinned to mutable tags rather than full 40-character SHA commit hashes, making the action vulnerable to supply-chain attacks if any of the referenced actions are compromised or their tags are moved. Failing references: `cpcloud/flake-dep-info-action@v2.0.10` (lines 45, 55), `cpcloud/compare-commits-action@v5.0.27` (line 60), `peter-evans/create-pull-request@v3` (line 73), `peter-evans/enable-pull-request-automerge@v1` (line 87).

Locations:

- `action.yml:45`
- `action.yml:55`
- `action.yml:60`
- `action.yml:73`
- `action.yml:87`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.dependency }}" appears directly in run: block of step "Update ${{ inputs.dependency }}"; move to env: map

Locations:

- `action.yml:53`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection, unpinned-uses

**Notes:**

Fixed all three findings in action.yml: (1) Script injection: moved `${{ inputs.dependency }}` from the `run:` shell command into an `env:` block as `DEPENDENCY`, then referenced it as `"$DEPENDENCY"` in the nix command. (2) Pinned all 5 unpinned `uses:` references to full 40-character SHA hashes with original tags preserved as comments: cpcloud/flake-dep-info-action@v2.0.10 → 6817d58e7ac2c6e435c25d533469c16018858c4f, cpcloud/compare-commits-action@v5.0.27 → 92437d53c25093bfc3a7fa5355cd2625aa85cc7b, peter-evans/create-pull-request@v3 → 18f7dc018cc2cd597073088f7c7591b9d1c02672, peter-evans/enable-pull-request-automerge@v1 → 21d45e1c52f5d111d2019b5d33f953ed2e735c46.

