# Docker Build and Push to Multiple Registries

> **Note**
> This GitHub Action builds a Docker Image and pushes it to multiple registries (i.e. DockerHub, Github Container Registry, etc.).

## Contents

- [Docker Build and Push to Multiple Registries](#docker-build-and-push-to-multiple-registries)
  - [Contents](#contents)
  - [Overview](#overview)
  - [Example](#example)
  - [Resources](#resources)

## Overview

It is useful to be able to build a single docker image, re-tag it for multiple registries, login to multiple registries,
and finally, push the image to those registries.

See the example below for how to do this.

## Example

In the example below, the docker image is built and pushed to the following registries:

- DockerHub
- GitHub Container Registry
- Azure Container Registry

***

- [`docker-build-push-multi.yml`](./docker-build-push-multi.yml):

```yml
name: Docker Build

on:
  push:
    branches:
      - main
  workflow_dispatch:

env:
  DOCKER_BASE_IMAGE: "nodejs:latest"
  DOCKER_IMAGE_NAME: "${{ github.repository_owner }}/${{ github.repository }}"
  DOCKER_IMAGE_TAG: "latest"
  DOCKER_USERNAME: "" # ${{ secrets.DOCKER_USERNAME }}
  DOCKER_PASSWORD: "" # ${{ secrets.DOCKER_PASSWORD }}
  ACR_REGISTRY: "" # ${{ secrets.ACR_REGISTRY }}
  ACR_USERNAME: "" # ${{ secrets.ACR_USERNAME }}
  ACR_PASSWORD: "" # ${{ secrets.ACR_PASSWORD }}

jobs:
  docker:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: Setup QEMU
        uses: docker/setup-qemu-action@v2

      - name: Setup Docker Buildx
        uses: docker/setup-buildx-action@v2

      - name: Login to DockerHub
        uses: docker/login-action@v2
        with:
          username: ${{ env.DOCKER_USERNAME }}
          password: ${{ env.DOCKER_PASSWORD }}

      - name: Login to Github Container Registry
        uses: docker/login-action@v2
        with:
          registry: ghcr.io
          username: ${{ github.repository_owner }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Login to ACR
        uses: docker/login-action@v2
        with:
          registry: ${{ env.ACR_REGISTRY }}
          username: ${{ env.ACR_USERNAME }}
          password: ${{ env.ACR_PASSWORD }}

      - name: Build and Push
        uses: docker/build-push-action@v4
        with:
          context: .
          platforms: linux/amd64,linux/arm64
          push: true
          tags: |
            ${{ env.DOCKER_USERNAME }}/${{ env.DOCKER_IMAGE_NAME }}:${{ env.DOCKER_IMAGE_TAG }}
            ghcr.io/${{ env.DOCKER_IMAGE_NAME }}:${{ env.DOCKER_IMAGE_TAG }}
            ghcr.io/${{ env.DOCKER_IMAGE_NAME }}:latest
            ${{ env.ACR_REGISTRY }}/${{ env.DOCKER_IMAGE_NAME }}:${{ env.DOCKER_IMAGE_TAG }}
            ${{ env.ACR_REGISTRY }}/${{ env.DOCKER_IMAGE_NAME }}:latest
```

## Resources
