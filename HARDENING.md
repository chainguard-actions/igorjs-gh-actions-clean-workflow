<!-- markdownlint-disable -->

# Hardening Report: igorjs--gh-actions-clean-workflow/v8

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **igorjs--gh-actions-clean-workflow/v8** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a) violation: The 'Fail if checks don't pass' run: block in cla-dco.yml directly interpolates ${{ steps.dco.outputs.dco_pass }} and ${{ steps.cla.outputs.cla_pass }} into shell commands. These step outputs are workflow-controllable values that flow through YAML template substitution before the shell sees them, enabling command injection. The offending lines are:
  DCO="${{ steps.dco.outputs.dco_pass }}"
  CLA="${{ steps.cla.outputs.cla_pass }}"
Fix: move the values into env: vars and reference them as "$DCO" / "$CLA" (which is already done for the comparison, but the assignment itself still uses direct interpolation).

Locations:

- `.github/workflows/cla-dco.yml:263`

### script-injection (severity: high)

Rule (a) violation: Multiple 'Display metrics' run: blocks in the integration-test-suite composite action directly interpolate ${{ steps.*.outputs.* }} expressions into echo shell commands. For example:
  echo "Total runs found: ${{ steps.basic.outputs['total-runs-found'] }}"
  echo "Runs to delete: ${{ steps.basic.outputs['runs-deleted'] }}"
This pattern appears in all six 'Display metrics' steps (basic, filtered, keep-many, delete-all, short, combined). Step outputs are workflow-controllable and flow through YAML template substitution before the shell executes them. Fix: route outputs through env: vars (as the 'Verify metrics' steps already do correctly) and reference them as quoted shell variables.

Locations:

- `.github/actions/integration-test-suite/action.yml:22`
- `.github/actions/integration-test-suite/action.yml:57`
- `.github/actions/integration-test-suite/action.yml:88`
- `.github/actions/integration-test-suite/action.yml:117`
- `.github/actions/integration-test-suite/action.yml:148`
- `.github/actions/integration-test-suite/action.yml:183`

### broad-permissions (severity: medium)

The scorecard.yml workflow sets `permissions: read-all` at the top level. This grants read access to all scopes across the entire workflow rather than using minimal specific permissions. The job-level permissions block does narrow the grants for the single job, but the top-level read-all is still a broad-permissions finding. Replace `permissions: read-all` with a minimal top-level block (e.g., `permissions: {}`) and rely solely on the job-level specific permissions.

Locations:

- `.github/workflows/scorecard.yml:20`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, broad-permissions

**Notes:**

Fixed 3 findings: (1) cla-dco.yml 'Fail if checks don't pass' step: moved ${{ steps.dco.outputs.dco_pass }} and ${{ steps.cla.outputs.cla_pass }} into env: vars DCO and CLA, removed inline interpolation from shell. (2) integration-test-suite/action.yml: fixed all 6 'Display metrics' steps (basic, filtered, keep-many, delete-all, short, combined) by moving all step output expressions into env: blocks and referencing them as plain shell variables. (3) scorecard.yml: replaced top-level 'permissions: read-all' with 'permissions: {}', relying on the existing job-level specific permissions (security-events: write, id-token: write, contents: read, actions: read).

