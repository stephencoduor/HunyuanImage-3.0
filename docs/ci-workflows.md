# CI workflows

This repository builds and publishes container images through GitHub Actions. The workflow file lives at `.github/workflows/docker.yml`.

## Triggers

- **Tagged pushes (`v*`)**: build and push Docker images (with and without FlashAttention) and optionally package the Helm chart when `BUILD_HELM_CHART` is enabled as a repository variable.
- **Manual runs (`workflow_dispatch`)**: identical behaviour as tagged builds, with an input to enable Helm chart packaging.

## Required secrets and variables

| Name | Type | Purpose |
| --- | --- | --- |
| `REGISTRY_USERNAME` | Secret | Registry username for Docker login (defaults to GitHub actor when unset). |
| `REGISTRY_PASSWORD` | Secret | Registry password or token for Docker login (defaults to `GITHUB_TOKEN` when unset). |
| `BUILD_HELM_CHART` | Repository variable | When set to `true`, packages the Helm chart on tagged pushes. |

The workflow assumes images are published to `ghcr.io/<owner>/<repo>` unless overridden by updating the `REGISTRY` or `IMAGE_NAME` environment variables in the workflow.

## Behaviour

- **Import lint**: installs `requirements.txt`, then runs a compile/import check against the `app`, `hunyuan_image_3`, and `vllm_infer` packages to fail fast on syntax errors or missing imports.
- **Docker builds**: uses a build matrix to build images with and without `INSTALL_FLASH_ATTN`, pushes tagged images to the registry, and reuses cached layers via GitHub Actions cache scopes per matrix entry.
- **Helm packaging (optional)**: when enabled, packages the chart in `helm/chart` and uploads the resulting `.tgz` file as a workflow artifact for publishing to a chart repository.
