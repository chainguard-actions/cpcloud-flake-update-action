<!-- markdownlint-disable -->

# Hardening Report: cpcloud--flake-update-action/v1.0.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **cpcloud--flake-update-action/v1.0.3** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The run: block at line 49 directly interpolates ${{ inputs.dependency }} into a shell command string: `run: nix flake lock --update-input ${{ inputs.dependency }}`. The `inputs.dependency` value is attacker-controlled and is substituted directly into the shell command before execution, enabling arbitrary command injection. The value should be passed via an env: variable and properly quoted instead.

Locations:

- `action.yml:49`

### unpinned-uses (severity: high)

All 5 uses: references in action.yml use mutable version tags instead of pinned 40-character SHA commit hashes, making the action vulnerable to supply-chain attacks if any of those tags are moved or compromised. Failing references: `cpcloud/flake-dep-info-action@v2.0.10` (lines 44, 53), `cpcloud/compare-commits-action@v5.0.27` (line 57), `peter-evans/create-pull-request@v3` (line 68), `peter-evans/enable-pull-request-automerge@v1` (line 80).

Locations:

- `action.yml:44`
- `action.yml:53`
- `action.yml:57`
- `action.yml:68`
- `action.yml:80`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.dependency }}" appears directly in run: block of step "Update ${{ inputs.dependency }}"; move to env: map

Locations:

- `action.yml:53`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, static-inline-injection

**Notes:**

Fixed all three findings in action.yml: (1) script-injection and static-inline-injection: moved `${{ inputs.dependency }}` from the `run:` shell command into an `env:` block as `DEPENDENCY`, then referenced it as `"$DEPENDENCY"` in the shell script to prevent command injection. (2) unpinned-uses: pinned all 5 uses: references to full 40-character SHA hashes — cpcloud/flake-dep-info-action@6817d58e7ac2c6e435c25d533469c16018858c4f (v2.0.10), cpcloud/compare-commits-action@92437d53c25093bfc3a7fa5355cd2625aa85cc7b (v5.0.27), peter-evans/create-pull-request@18f7dc018cc2cd597073088f7c7591b9d1c02672 (v3), peter-evans/enable-pull-request-automerge@21d45e1c52f5d111d2019b5d33f953ed2e735c46 (v1) — with original tags preserved as comments.

