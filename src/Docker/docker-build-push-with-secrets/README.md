# Docker Build and Push with Secrets GitHub Action

> **Note**
> This GitHub Action showcases how to build and push a docker image with custom secrets (variables or files) included.

## Contents

- [Docker Build and Push with Secrets GitHub Action](#docker-build-and-push-with-secrets-github-action)
  - [Contents](#contents)
  - [Overview](#overview)
  - [Example](#example)
  - [Resources](#resources)

## Overview



## Example

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

      - name: Build and Push with Secrets
        uses: docker/build-push-action@v4
        with:
          context: .
          file: ./Dockerfile
          push: true
          tags: ${{ env.DOCKER_IMAGE_NAME }}:${{ env.DOCKER_IMAGE_TAG }}
          secrets: |
            "github_token=${{ secrets.GITHUB_TOKEN }}"
            "MY_SECRET=./secret.txt"
          build-args: |
            BASE_IMAGE=${{ env.DOCKER_BASE_IMAGE }}
            BUILDKIT_INLINE_CACHE=1
```

## Resources

