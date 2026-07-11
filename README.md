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
  lint:
    uses: soulsbros/CICD-template/.github/workflows/node.yml@158288970a760999c5e7736080e51e6fb55c29a8 # 1.1.3

  build-tags:
    # Build and push the image with the tag name
    uses: soulsbros/CICD-template/.github/workflows/docker-build.yml@158288970a760999c5e7736080e51e6fb55c29a8 # 1.1.3
    if: github.ref_type == 'tag'
    with:
      image-name: some-org/some-image:${{ github.ref_name }}  # TODO adapt to your needs
      push: true
    secrets:
      DOCKERHUB_USERNAME: ${{ secrets.DOCKERHUB_USERNAME }}
      DOCKERHUB_TOKEN: ${{ secrets.DOCKERHUB_TOKEN }}

  build-branches:
    # Build all branches but push only in main
    uses: soulsbros/CICD-template/.github/workflows/docker-build.yml@158288970a760999c5e7736080e51e6fb55c29a8 # 1.1.3
    if: github.ref_type == 'branch'
    with:
      image-name: some-org/some-image:latest  # TODO adapt to your needs
      push: ${{ github.ref_name == 'main' }}
      ping_webhook: ${{ github.ref_name == 'main' }}
    secrets:
      DOCKERHUB_USERNAME: ${{ secrets.DOCKERHUB_USERNAME }}
      DOCKERHUB_TOKEN: ${{ secrets.DOCKERHUB_TOKEN }}
      WEBHOOK_URL: ${{ secrets.WEBHOOK_URL }}
  
  # Optional: also build arm64 image on native hardware
  build-branches-arm:
    # Build all branches but push only in main (no ping)
    uses: soulsbros/CICD-template/.github/workflows/docker-build.yml@158288970a760999c5e7736080e51e6fb55c29a8 # 1.1.3
    if: github.ref_type == 'branch'
    with:
      image-name: some-org/some-image:arm  # TODO adapt to your needs
      push: ${{ github.ref_name == 'main' }}
      ping_webhook: false
      runner: ubuntu-26.04-arm
    secrets:
      DOCKERHUB_USERNAME: ${{ secrets.DOCKERHUB_USERNAME }}
      DOCKERHUB_TOKEN: ${{ secrets.DOCKERHUB_TOKEN }}
      WEBHOOK_URL: ${{ secrets.WEBHOOK_URL }}
  
  # Optional: code roasting
  sonarqube:
    uses: soulsbros/CICD-template/.github/workflows/sonarqube.yml@158288970a760999c5e7736080e51e6fb55c29a8 # 1.1.3
    if: github.ref_name == 'main'
    with:
      host-url: https://my-sonar-instance.domain.com  # TODO adapt to your needs
    secrets:
      SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
```

You can also override the Dockerfile location with the `dockerfile` input, or the type of runner used.

## Resources

### Actions used

<https://github.com/docker/login-action>

<https://github.com/docker/build-push-action>

<https://github.com/actions/checkout>

<https://github.com/SonarSource/sonarqube-scan-action>

<https://github.com/SonarSource/sonarqube-quality-gate-action>

<https://github.com/SocketDev/action>

### Documentation

<https://docs.github.com/en/actions/sharing-automations/reusing-workflows#creating-a-reusable-workflow>
