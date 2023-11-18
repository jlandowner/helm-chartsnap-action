# Helm chartsnap action
[![GitHub Marketplace](https://img.shields.io/badge/Marketplace-Helm%20chartsnap%20action-blue.svg?colorA=24292e&colorB=0366d6&style=flat&longCache=true&logo=github)](https://github.com/marketplace/actions/helm-chartsnap-action)
[![Helm chartsnap](https://img.shields.io/badge/Repo-Helm%20chartsnap-repo?style=flat&logo=github&labelColor=24292e&color=orange)](https://github.com/jlandowner/helm-chartsnap)
[![CI](https://github.com/jlandowner/helm-chartsnap-action/actions/workflows/test.yaml/badge.svg)](https://github.com/jlandowner/helm-chartsnap-action/actions/workflows/test.yaml)

A GitHub action to do continuous snapshot testing for changes of your or third-party Helm chart values in your repository.

If you want to update snapshots, the changes will be automatically committed to a new branch and a pull request created.

Helm chartsnap action will:

1. Run [helm-chartsnap](https://github.com/jlandowner/helm-chartsnap) for the Helm chart with your values file.
2. If you want to update snapshot, create a pull request to merge the new branch into the base&mdash;the branch checked out in the workflow.

## Usage

```yaml
jobs:
  chartsnap-ingress-nginx:
    runs-on: ubuntu-latest
    name: Do snapshot ingress-nginx and create PR if snapshot changed
    permissions:
      contents: write
      pull-requests: write
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Chart Snapshot for ingress-nginx
        uses: jlandowner/helm-chartsnap-action@v1
        with:
          repo:   https://kubernetes.github.io/ingress-nginx
          chart:  ingress-nginx
          values: charts/remote/ingress-nginx.values.yaml
          update_snapshot: true

  chartsnap-local:
    runs-on: ubuntu-latest
    name: Local chart snapshot
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Chart Snapshot for local chart
        uses: jlandowner/helm-chartsnap-action@v1
        id: chartsnap
        with:
          chart:  charts/app1/
          values: charts/app1/test/
          update_snapshot: true

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

## Examples

### Action Usecases

#### 1. Get snapshot of third-party chart using your values file continuously to get diff when chart version is bumpped up

```yaml
jobs:
  chartsnap-ingress-nginx:
    runs-on: ubuntu-latest
    name: Do snapshot ingress-nginx and create PR if snapshot changed
    permissions:
      contents: write
      pull-requests: write
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Chart Snapshot for ingress-nginx
        uses: jlandowner/helm-chartsnap-action@v1
        with:
          repo:   https://kubernetes.github.io/ingress-nginx # Third-party chart repository
          chart:  ingress-nginx                              # Chart name in the repository to snapshot
          values: charts/remote/ingress-nginx.values.yaml    # Your values file path
          update_snapshot: true                              # Create PR if snapshot is changed
```

#### 2. Get snapshots of all expected usecase patterns of values files of your chart continuously

```yaml
  chartsnap-local:
    runs-on: ubuntu-latest
    name: Do snapshot app1 chart and create PR if snapshot changed
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Chart Snapshot for local chart
        uses: jlandowner/helm-chartsnap-action@v1
        id: chartsnap
        with:
          chart:  charts/app1/       # Chart directory path to snapshot
          values: charts/app1/test/  # Directory including the values files
          update_snapshot: true      # Create PR if snapshots are changed
```

#### 3. Test backward compatibility of your chart with old values continuously

```yaml
  chartsnap-local:
    runs-on: ubuntu-latest
    name: Test app1 chart's backward compatibility with old values
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Snapshot testing for app1 chart
        uses: jlandowner/helm-chartsnap-action@v1
        id: chartsnap
        with:
          chart:  charts/app1/       # Chart directory path to snapshot
          values: charts/app1/test/  # Directory including the values files
          # if snapshot is changed, this action fails and show the difference in log.
```

### Pull Request example

See https://github.com/jlandowner/helm-chartsnap-action/pull/2

## License

[MIT](LICENSE)
