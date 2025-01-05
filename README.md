# Build and Push Containers with Buildah

- [Usage](#usage)
- [Inputs](#inputs)
- [Secrets](#secrets)
- [Workflow Steps](#workflow-steps)
- [Permissions](#permissions)
- [Notes](#notes)

This is a reusable GitHub Actions workflow that builds and pushes container
images using Buildah. It's designed to be flexible and configurable for various
projects.

## Usage

To use this workflow in your GitHub Actions, create a new workflow file (e.g.,
`.github/workflows/build-container.yml`) in your repository with the following
content:

```yaml
on:
  push:
    branches: ["**"]
  workflow_dispatch:
  release:
    types: [created]

permissions:
  packages: write
  issues: read
  contents: write

jobs:
  build-and-push-with-buildah:
    uses: circleeh/workflows-build-containers/.github/workflows/buildah-ci.yaml
    with:
      image_description: "My very special project."
      image_authors: "Robin Developer <robin@example.com>"
      image_title: ${{ github.event.repository.name }}
    secrets:
      CALLING_GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

## Inputs

| Name                        | Description                                                          | Required | Default                                               |
| --------------------------- | -------------------------------------------------------------------- | -------- | ----------------------------------------------------- |
| `build_context`             | The build context to use for the build                               | No       | `.`                                                   |
| `containerfiles`            | The Dockerfile or Containerfile to use                               | No       | `Containerfile`                                       |
| `image_authors`             | The authors of the container image                                   | No       | `''`                                                  |
| `image_description`         | The description of the container image                               | No       | `''`                                                  |
| `image_source`              | The source of the container image                                    | No       | `'${{ github.server_url }}/${{ github.repository }}'` |
| `image_suffix`              | Suffix to add to the image name                                      | No       | `''`                                                  |
| `image_title`               | The title of the container image                                     | No       | `'Default Container Image Title'`                     |
| `image_vendor`              | The vendor of the container image                                    | No       | `'Circle Eh!'`                                        |
| `image_version`             | The version of the container image                                   | No       | `'${{ github.sha }}'`                                 |
| `org_name`                  | The GitHub owner or organization of the repository                   | No       | `circleeh`                                            |
| `registry_base`             | The hostname/domain of the container image registry                  | No       | `ghcr.io`                                             |
| `registry_username`         | The username to authenticate with the package registry               | No       | ``                                                    |
| `repo_name`                 | The repository you wish to act on                                    | No       | `${{ github.repository }}`                            |

## Secrets

| Name                   | Description                                                                                               | Required |
| ---------------------- | --------------------------------------------------------------------------------------------------------- | -------- |
| `CALLING_GITHUB_TOKEN` | Should always be set to the CD token secret                                                               | Yes      |
| `registry_token`       | The password to authenticate to the container registry. Will default to CALLING_GITHUB_TOKEN if undefined | No       |

## Workflow Steps

1. Set environment variables
2. Checkout code
3. Setup Build Environment (install Buildah and Podman)
4. Determine Image Tags
5. Registry login
6. Build Image
7. Push Image(s) to Registry

## Permissions

This workflow requires the following permissions:

- `packages: write`
- `issues: read`
- `contents: write`

## Notes

- This workflow uses Buildah to build container images and Podman to push them to the registry.
- It supports caching to speed up builds.
- The workflow automatically generates OCI labels for the container image.
- It supports building projects that contain git submodules.

For more details on the specific configuration options, please refer to the workflow file itself.
