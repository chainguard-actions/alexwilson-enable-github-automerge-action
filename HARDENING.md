<!-- markdownlint-disable -->

# Hardening Report: alexwilson--enable-github-automerge-action/1.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **alexwilson--enable-github-automerge-action/1.1.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference GitHub Actions using mutable branch names (@master) or version tags (@v4) instead of immutable full 40-character commit SHAs. This exposes the workflow to supply-chain attacks if the referenced action is compromised or altered. Failing references: auto-merge-dependabot.yml — `actions/checkout@master`; rebuild.yml — `actions/checkout@master`, `actions/setup-node@master`, `stefanzweifel/git-auto-commit-action@v4`; test.yml — `actions/checkout@master`, `actions/setup-node@master`.

Locations:

- `.github/workflows/auto-merge-dependabot.yml:19`
- `.github/workflows/rebuild.yml:15`
- `.github/workflows/rebuild.yml:18`
- `.github/workflows/rebuild.yml:23`
- `.github/workflows/test.yml:17`
- `.github/workflows/test.yml:20`

### missing-permissions (severity: medium)

None of the workflow files define a top-level `permissions:` block, and none of the individual jobs define job-level `permissions:` blocks. Without explicit permissions, workflows run with the default (often broad) token permissions, violating the principle of least privilege.

Locations:

- `.github/workflows/auto-merge-dependabot.yml:1`
- `.github/workflows/rebuild.yml:1`
- `.github/workflows/test.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all three workflow files:

1. **unpinned-uses**: Pinned all mutable action references to full 40-character commit SHAs:
   - `actions/checkout@master` → `actions/checkout@61b9e3751b92087fd0b06925ba6dd6314e06f089 # master` (in all 3 files)
   - `actions/setup-node@master` → `actions/setup-node@60c11408891ef62846c29c28c2374afe5c91fad5 # master` (in rebuild.yml and test.yml)
   - `stefanzweifel/git-auto-commit-action@v4` → `stefanzweifel/git-auto-commit-action@3ea6ae190baf489ba007f7c92608f33ce20ef04a # v4` (in rebuild.yml)

2. **missing-permissions**: Added top-level `permissions: {}` to all three workflow files, plus job-level permissions with minimum required access:
   - `auto-merge-dependabot.yml`: `auto-merge-dependency-updates` job gets `contents: read, pull-requests: write`; `auto-approve-dependency-updates` job gets `pull-requests: write`
   - `rebuild.yml`: `rebuild` job gets `contents: write` (needed to commit rebuilt dist files)
   - `test.yml`: `test` job gets `contents: read`

