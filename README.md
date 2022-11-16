# github-actions


# Manual Usage

## Build Workflow
Use the following build.yml file in your project, decide what branches you want to build on, and reference the go-build-workflow
```
# This workflow will build a golang project
# For more information see: https://docs.github.com/en/actions/automating-builds-and-tests/building-and-testing-go

name: Build Main

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]
  workflow_dispatch:

jobs:
  build:
    uses:
      bigbird023/github-actions/.github/workflows/go-build-workflow.yaml@main 
```

## Release Workflow
Use the following release.yml file in your project and reference the go-release-publishToGithub-workflow

then create a release through the UI and this flow will kick off
```
name: Release binaries

on:
  release:
    types: [created]

jobs:
  releases:
    uses: bigbird023/github-actions/.github/workflows/go-release-publishToGithub-workflow.yaml@main
    with:
      BINARY_NAME: "fortifyjsonparsertoexcel"
```
