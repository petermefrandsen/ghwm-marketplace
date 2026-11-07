# Workflows

One folder per managed GitHub workflow, each containing `<name>.yaml`, `workflow.yml`,
`package.json` and `README.md`. Merging to `main` publishes new package versions to GitHub Packages
(see [`publish.yml`](../.github/workflows/publish.yml)).

| Workflow | Description | Docs |
| --- | --- | --- |
| `apm-update` | Refresh APM skills on a schedule and open a pull request | [Readme](apm-update/README.md) |
