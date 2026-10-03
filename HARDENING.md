<!-- markdownlint-disable -->

# Hardening Report: apache--skywalking-eyes/v0.9.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **apache--skywalking-eyes/v0.9.0** was hardened automatically. 7 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Direct expression interpolation in run: block. In action.yml, the run: command directly interpolates ${{ inputs.log }}, ${{ inputs.config }}, and ${{ inputs.mode }} into the shell command string: `license-eye -v ${{ inputs.log }} -c ${{ inputs.config }} header ${{ inputs.mode }}`. An attacker-controlled input value can inject arbitrary shell commands. The fixed header/action.yml shows the correct pattern: route inputs through env vars and double-quote them.

Locations:

- `action.yml:57`

### script-injection (severity: high)

Sub-rule (a): Direct expression interpolation in run: block. In dependency/action.yml, the run: command directly interpolates ${{ inputs.log }}, ${{ inputs.config }}, ${{ inputs.mode }}, and ${{ inputs.flags }} into the shell command string: `license-eye -v ${{ inputs.log }} -c ${{ inputs.config }} dependency ${{ inputs.mode }} ${{ inputs.flags }}`. An attacker-controlled input value can inject arbitrary shell commands.

Locations:

- `dependency/action.yml:56`

### unpinned-uses (severity: high)

action.yml references actions/setup-go@v6, which is a mutable tag rather than a pinned 40-character commit SHA. This is vulnerable to supply-chain attacks if the tag is moved to a malicious commit. The correct pattern (shown in header/action.yml) is: actions/setup-go@4a3601121dd01d1626a1e23e37211e3254c1c06c # v6

Locations:

- `action.yml:48`

### unpinned-uses (severity: high)

dependency/action.yml references actions/setup-go@v6, which is a mutable tag rather than a pinned 40-character commit SHA. This is vulnerable to supply-chain attacks if the tag is moved to a malicious commit.

Locations:

- `dependency/action.yml:47`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.log }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:58`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.config }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:58`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.mode }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:58`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection, unpinned-uses

**Notes:**

Fixed all findings in action.yml and dependency/action.yml:
1. Pinned actions/setup-go@v6 → @924ae3a1cded613372ab5595356fb5720e22ba16 # v6 in both files.
2. In action.yml: moved inputs.log, inputs.config, inputs.mode into env: block as INPUT_LOG, INPUT_CONFIG, INPUT_MODE and referenced them as double-quoted shell variables in the run: command.
3. In dependency/action.yml: moved inputs.log, inputs.config, inputs.mode, inputs.flags into env: block. For inputs.flags (a list-style extra-flags input), used the xargs-based tokenization pattern with a bash array to safely expand multiple flags while preserving argument boundaries and preventing injection.

