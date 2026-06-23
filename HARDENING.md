<!-- markdownlint-disable -->

# Hardening Report: cpcloud--flake-update-action/v2.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **cpcloud--flake-update-action/v2.0.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A ${{ }} expression is interpolated directly inside a run: shell command. The step 'Update ${{ inputs.dependency }}' contains: `run: nix flake update ${{ inputs.dependency }}`. The value of inputs.dependency is controlled by the caller and is substituted directly into the shell command before execution, enabling command injection. The fix is to pass the value via an env: variable and quote it: `env:\n  DEPENDENCY: ${{ inputs.dependency }}\nrun: nix flake update "$DEPENDENCY"`.

Locations:

- `action.yml:52`

### unpinned-uses (severity: high)

All uses: references in action.yml use mutable version tags instead of immutable full 40-character SHA commit digests, making the action vulnerable to supply-chain attacks if any referenced action is compromised or its tag is moved. Unpinned references: cpcloud/flake-dep-info-action@v2.0.10 (lines 46, 56), cpcloud/compare-commits-action@v5.0.28 (line 61), peter-evans/create-pull-request@v5 (line 74), peter-evans/enable-pull-request-automerge@v3 (line 89).

Locations:

- `action.yml:46`
- `action.yml:56`
- `action.yml:61`
- `action.yml:74`
- `action.yml:89`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.dependency }}" appears directly in run: block of step "Update ${{ inputs.dependency }}"; move to env: map

Locations:

- `action.yml:53`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection, unpinned-uses

**Notes:**

Fixed all three findings in action.yml: (1) Moved ${{ inputs.dependency }} out of the run: shell command into an env: block as DEPENDENCY, then referenced it as "$DEPENDENCY" to prevent shell injection. (2) Pinned all four unpinned action references to full 40-character SHA digests: cpcloud/flake-dep-info-action@6817d58e7ac2c6e435c25d533469c16018858c4f (v2.0.10), cpcloud/compare-commits-action@2dce7af01600860922eda90208afc81bd87b9d98 (v5.0.28), peter-evans/create-pull-request@4e1beaa7521e8b457b572c090b25bd3db56bf1c5 (v5), peter-evans/enable-pull-request-automerge@a660677d5469627102a1c1e11409dd063606628d (v3).

