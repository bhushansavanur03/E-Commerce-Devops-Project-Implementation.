# GitHub Actions

## Introduction

GitHub Actions is a CI/CD automation platform provided by GitHub. It allows developers to automate software development workflows directly within their GitHub repositories.

It can automatically execute workflows whenever specific repository events occur, such as code pushes, Pull Requests, or manually triggered events.

In this project, GitHub Actions is used to automate the Continuous Integration (CI) process, including source code checkout, testing, application building, Docker image creation, vulnerability scanning, and pushing Docker images to Docker Hub.

## GitHub Actions Workflow Structure

GitHub Actions workflows are defined using YAML files and stored inside the following directory structure in the repository:

```text
project-root/
└── .github/
    └── workflows/
        └── CI.yaml
```

The `CI.yaml` file contains the instructions that define how and when the CI pipeline should execute.

## Important Components of GitHub Actions

### 1. Workflow

A workflow is an automated process defined in a YAML file. It contains the events, jobs, and steps required to execute a CI/CD pipeline.

### 2. Events (Triggers)

Events define when a workflow should execute.

Common examples include:

- `push`: Triggers the workflow when code is pushed to a repository.
- `pull_request`: Triggers the workflow when a Pull Request is opened or updated.
- `workflow_dispatch`: Allows a workflow to be triggered manually.

### 3. Jobs

A job is a collection of steps that execute on a GitHub-hosted runner or a configured self-hosted runner.

A workflow can contain multiple jobs, and jobs can run in parallel by default. Dependencies can be configured using `needs` to control execution order.

### 4. Steps

Steps are individual tasks executed within a job. They can execute shell commands or use reusable GitHub Actions.

Examples include:

- Checking out source code.
- Installing application dependencies.
- Running unit tests.
- Building the application.
- Creating Docker images.
- Scanning images for vulnerabilities.
- Pushing images to Docker Hub.

### 5. Actions

Actions are reusable units of automation that help developers perform common tasks without writing everything from scratch.

For example:

- `actions/checkout`: Checks out the repository source code.
- `docker/setup-buildx-action`: Sets up Docker Buildx.
- `docker/login-action`: Authenticates with a container registry.
- `docker/build-push-action`: Builds and optionally pushes Docker images.

These reusable actions simplify workflow development and reduce the amount of custom code required.

## Execution Flow

The GitHub Actions CI workflow generally follows this sequence:

1. A developer pushes code or creates a Pull Request.
2. GitHub detects the configured repository event.
3. The corresponding workflow is triggered.
4. A runner is assigned to execute the workflow.
5. The runner executes the configured jobs and their steps.
6. Each step performs its assigned task using shell commands or reusable GitHub Actions.
7. After successful validation and image creation, the Docker image can be pushed to Docker Hub.

## Summary

GitHub Actions provides an integrated automation platform for implementing CI/CD pipelines directly within GitHub repositories. By defining workflows using YAML files and combining jobs, steps, shell commands, and reusable Actions, developers can automate repetitive software development and deployment tasks efficiently.
