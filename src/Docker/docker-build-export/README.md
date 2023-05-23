# Docker Build Export Action

> **Note**
> GitHub Action that builds and *exports* a Docker Image.

## Contents

- [Docker Build Export Action](#docker-build-export-action)
  - [Contents](#contents)
  - [Overview](#overview)
  - [Example](#example)
  - [Resources](#resources)

## Overview

Use this action to [pass data between jobs](https://docs.github.com/en/actions/using-workflows/storing-workflow-data-as-artifacts#passing-data-between-jobs-in-a-workflow) in a workflow using the [actions/upload-artifact](https://github.com/actions/upload-artifact) and [actions/download-artifact](https://github.com/actions/download-artifact) GitHub Actions.

This allows one to *share built docker images between jobs* via GitHub Actions.

## Example

- [`docker-build-export.yml`](./docker-build-export.yml)

```yml
name: Docker Build Export

on:
  push:
    branches:
      - "main"
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3
      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2
      - name: Build and Export
        uses: docker/build-push-action@v4
        with:
          context: .
          tags: myimage:latest
          outputs: type=docker,dest=/tmp/myimage.tar
      - name: Upload artifact
        uses: actions/upload-artifact@v3
        with:
          name: myimage
          path: /tmp/myimage.tar

  use:
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2
      - name: Download artifact
        uses: actions/download-artifact@v3
        with:
          name: myimage
          path: /tmp
      - name: Load image
        run: |
          docker load --input /tmp/myimage.tar
          docker image ls -a
```

## Resources

- [Share built image between jobs with GitHub Actions | Docker Documentation](https://docs.docker.com/build/ci/github-actions/share-image-jobs/)
- [About self-hosted runners - GitHub Docs](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners/about-self-hosted-runners)
- [Storing workflow data as artifacts - GitHub Docs](https://docs.github.com/en/actions/using-workflows/storing-workflow-data-as-artifacts#passing-data-between-jobs-in-a-workflow)
- [actions/upload-artifact (github.com)](https://github.com/actions/upload-artifact)
- [actions/download-artifact (github.com)](https://github.com/actions/download-artifact)
