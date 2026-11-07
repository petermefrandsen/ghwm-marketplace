# APM Update Workflow

The `apm-update` workflow refreshes the [APM](https://microsoft.github.io/apm/) dependencies (agent
skills) declared in `apm.yml`, rewrites `apm.lock.yaml` and the deployed skill files, and opens a
pull request with the result. It is the APM counterpart of
[`ghwm-update`](https://github.com/ghwfxlab/ghwm-registry/tree/main/workflows/ghwm-update).

## What gets installed

After running `ghwm install`, the consumer repository gets:

- `.github/workflows/apm-update.yaml`
- An entry in `ghwm.lock`

## How it works

The workflow triggers on a schedule (`0 6 * * 1`, Mondays at 06:00 UTC) and through
`workflow_dispatch`. On each run it:

1. creates a GitHub App installation token with `contents` and `pull-requests` write permissions
2. runs [`microsoft/apm-action`](https://github.com/microsoft/apm-action) with `update: true`, which
   executes `apm update --yes` with a pinned APM CLI (`env.APM_VERSION`)
3. opens or updates a pull request from `chore/apm-update` when `apm.lock.yaml` or the deployed
   skills changed (the branch is force-pushed on every run)

If `apm.yml` declares no dependencies, the run produces no changes and no pull request.

## Consumer Setup

### 1. Install the workflow

`ghwm.yml` supports a per-workflow `source`, so this package can be installed next to packages from
the main registry:

```yaml
source: ghwfxlab/ghwm-registry
workflows:
  - name: apm-update
    version: 1.0.0
    source: petermefrandsen/ghwm-marketplace
```

Then run `ghwm install` and commit `.github/workflows/apm-update.yaml` and `ghwm.lock`.

### 2. Required secrets

| Secret | Purpose |
| --- | --- |
| `GH_APP_PRIVATE_KEY` | Private key of the GitHub App used to push the update branch and open the pull request. |
| `GH_APP_CLIENT_ID` or `GH_APP_ID` | Identifier of the same GitHub App. |
| `APM_PAT` | Optional. Token used by APM to resolve dependencies in private repositories outside the current one. Defaults to `github.token`. |

The GitHub App must be installed on the consumer repository with `contents: write` and
`pull-requests: write`.

### 3. Conventions

- pull request branch: `chore/apm-update` (owned by the workflow, force-pushed on every run)
- commit and title: `chore(ci): update APM skills`
- label: `flag:dependencies`

## Update

Bump the version in `ghwm.yml`, then run `ghwm update`.

## Remove

Remove the entry from `ghwm.yml`, then run `ghwm install` to prune the workflow file.

## Current Limitations

- `ghwm-update` resolves every package in `ghwm.yml` against the global `source` owner, so it logs a
  warning that it cannot resolve `@<owner>/apm-update` for entries with their own `source`. The
  warning is harmless; bump the `apm-update` version in `ghwm.yml` by hand.
- Only dependencies already declared in `apm.yml` are refreshed; new skills are not added.
