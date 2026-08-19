<!-- markdownlint-disable -->

# Hardening Report: igorjs--gh-actions-clean-workflow/v6

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **igorjs--gh-actions-clean-workflow/v6** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference actions using mutable tags or branch names instead of full 40-character SHA commit hashes. This exposes the workflow to supply-chain attacks where a tag is moved to point to malicious code.

- check-dist.yml: `actions/checkout@v4`, `actions/setup-node@v4`
- clean-logs.yml: `actions/checkout@v4`, `igorjs/gh-actions-clean-workflow@main`
- dependabot-auto-approve.yml: `dependabot/fetch-metadata@v2`
- dependabot-auto-merge.yml: `dependabot/fetch-metadata@v2`
- dependabot-pull-request.yml: `dependabot/fetch-metadata@v2`

All `uses:` references should be pinned to a full SHA, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `.github/workflows/check-dist.yml:21`
- `.github/workflows/check-dist.yml:27`
- `.github/workflows/check-dist.yml:33`
- `.github/workflows/clean-logs.yml:18`
- `.github/workflows/clean-logs.yml:21`
- `.github/workflows/dependabot-auto-approve.yml:12`
- `.github/workflows/dependabot-auto-merge.yml:12`
- `.github/workflows/dependabot-pull-request.yml:30`

### broad-permissions (severity: medium)

The workflow `check-dist.yml` declares top-level `permissions: contents: write` and `actions: write`. This workflow is triggered on `pull_request` events (including from forks), meaning these broad write permissions are granted to all pull request runs. The `contents: write` permission in particular is overly broad — the workflow only needs read access for checking the dist directory diff; write access is only needed in the commit step, which should have its own narrower job-level permissions block. Consider splitting into separate jobs or scoping permissions to the minimum required.

Locations:

- `.github/workflows/check-dist.yml:15`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, broad-permissions

**Notes:**

Fixed all unpinned action references by pinning to full 40-character SHA commit hashes: actions/checkout@v4 → SHA 11d5960a..., actions/setup-node@v4 → SHA 49933ea5..., igorjs/gh-actions-clean-workflow@main → SHA 7d77b747..., dependabot/fetch-metadata@v2 → SHA 21025c70... (applied to all three dependabot workflow files). Fixed broad-permissions in check-dist.yml by setting top-level permissions to 'contents: read' and moving the write permissions (contents: write, actions: write) to the job level where they are actually needed.

