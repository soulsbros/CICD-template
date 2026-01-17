# CICD-template

![Test workflow](https://github.com/soulsbros/CICD-template/actions/workflows/test.yml/badge.svg)

Shared template for GitHub Actions

## Usage

For example, to build on all branches and tags, add this to `.github/workflows/your-workflow.yml`:

```yaml
name: your-workflow

on:
  workflow_dispatch:
  push:
    branches:
      - "*"
    tags:
      - "*"

jobs:
  build-tags:
    # Build and push the image with the tag name
    uses: soulsbros/CICD-template/.github/workflows/docker-build.yml@main
    if: github.ref_type == 'tag'
    with:
      image-name: some-org/some-image:${{ github.ref_name }}  # TODO adapt to your needs
      push: true
    secrets:
      DOCKERHUB_USERNAME: ${{ secrets.DOCKERHUB_USERNAME }}
      DOCKERHUB_TOKEN: ${{ secrets.DOCKERHUB_TOKEN }}

  build-branches:
    # Build all branches but push only in main
    uses: soulsbros/CICD-template/.github/workflows/docker-build.yml@main
    if: github.ref_type == 'branch'
    with:
      image-name: some-org/some-image:latest  # TODO adapt to your needs
      push: ${{ github.ref_name == 'main' }}
      ping_webhook: ${{ github.ref_name == 'main' }}
    secrets:
      DOCKERHUB_USERNAME: ${{ secrets.DOCKERHUB_USERNAME }}
      DOCKERHUB_TOKEN: ${{ secrets.DOCKERHUB_TOKEN }}
      WEBHOOK_URL: ${{ secrets.WEBHOOK_URL }}
```

You can also override the Dockerfile location with the `dockerfile` input.

## Resources

### Actions used

<https://github.com/docker/login-action>

<https://github.com/docker/build-push-action>

### Documentation

<https://docs.github.com/en/actions/sharing-automations/reusing-workflows#creating-a-reusable-workflow>
