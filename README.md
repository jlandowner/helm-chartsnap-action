# Helm chartsnap action
[![GitHub Marketplace](https://img.shields.io/badge/Marketplace-Helm%20chartsnap%20action-blue.svg?colorA=24292e&colorB=0366d6&style=flat&longCache=true&logo=github)](https://github.com/marketplace/actions/helm-chartsnap-action)
![Helm chartsnap](https://img.shields.io/badge/Repo-Helm%20chartsnap-repo?style=flat&logo=github&labelColor=24292e&color=orange)
[![CI](https://github.com/jlandowner/helm-chartsnap-action/actions/workflows/test.yaml/badge.svg)](https://github.com/jlandowner/helm-chartsnap-action/actions/workflows/test.yaml)

A GitHub action to do continuous snapshot testing for changes of your or third-party Helm chart values in your repository.

If you want to update snapshots, the changes will be automatically committed to a new branch and a pull request created.

Helm chartsnap action will:

1. Run [helm-chartsnap](https://github.com/jlandowner/helm-chartsnap) for the Helm chart with your values file.
2. If you want to update snapshot, create a pull request to merge the new branch into the base&mdash;the branch checked out in the workflow.

## Usage

```yaml
jobs:
  test_action_remote_create_pr:
    runs-on: ubuntu-latest
    name: Remote chart snapshot and create PR
    permissions:
      contents: write
      pull-requests: write
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: Snapshot
        uses: jlandowner/helm-chartsnap-action@v1
        id: chartsnap
        with:
          chart: ingress-nginx
          repo: https://kubernetes.github.io/ingress-nginx
          values: test/remote/ingress-nginx.values.yaml
          update_snapshot: true

  test_action_local:
    runs-on: ubuntu-latest
    name: Local chart snapshot
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: Snapshot
        uses: jlandowner/helm-chartsnap-action@v1
        id: chartsnap
        with:
          chart: test/app1/

```

You can also pin to a [specific release](https://github.com/jlandowner/helm-chartsnap-action/releases) version in the format `@v1.x.x`

### Workflow permissions

For this action to work you must explicitly allow GitHub Actions to create pull requests.
This setting can be found in a repository's settings or organization's settings under `Actions > General > Workflow permissions`.

Also you needs to set `permissions` in your workflow config.

```yaml
    permissions:
      contents: write
      pull-requests: write
```

### Action inputs

| Name | Description | Required | Default |
|:-----|:------------|:---------|:--------|
| `chart` | Name of the Helm chart in remote repository or local chart directory path | Yes | - |
| `repo` | URL of the remote Helm repository | No | "" |
| `values` | Path to the values file or directory | No | "" |
| `additional_args` | Addtional args for the command "helm template" (e.g. `--namespace kube-system`, `--skip-tests`, `--show-only` etc.) | No | "" |
| `update_snapshot` | If set `true`, update snapshot and [create pull request](https://github.com/peter-evans/create-pull-request) if snapshots are changed | No | `false` |
| `disable_create_pull_request` | If set `true`, disable to [create pull request](https://github.com/peter-evans/create-pull-request) even if snapshots are changed | No | `false` |

## Example

WIP

## Example Pull Request

https://github.com/jlandowner/helm-chartsnap-action/pull/2

## License

[MIT](LICENSE)
