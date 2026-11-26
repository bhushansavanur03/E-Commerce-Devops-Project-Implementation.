# CI Pipeline – Product Catalog Service

## 1. Overview

Implemented a Continuous Integration (CI) pipeline for the Product Catalog microservice using GitHub Actions.

The objective was to automate application building, unit testing, and code quality validation whenever changes were submitted through a Pull Request targeting the `main` branch.

## 2. Selecting the Microservice

Selected the **Product Catalog** microservice from the OpenTelemetry Demo project.

This service was used to implement and test the CI workflow.

## 3. Creating the GitHub Actions Workflow

Created a workflow file named `CI.yaml` inside the following directory:

```text
.github/
└── workflows/
    └── CI.yaml
```

The workflow defines the automation steps required to build, test, and validate the Product Catalog service.

### Workflow Triggers

Configured the following GitHub events:

- `pull_request`: Triggers CI when a Pull Request targets the `main` branch.
- `push`: Triggers the workflow when changes are pushed to the `main` branch.

## 4. Configuring CI Jobs

The workflow contains four jobs.

### Job 1: Build and Test

- Used `actions/checkout@v4` to check out the repository.
- Configured Go 1.22 using `actions/setup-go@v5`.
- Downloaded Go dependencies using `go mod download`.
- Built the Product Catalog application using `go build`.
- Executed unit tests using `go test ./...`.

### Job 2: Code Quality

Integrated `golangci-lint` to perform static code analysis and identify potential issues in the Go application.

Configured the `golangci/golangci-lint-action@v6` GitHub Action.

During initial testing, the linter identified three issues in `src/product-catalog/main.go`:

1. An unused `createClient` function.
2. Unnecessary use of `fmt.Sprintf` for a constant string.
3. Use of the deprecated `grpc.DialContext` API inside the unused function.

Fixed the reported issues and pushed the updated code to the feature branch.

### Job 3: Docker Image Build and Push

Configured Docker Buildx to build the Product Catalog Docker image.

Used the Docker login action to authenticate with Docker Hub.

The pipeline builds and pushes the image to Docker Hub with a unique image tag based on the GitHub Actions run ID.

Image naming convention:

```text
DOCKER_USERNAME/product-catalog:GITHUB_RUN_ID
```

This job runs only when changes are pushed to the `main` branch.

### Job 4: Kubernetes Manifest Update

Configured an automated step to update the Docker image reference in:

```text
kubernetes/productcatalog/deploy.yaml
```

Used the `sed` command to replace the existing image reference with the newly published Docker image tag.

The workflow then:

- Configures Git identity for GitHub Actions.
- Stages the updated Kubernetes deployment manifest.
- Commits the manifest change.
- Pushes the updated manifest to the `main` branch.

This job depends on the successful completion of the Docker job.

## 5. Managing Secrets

Avoided hardcoding Docker Hub credentials and used GitHub repository secrets instead.

Configured the following secrets:

| Secret | Purpose |
|---|---|
| `DOCKER_USERNAME` | Docker Hub username |
| `DOCKER_TOKEN` | Docker Hub access token |

The workflow also uses GitHub Actions' built-in `GITHUB_TOKEN` for repository operations, with the required `contents: write` permission.

## 6. Testing the CI Pipeline

To verify the workflow, created a separate feature branch:

```bash
git checkout -b githubcicheck
```

Modified the `main.go` file inside the Product Catalog service to generate a change for testing.

Checked the changes:

```bash
git status
```

Staged and committed the changes:

```bash
git add .
git commit -m "trigger CI pipeline"
```

Pushed the feature branch to GitHub:

```bash
git push origin githubcicheck
```

Opened the Pull Request to merge the feature branch into `main`.

## 7. Debugging and Fixing CI Issues

During initial testing, the workflow did not trigger because GitHub Actions had not yet been enabled for the repository.

Enabled GitHub Actions and investigated the workflow execution.

Also encountered Git branch divergence while synchronizing the feature branch with `main`. Resolved the issue using Git rebase and `git push --force-with-lease` after checking the branch history.

Once the workflow started executing, the Build job passed, but the Code Quality job failed due to Go linting issues.

Inspected the GitHub Actions logs, identified the three lint issues, corrected the Go source code, and pushed the changes to the feature branch.

## 8. Final Verification

Verified the workflow execution through the GitHub Actions interface.

The following behavior was confirmed:

| Job | Pull Request | Push to main |
|---|---|---|
| Build and Test | Runs | Runs |
| Code Quality | Runs | Runs |
| Docker Build and Push | Skipped | Runs |
| Kubernetes Manifest Update | Skipped | Runs after Docker |

After the changes reached `main`, verified the post-merge automation:

- Docker image was successfully built and published to Docker Hub.
- Verified the newly published Product Catalog image in Docker Hub.
- Checked `kubernetes/productcatalog/deploy.yaml`.
- Confirmed that the deployment manifest contained the newly generated Docker image tag.

## 9. Outcome

Successfully implemented and tested a GitHub Actions workflow for the Product Catalog service.

The implementation automated:

- Go application building and testing.
- Static code analysis using golangci-lint.
- Docker image creation and publishing after changes reach `main`.
- Automatic Kubernetes deployment manifest image-tag updates.

This established an automated CI validation process and a post-merge delivery workflow for the Product Catalog microservice.

---

**Next Section:** Continuous Delivery (CD) Pipeline – Kubernetes Deployment and Application Rollout.
