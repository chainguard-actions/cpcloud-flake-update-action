<!-- markdownlint-disable -->

# Hardening Report: cpcloud--flake-update-action/v1.0.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **cpcloud--flake-update-action/v1.0.3** was hardened automatically. 3 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a) violation: The `run:` block directly interpolates the attacker-controlled expression `${{ inputs.dependency }}` into the shell command string: `nix flake lock --update-input ${{ inputs.dependency }}`. GitHub Actions performs template substitution before the shell parses the command, so a malicious value for `dependency` (e.g. containing `;`, `&&`, `$(...)`, etc.) can inject arbitrary shell commands. Fix: move the value into an `env:` variable and double-quote it in the script, e.g. `env: { DEPENDENCY: "${{ inputs.dependency }}" }` then `run: nix flake lock --update-input "$DEPENDENCY"`.

Locations:

- `action.yml:47`

### unpinned-uses (severity: high)

All five `uses:` references in action.yml are pinned to mutable version tags rather than immutable 40-character SHA commit hashes. A compromised or malicious tag update could silently alter the code executed by this action. Failing references:
- `cpcloud/flake-dep-info-action@v2.0.10` (lines 43 and 51)
- `cpcloud/compare-commits-action@v5.0.27` (line 55)
- `peter-evans/create-pull-request@v3` (line 67)
- `peter-evans/enable-pull-request-automerge@v1` (line 82)
Each should be replaced with the full SHA of the intended commit, e.g. `uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `action.yml:43`
- `action.yml:51`
- `action.yml:55`
- `action.yml:67`
- `action.yml:82`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.dependency }}" appears directly in run: block of step "Update ${{ inputs.dependency }}"; move to env: map

Locations:

- `action.yml:53`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection, unpinned-uses

**Notes:**

Fixed all three findings in action.yml: (1) Moved `${{ inputs.dependency }}` from the `run:` shell string into an `env:` block as `DEPENDENCY`, then referenced it as `"$DEPENDENCY"` to prevent shell injection. (2) Pinned all five `uses:` references to full 40-character commit SHAs with original tags preserved as comments: cpcloud/flake-dep-info-action@v2.0.10 → 6817d58e7ac2c6e435c25d533469c16018858c4f, cpcloud/compare-commits-action@v5.0.27 → 92437d53c25093bfc3a7fa5355cd2625aa85cc7b, peter-evans/create-pull-request@v3 → 18f7dc018cc2cd597073088f7c7591b9d1c02672, peter-evans/enable-pull-request-automerge@v1 → 21d45e1c52f5d111d2019b5d33f953ed2e735c46.

### Iteration 2

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all findings in both workflow files:

auto-rebase.yml:
- Pinned tibdex/github-app-token@v1 → @32691ba7c9e7063bd457bd8f2a5703138591fa58 # v1
- Pinned Label305/AutoRebase@v0.1 → @e2bf9fd616286d8feddcaba773a8f2ea9011c745 # v0.1
- Added top-level `permissions: {}` and job-level permissions (contents: write, pull-requests: write)

ci.yml:
- Pinned actions/checkout@v2 → @0717577d45739eb3c851188b29f50ed6c0b2194e # v2 (×3 occurrences)
- Pinned cachix/install-nix-action@v16 → @d56f3ce9be45c562799280e8a561fbbe8f36de44 # v16 (×2 occurrences)
- Pinned tibdex/github-app-token@v1 → @32691ba7c9e7063bd457bd8f2a5703138591fa58 # v1 (×2 occurrences)
- Pinned actions/setup-node@v2 → @7c12f8017d5436eb855f1ed4399f037a36fbd9e8 # v2
- Pinned cycjimmy/semantic-release-action@v2.7.0 → @5982a02995853159735cb838992248c4f0f16166 # v2.7.0
- Added top-level `permissions: {}` and job-level permissions for each job (get-flakes: contents: read; flake-update: contents: write + pull-requests: write; release: contents: write + pull-requests: write)

