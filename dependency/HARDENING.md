<!-- markdownlint-disable -->

# Hardening Report: apache--skywalking-eyes--dependency/v0.9.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **apache--skywalking-eyes--dependency/v0.9.0** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Multiple `${{ inputs.* }}` expressions are interpolated directly inside a `run:` shell command string. Specifically, `${{ inputs.log }}`, `${{ inputs.config }}`, `${{ inputs.mode }}`, and `${{ inputs.flags }}` are all embedded verbatim in the shell command `license-eye -v ${{ inputs.log }} -c ${{ inputs.config }} dependency ${{ inputs.mode }} ${{ inputs.flags }}`. Because these values are substituted by the Actions template engine before the shell parses the command, a caller can inject arbitrary shell metacharacters (e.g. via `inputs.flags`) to execute arbitrary commands. These inputs must be moved to `env:` variables and referenced as double-quoted shell variables (e.g. `"$LOG"`) instead.

Locations:

- `action.yml:54`

### unpinned-uses (severity: high)

The step `uses: actions/setup-go@v6` references a mutable tag (`@v6`) rather than a full 40-character commit SHA. A tag can be moved to point to a different (potentially malicious) commit at any time, making this a supply-chain risk. It should be pinned to a specific SHA, e.g. `actions/setup-go@0aaccfd150d50ccf4b7ff52e8d6c7ac8e8b0b558 # v6`.

Locations:

- `action.yml:45`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.log }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:56`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.config }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:56`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.mode }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:56`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.flags }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:56`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, static-inline-injection

**Notes:**

1. Pinned actions/setup-go@v6 to full SHA 924ae3a1cded613372ab5595356fb5720e22ba16 (# v6). 2. Moved all four ${{ inputs.* }} expressions (log, config, mode, flags) to the step's env: block as INPUT_LOG, INPUT_CONFIG, INPUT_MODE, INPUT_FLAGS. Single-value inputs (log, config, mode) are referenced as double-quoted shell variables. The list-style flags input is tokenized via xargs with a NUL-delimited read loop (guarded for empty values) to correctly handle quoted arguments without injection risk.

