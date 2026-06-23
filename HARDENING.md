<!-- markdownlint-disable -->

# Hardening Report: cpcloud--flake-update-action/v1.0.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **cpcloud--flake-update-action/v1.0.2** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A ${{ }} expression is directly interpolated inside a run: shell command. The step 'Update ${{ inputs.dependency }}' contains: `run: nix flake lock --update-input ${{ inputs.dependency }}`. The `inputs.dependency` value is caller-controlled and is injected directly into the shell command string before the shell ever sees it, enabling arbitrary command injection.

Locations:

- `action.yml:50`

### unpinned-uses (severity: high)

All 5 uses: references in action.yml are pinned to mutable version tags rather than immutable 40-character SHA commit hashes, making the action vulnerable to supply-chain attacks if any of those tags are moved or compromised. Failing references: `cpcloud/flake-dep-info-action@v1.1.0` (used twice, lines 44 and 54), `cpcloud/compare-commits-action@v5.0.10` (line 59), `peter-evans/create-pull-request@v3` (line 68), `peter-evans/enable-pull-request-automerge@v1` (line 80).

Locations:

- `action.yml:44`
- `action.yml:54`
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

Fixed all three findings in action.yml: (1) Script injection: moved `${{ inputs.dependency }}` out of the `run:` shell command into an `env:` block as DEPENDENCY, referenced as "$DEPENDENCY" in the shell. (2) Pinned all 5 unpinned action references to full 40-character SHA hashes with original tags preserved as comments: cpcloud/flake-dep-info-action@v1.1.0 → c030a6fd3878b08f3c7b1632b96484a13eaf4dd0, cpcloud/compare-commits-action@v5.0.10 → 2ab0d2acc6c5c58840da6bc522ae87b3e3ccf6af, peter-evans/create-pull-request@v3 → 18f7dc018cc2cd597073088f7c7591b9d1c02672, peter-evans/enable-pull-request-automerge@v1 → 21d45e1c52f5d111d2019b5d33f953ed2e735c46.

