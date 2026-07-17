# Shoe Store

Official repository for the Shoe Store project.

## Status

Foundation Phase

Current Task:
Repository Initialization

This repository follows:

- Software Engineering Blueprint v1.0
- Project Profile v1.0

Development has not started yet.


## Backend Continuous Delivery Artifact

The Backend CI workflow creates a distributable executable JAR only after all required backend validations succeed.

Workflow configuration: `.github/workflows/backend-ci.yml`

### Artifact creation condition

The artifact is uploaded only after:

1. PostgreSQL service health validation succeeds.
2. Flyway connects to PostgreSQL and validates the migration baseline.
3. `mvn --batch-mode clean verify` completes successfully.
4. Backend tests pass.
5. The generated JAR is verified as a non-empty Spring Boot executable archive.

### Artifact name

The GitHub Actions artifact is named:

`shoe-store-backend-<commit-sha>`

The commit SHA identifies the source revision associated with the workflow run.

### Artifact content

The stored artifact contains only:

`target/shoe-store-backend-0.0.1-SNAPSHOT.jar`

Intermediate Maven output, test reports, plain JAR files, Docker images, releases, and deployment files are not included.

### Artifact retrieval

1. Open the successful workflow run in GitHub Actions.
2. Locate the **Artifacts** section on the workflow run summary.
3. Download the artifact named `shoe-store-backend-<commit-sha>`.
4. Extract the downloaded archive.
5. Verify that it contains `shoe-store-backend-0.0.1-SNAPSHOT.jar`.
6. Compare `<commit-sha>` with the commit shown on the workflow run.

This pipeline stores a build artifact only. It does not deploy the backend or publish a GitHub Release.