<!-- markdownlint-disable -->

# Hardening Report: igorjs--gh-actions-clean-workflow/v7.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **igorjs--gh-actions-clean-workflow/v7.0.1** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Multiple run: blocks in the composite action directly interpolate ${{ steps.*.outputs[...] }} expressions into shell commands without routing through env: variables. For example: `echo "Total runs found: ${{ steps.basic.outputs['total-runs-found'] }}"`. The steps.*.outputs context values flow through YAML template substitution before the shell processes them, allowing shell metacharacters to be injected. All six display-metrics run: blocks are affected.

Locations:

- `.github/actions/integration-test-suite/action.yml:23`
- `.github/actions/integration-test-suite/action.yml:52`
- `.github/actions/integration-test-suite/action.yml:68`
- `.github/actions/integration-test-suite/action.yml:81`
- `.github/actions/integration-test-suite/action.yml:94`
- `.github/actions/integration-test-suite/action.yml:108`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed all six display-metrics run: blocks in .github/actions/integration-test-suite/action.yml. Each ${{ steps.*.outputs['...'] }} expression was moved from the run: shell string into the step's env: block, and the shell script now references the corresponding plain environment variable (e.g. $TOTAL_RUNS_FOUND, $RUNS_DELETED, etc.). This eliminates the YAML template substitution attack surface where shell metacharacters in step output values could have been injected into the shell command.

