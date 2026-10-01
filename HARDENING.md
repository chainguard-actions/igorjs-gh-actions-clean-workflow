<!-- markdownlint-disable -->

# Hardening Report: igorjs--gh-actions-clean-workflow/v7.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **igorjs--gh-actions-clean-workflow/v7.0.1** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a): Multiple `run:` blocks in the composite action directly interpolate `${{ steps.*.outputs[...] }}` expressions into shell commands. Although `steps.*.outputs` values are produced by the action itself, any `${{ ... }}` expression inside a `run:` block is subject to YAML template substitution before the shell sees it, making it a script-injection risk. Affected steps: 'Display metrics (basic)' (e.g. `echo "Total runs found: ${{ steps.basic.outputs['total-runs-found'] }}"`), 'Display filtered metrics' (e.g. `echo "Total runs found: ${{ steps.filter.outputs['total-runs-found'] }}"`), 'Display keep-many metrics' (e.g. `echo "Total runs found: ${{ steps.keep-many.outputs['total-runs-found'] }}"`), 'Display delete-all metrics' (e.g. `echo "Total runs found: ${{ steps.delete-all.outputs['total-runs-found'] }}"`), 'Display short retention metrics' (e.g. `echo "Total runs found: ${{ steps.short.outputs['total-runs-found'] }}"`), and 'Display combined metrics' (e.g. `echo "Total runs found: ${{ steps.combined.outputs['total-runs-found'] }}"`). Fix: move the output values into `env:` variables and reference them as `"$ENV_VAR"` in the shell script.

Locations:

- `.github/actions/integration-test-suite/action.yml:24`
- `.github/actions/integration-test-suite/action.yml:46`
- `.github/actions/integration-test-suite/action.yml:64`
- `.github/actions/integration-test-suite/action.yml:79`
- `.github/actions/integration-test-suite/action.yml:95`
- `.github/actions/integration-test-suite/action.yml:113`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed all six 'Display metrics' steps in hardened/action/.github/actions/integration-test-suite/action.yml. Each step's ${{ steps.*.outputs['...'] }} expressions were moved into an env: block with descriptive variable names (e.g. BASIC_TOTAL_RUNS_FOUND, FILTER_RUNS_DELETED, etc.), and the run: shell scripts were updated to reference those environment variables with plain $VAR_NAME syntax. No ${{ }} expressions remain inside any run: block.

