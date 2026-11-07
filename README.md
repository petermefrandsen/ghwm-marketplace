# ghwm-marketplace

Personal [ghwm](https://github.com/ghwfxlab/ghwm) registry: workflow packages published to GitHub
Packages as `@petermefrandsen/<name>` and installed with the `ghwm` CLI. The layout follows
[ghwfxlab/ghwm-registry](https://github.com/ghwfxlab/ghwm-registry).

## Workflows

See [workflows/README.md](workflows/README.md).

## Using a workflow

Add it to `ghwm.yml` with a per-workflow `source`, then run `ghwm install`:

```yaml
workflows:
  - name: apm-update
    version: 1.0.0
    source: petermefrandsen/ghwm-marketplace
```

## Publishing

`.github/workflows/publish.yml` publishes every `workflows/*` package whose version is not yet on
GitHub Packages. Bump `version` in the package's `package.json` for each change.

> [!IMPORTANT]
> After the first publish, set each package's visibility to **Public** in GitHub Packages, or other
> repositories will not be able to read it.

## Local testing

From a consumer repository: `ghwm install --local ../ghwm-marketplace`.
