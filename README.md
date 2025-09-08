# Detailed Software Design Document (DSDD)

## Overview

This repository contains GitHub Actions workflows designed to automate various CI/CD processes for different types of projects, including Angular, Go, and Docker-based applications. The workflows are modular, reusable, and designed to ensure code quality, automate builds, and streamline deployments.

---

## Table of Contents

1. [Workflows Overview](#workflows-overview)
2. [Workflow Details](#workflow-details)
   - [Angular Build Workflow](#angular-build-workflow)
   - [Go Build Workflow](#go-build-workflow)
   - [Go Release Publish Workflow](#go-release-publish-workflow)
   - [Docker Pipelines Workflow](#docker-pipelines-workflow)
3. [Environment Variables and Secrets](#environment-variables-and-secrets)
4. [Versioning and Tagging](#versioning-and-tagging)
5. [Conclusion](#conclusion)

---

## Workflows Overview

### 1. Angular Build Workflow
The `angular-build-workflow.yaml` automates the build, test, and release processes for Angular projects. It supports draft releases and includes configurable inputs for pull request checks, test coverage thresholds, and Node.js versions.

### 2. Go Build Workflow
The `go-build-workflow.yaml` ensures code quality for Go projects by running linting, vetting, static analysis, and dependency vulnerability checks. It also verifies the existence of a `LICENSE` file and performs unit tests with test coverage validation.

### 3. Go Release Publish Workflow
The `go-release-publishToGithub-workflow.yaml` builds and publishes Go binaries for multiple platforms (e.g., Linux, Windows) and uploads them as release assets to GitHub.

### 4. Docker Pipelines Workflow
The `docker-pipelines-workflow.yaml` automates Docker image builds, tagging, and pushes to a Docker registry. It supports semantic versioning based on Git commit messages and includes shared steps for reusability.

---

## Workflow Details

### Angular Build Workflow

**File**: `angular-build-workflow.yaml`

#### Purpose
- Build and test Angular projects.
- Create draft releases for non-pull-request builds.
- Ensure high test coverage and code quality.

#### Inputs
- `PULLREQUEST_FLAG`: Boolean to determine if the workflow is triggered by a pull request.
- `TESTCOVERAGE_THRESHOLD`: Minimum test coverage percentage (default: 90).
- `NODE_VERSION`: Node.js version to use (default: 16).

#### Key Features
- Linting and testing.
- Builds the Angular project.
- Creates a draft release with the build artifacts.

#### Example Usage
```yaml
on:
  workflow_call:
    inputs:
      PULLREQUEST_FLAG:
        type: boolean
        default: true
```

### Go Build Workflow

**File**: go-build-workflow.yaml

#### Purpose
- Automate code quality checks for Go projects.
- Perform linting, vetting, static analysis, and dependency vulnerability checks.
- Validate test coverage and build the application.

#### Inputs
- TESTCOVERAGE_THRESHOLD: Minimum test coverage percentage (default: 90).

#### Key Features
- Runs multiple quality gates (e.g., lint, vet, static analysis).
- Checks for dependency vulnerabilities using nancy.
- Validates test coverage and enforces thresholds.
#### Example Usage
```yaml
on:
  workflow_call:
    inputs:
      TESTCOVERAGE_THRESHOLD:
        type: number
        default: 90
```

### Go Release Publish Workflow

**File**: go-release-publishToGithub-workflow.yaml

#### Purpose
- Build and publish Go binaries for multiple platforms.
- Upload binaries as release assets to GitHub.

#### Inputs
- BINARY_NAME: Name of the binary to be generated (default: binarynamerelease).

#### Key Features
- Builds binaries for Linux and Windows (amd64 architecture).
- Uploads binaries as release assets.
- Includes additional files (e.g., LICENSE, README.md) in the release.
#### Example Usage
```yaml
on:
  workflow_call:
    inputs:
      BINARY_NAME:
        type: string
        default: 'mybinary'
```

### Docker Pipelines Workflow

**File**: docker-pipelines-workflow.yaml

#### Purpose
- Automate Docker image builds, tagging, and pushes.
- Support semantic versioning based on Git commit messages.
- Include shared steps for reusability.
#### Triggers
- push events to the main branch or tags matching tags/*.
- Scheduled runs every Sunday at 2:00 AM UTC.
#### Jobs
- Shared Steps:
  - Fetches the repository name.
  - Calculates the Docker image version based on Git tags and commit history.
  - Builds, tags, and pushes Docker images.
- Main Branch Pipeline:
  - Validates Dockerfile format.
  - Scans Docker images for vulnerabilities.
  - Calculates the next version and creates a GitHub tag.
- Tag Pipeline:
  - Executes shared steps for tag-based workflows.
- Scheduled Pipeline:
  - Executes shared steps for scheduled workflows.

#### Example Usage
```yaml
on:
  push:
    branches:
      - main
    tags:
      - 'tags/*'
```

#### Environment Variables and Secrets

##### Required Secrets
 - DOCKER_USERNAME: Docker Hub username for authentication.
 - DOCKER_PASSWORD: Docker Hub password for authentication. 
 - GITHUB_TOKEN: Token for interacting with the GitHub API.

#### Example Usage
```yaml
env:
  DOCKER_USERNAME: ${{ secrets.DOCKER_USERNAME }}
  DOCKER_PASSWORD: ${{ secrets.DOCKER_PASSWORD }}
```

#### Versioning and Tagging
##### Semantic Versioning
The workflows use semantic versioning to calculate the next version based on Git commit messages:

 - +semver: major: Increments the major version.
 - +semver: minor: Increments the minor version.
 - +semver: patch: Increments the patch version.

Example
```yaml
- name: Fetch Latest Tag and Calculate Next Version
  run: |
    git fetch --tags
    LATEST_TAG=$(git tag --list 'v*' | sort -V | tail -n 1)
    # Logic to calculate the next version...
```
