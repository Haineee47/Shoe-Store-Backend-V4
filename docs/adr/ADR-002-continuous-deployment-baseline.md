# ADR-002 — Continuous Deployment Baseline

- ADR ID: ADR-002 — Provisional
- Status: Proposed
- Decision Status: Pending Governance Decision
- Source Task: TASK-009
- Source Project State: Official Project State v1.1.3


# ADR-002 — Continuous Deployment Baseline

## 1. Metadata

| Field                | Value                                                    |
| -------------------- | -------------------------------------------------------- |
| Artifact Type        | Architecture Decision Record                             |
| ADR ID               | ADR-002 — Provisional                                    |
| Title                | Continuous Deployment Baseline                           |
| Status               | Proposed                                                 |
| Decision Status      | Pending Governance Decision                              |
| Source Task          | TASK-009 — Continuous Deployment Baseline Definition     |
| Source Project State | Official Project State v1.1.3                            |
| Current Phase        | Foundation                                               |
| Current Milestone    | Foundation Preparation                                   |
| Author               | Development Workspace                                    |
| Reviewer             | Project State Manager                                    |
| Approver             | Project State Manager or designated governance authority |
| Date                 | 2026-07-17                                               |
| Supersedes           | None                                                     |
| Superseded By        | None                                                     |

> `ADR-002` is a provisional identifier and must be confirmed against the official Decision Register before approval.

---

# 2. Status

```text
PROPOSED
```

This ADR has not been:

* Accepted.
* Approved.
* Added to the official Decision Register.
* Incorporated into Baseline References.
* Used to authorize deployment implementation.

Continuous Deployment remains:

```text
Not Started
```

---

# 3. Context

Official Project State v1.1.3 confirms that the following Foundation deliverables have been completed:

* Repository Initialization.
* Backend Bootstrap.
* Frontend Bootstrap.
* Database Foundation Setup.
* Docker Environment Setup.
* Backend Continuous Integration Pipeline Setup.
* Frontend Continuous Integration Pipeline Setup.
* Backend Continuous Delivery Artifact Pipeline Setup.

The backend pipeline can:

* Verify the backend using PostgreSQL.
* Package an executable Spring Boot JAR.
* Store the packaged JAR as a GitHub Actions artifact.
* Associate the artifact with a source revision and workflow run.

ADR-001 establishes that Spring Boot integration tests requiring application-context startup must use PostgreSQL through GitHub Actions service containers.

However, the project currently has no approved Continuous Deployment Baseline defining:

* Initial deployment scope.
* Target environment.
* Hosting platform.
* Runtime topology.
* Deployment trigger.
* Branch or tag policy.
* Approval policy.
* Secret-management policy.
* Workflow permissions.
* Database migration execution policy.
* Health-check policy.
* Rollback or recovery policy.
* Operational ownership.
* Required deployment evidence.

Creating a deployment workflow without these decisions would introduce unapproved Engineering, Security and Operations Baseline changes.

---

# 4. Problem Statement

The project has an approved mechanism for producing a verified backend artifact, but it has no approved mechanism for deploying that artifact into a runtime environment.

Continuous Deployment is not simply an additional GitHub Actions step.

It requires coordinated decisions involving:

* Application runtime.
* Infrastructure.
* Environment isolation.
* Authentication and authorization.
* Secrets.
* Database migration.
* Availability.
* Health verification.
* Failure recovery.
* Monitoring.
* Audit evidence.

Without a formal baseline, implementation would require Development Workspace to make architectural and operational assumptions beyond its authority.

The project therefore requires an approved Continuous Deployment architecture decision before any deployment implementation task can begin.

---

# 5. Decision Scope

This ADR is intended to establish the baseline for the first application deployment pipeline.

The decision must cover:

1. Initial deployable component.
2. Initial target environment.
3. Hosting and runtime model.
4. Artifact source.
5. Deployment trigger.
6. Branch or release policy.
7. Environment approval policy.
8. Secret-management mechanism.
9. Deployment authentication mechanism.
10. Workflow permissions.
11. Database migration policy.
12. Deployment strategy.
13. Health-check and validation policy.
14. Rollback and recovery policy.
15. Operational ownership.
16. Logging and evidence requirements.

---

# 6. Out of Scope

This ADR does not itself:

* Create a deployment workflow.
* Deploy the backend.
* Deploy the frontend.
* Provision a VM or server.
* Provision a container host.
* Configure Kubernetes.
* Create a cloud account.
* Create a cloud resource.
* Create a production database.
* Run Flyway on staging or production.
* Create production credentials.
* Configure domain, DNS or TLS.
* Publish a Docker image.
* Create a container registry.
* Create a GitHub Release.
* Modify backend source code.
* Modify frontend source code.
* Modify the Official Project State.
* Mark Continuous Deployment as completed.

Implementation must be performed through one or more future Current Tasks after this ADR is approved.

---

# 7. Decision Drivers

The decision must prioritize the project’s established quality goals:

1. Maintainability.
2. Scalability.
3. Performance.
4. Development speed.
5. Cost optimization.

Additional decision drivers are:

* Simplicity suitable for the Foundation Phase.
* Low operational overhead.
* Clear artifact traceability.
* PostgreSQL production parity.
* Least-privilege access.
* Secret isolation.
* Environment isolation.
* Controlled database migration.
* Reliable health verification.
* Recoverability.
* Auditability.
* Avoidance of unnecessary infrastructure complexity.
* Ability to evolve without immediate migration to distributed architecture.

---

# 8. Governance Principles

The approved Continuous Deployment Baseline must satisfy the following principles.

## 8.1 Verified Artifact Only

Deployment must consume an artifact that has already passed the required CI and Continuous Delivery checks.

The deployment stage must not rebuild the application.

---

## 8.2 Immutable Artifact

The artifact deployed must be identifiable and immutable.

The system must not rely solely on an ambiguous label such as:

```text
latest
```

without an associated immutable identifier.

---

## 8.3 Source-to-Runtime Traceability

Every deployment must support the traceability chain:

```text
Repository
→ Branch or Tag
→ Commit SHA
→ CI Workflow
→ Verified Artifact
→ Deployment Run
→ Target Environment
→ Runtime Release Identity
```

---

## 8.4 Environment Isolation

Secrets, credentials, configuration, databases and permissions must be separated by environment.

Production credentials must not be reused in:

* Local development.
* CI.
* Testing.
* Non-production environments.

---

## 8.5 Least Privilege

The deployment workflow must receive only the permissions necessary to deploy to the approved environment.

Runtime application credentials must not automatically receive database schema administration rights.

---

## 8.6 Explicit Database Migration Policy

Successful Flyway execution in CI does not authorize automatic Flyway execution in staging or production.

Database migration must follow an explicitly approved policy.

---

## 8.7 Health-based Deployment Success

A deployment must not be considered successful merely because:

* A process started.
* A container is running.
* A port opened.
* A workflow returned exit code zero.

Deployment success requires approved health checks and post-deployment validation.

---

## 8.8 Explicit Recovery State

A failed, cancelled or interrupted deployment must result in a known runtime state.

`State Unknown` must not be treated as successful.

---

## 8.9 Evidence Required

Every deployment, rollback and recovery must generate traceable and sanitised evidence.

---

# 9. Considered Alternatives

## 9.1 Alternative A — Traditional VM or VPS Deployment

Application artifact is transferred to and executed on a manually or automatically managed VM or VPS.

### Advantages

* Relatively simple runtime model.
* Direct infrastructure control.
* Suitable for a single Spring Boot application.
* Low conceptual overhead.
* Can operate without container orchestration.
* Potentially low cost for a small application.

### Disadvantages

* Configuration drift risk.
* Manual operating-system maintenance.
* Runtime dependencies must be managed.
* Rollback may require custom scripts.
* Scaling and high availability require additional work.
* Security patching remains the operator’s responsibility.

### Status

```text
Considered — Not Selected
```

---

## 9.2 Alternative B — Container-based Deployment

Application is packaged or wrapped as a container and deployed to a container-capable runtime.

### Advantages

* Consistent runtime packaging.
* Improved environment reproducibility.
* Easier version replacement.
* Strong compatibility with future scaling.
* Clear runtime boundaries.

### Disadvantages

* Introduces container-image build and registry concerns.
* Current baseline produces an executable JAR, not an approved container image.
* Requires container host or managed container platform.
* Adds security and artifact-management decisions.
* May require additional CI/CD stages.

### Status

```text
Considered — Not Selected
```

---

## 9.3 Alternative C — Managed Application Platform

Application is deployed to a generic managed hosting or Platform-as-a-Service environment.

### Advantages

* Reduced infrastructure administration.
* Faster initial deployment.
* Built-in operational capabilities may be available.
* Platform may provide health checks, secret handling and rollback features.
* Suitable for a small project and limited operations team.

### Disadvantages

* Vendor-specific constraints.
* Pricing may grow with usage.
* Platform capabilities differ significantly.
* Database and migration behavior require platform-specific analysis.
* Portability may be reduced.

### Status

```text
Considered — Not Selected
```

No provider is selected by this ADR draft.

---

## 9.4 Alternative D — Kubernetes

Application is deployed to a Kubernetes cluster.

### Advantages

* Strong deployment primitives.
* Rolling, blue-green and canary patterns are possible.
* Rich health-probe support.
* Mature scaling and service orchestration.
* Broad industry adoption for distributed workloads.

### Disadvantages

* High operational complexity.
* Significant learning and maintenance overhead.
* Requires cluster governance.
* Requires container-image and registry baseline.
* Disproportionate for the current Foundation Phase.
* Increased infrastructure and observability requirements.

### Status

```text
Considered — Not Selected
```

---

# 10. Comparative Analysis

| Criterion               |         VM/VPS | Container Host |   Managed Platform |    Kubernetes |
| ----------------------- | -------------: | -------------: | -----------------: | ------------: |
| Initial simplicity      |           High |         Medium |               High |           Low |
| Operational control     |           High |           High |             Medium |          High |
| Operational overhead    | Medium to High |         Medium |      Low to Medium |     Very High |
| Environment consistency |         Medium |           High |               High |          High |
| Scaling capability      |         Medium |           High |     Medium to High |     Very High |
| Recovery automation     |         Medium |           High | Platform-dependent |     Very High |
| Cost predictability     |         Medium |         Medium | Platform-dependent | Low to Medium |
| Foundation suitability  |           High | Medium to High |               High |           Low |
| Vendor independence     |           High |           High |      Low to Medium |          High |
| Learning overhead       |         Medium |         Medium |      Low to Medium |     Very High |

---

# 11. Proposed Decision State

## 11.1 Final Decision

```text
PENDING GOVERNANCE DECISION
```

There is not enough approved evidence to select:

* A hosting provider.
* A runtime platform.
* A target environment.
* A deployment strategy.
* An automatic or manual trigger.
* A production deployment model.

No platform is approved by this draft ADR.

---

## 11.2 Direction Supported by Current Evidence

The evidence currently supports the following non-final direction:

* Begin with one deployable component rather than deploying the entire system at once.
* Prefer a non-production environment for the first deployment.
* Reuse the verified backend executable JAR produced by the approved artifact pipeline.
* Avoid rebuilding the application during deployment.
* Require immutable artifact identity.
* Require explicit approval before production deployment.
* Prefer the least operationally complex platform that satisfies security and recovery requirements.
* Avoid Kubernetes during the Foundation Phase unless new requirements justify it.
* Keep database migration separate from deployment success assumptions.
* Require health checks, post-deployment validation and evidence.

This direction is advisory only.

---

# 12. Required Baseline Decisions

The ADR cannot become `Accepted` until the following decisions are resolved.

| ID     | Required Decision                               | Current Status    |
| ------ | ----------------------------------------------- | ----------------- |
| CD-001 | Initial component: Backend, Frontend or both    | Open Decision     |
| CD-002 | First environment: non-production or production | Open Decision     |
| CD-003 | Hosting platform                                | Open Decision     |
| CD-004 | Runtime model                                   | Open Decision     |
| CD-005 | Deployment target                               | Open Decision     |
| CD-006 | Artifact source and retrieval method            | Partially Defined |
| CD-007 | Artifact integrity mechanism                    | Open Decision     |
| CD-008 | Trigger policy                                  | Open Decision     |
| CD-009 | Branch or tag policy                            | Open Decision     |
| CD-010 | Manual approval policy                          | Open Decision     |
| CD-011 | Environment protection policy                   | Open Decision     |
| CD-012 | Secret-storage mechanism                        | Open Decision     |
| CD-013 | Deployment authentication method                | Open Decision     |
| CD-014 | Workflow permission model                       | Open Decision     |
| CD-015 | Database migration execution model              | Open Decision     |
| CD-016 | Destructive migration policy                    | Open Decision     |
| CD-017 | Backup and restore policy                       | Open Decision     |
| CD-018 | Deployment strategy                             | Open Decision     |
| CD-019 | Health-check endpoints and semantics            | Open Decision     |
| CD-020 | Smoke-test policy                               | Open Decision     |
| CD-021 | Observation period                              | Open Decision     |
| CD-022 | Rollback and recovery policy                    | Open Decision     |
| CD-023 | Deployment owner                                | Open Decision     |
| CD-024 | Incident owner                                  | Open Decision     |
| CD-025 | Monitoring and alerting mechanism               | Open Decision     |
| CD-026 | Deployment evidence retention                   | Open Decision     |

---

# 13. Decisions Supported by Existing Baseline

The following constraints are supported by the current project baseline.

## 13.1 PostgreSQL

PostgreSQL is the approved database platform.

---

## 13.2 Flyway

Flyway is the approved database migration tool.

This does not determine how or when Flyway runs in a deployed environment.

---

## 13.3 PostgreSQL-backed Verification

Backend verification that starts the Spring Boot application context must run against PostgreSQL according to ADR-001.

---

## 13.4 Verified Backend Artifact

The backend Continuous Delivery pipeline can create and retain an executable Spring Boot JAR.

This artifact is the currently available deployment candidate.

---

## 13.5 No Continuous Deployment Baseline

Continuous Deployment remains `Not Started`.

No deployment target or runtime environment has been approved.

---

# 14. Artifact Policy

The approved implementation must follow these rules:

* Deploy only an artifact produced by the approved pipeline.
* Do not rebuild in the deployment stage.
* Associate the artifact with a full commit SHA.
* Associate the artifact with its CI workflow run.
* Use a unique artifact identifier.
* Preserve artifact metadata.
* Verify artifact retrieval before deployment.
* Do not deploy from an uncommitted local working tree.
* Do not silently substitute a different artifact.

Artifact checksum or signing remains an Open Decision.

---

# 15. Trigger and Release Policy

The following trigger models remain under consideration:

* Automatic deployment after merge.
* Deployment from an approved branch.
* Deployment from an immutable tag.
* Deployment from a formal release.
* Manual workflow dispatch.
* Scheduled deployment.
* Hybrid trigger by environment.

The ADR must ultimately define:

* Which actor can trigger deployment.
* Which branch, tag or release is eligible.
* Which environment can be targeted.
* Whether approval is required.
* Whether production deployment can be automatic.
* How emergency deployment is handled.

Current status:

```text
Open Decision
```

---

# 16. Security Policy Requirements

The final decision must define:

* Secret-storage mechanism.
* Environment-specific secret scope.
* Secret access ownership.
* Secret rotation responsibility.
* Deployment authentication method.
* Workflow token permissions.
* Environment protection.
* Approval authority.
* Audit-log requirements.
* Credential revocation procedure.

Required principles:

* No production secret in source control.
* No secret in artifacts.
* No secret in deployment evidence.
* No unrestricted administrative workflow identity.
* No reuse of production credentials in CI.
* No public exposure of detailed health information.
* Runtime and migration credentials should be separated when technically feasible.

Current status:

```text
Open Decision
```

---

# 17. Database Migration Policy Requirements

The final baseline must decide whether Flyway is executed through:

* Application startup.
* A dedicated migration job.
* Manual execution.
* A hybrid policy.

The policy must address:

* Migration ownership.
* Migration credentials.
* Backward compatibility.
* Forward-only changes.
* Destructive migration approval.
* Data migration.
* Backup requirements.
* Concurrent deployments.
* Failure handling.
* Schema verification.
* Migration evidence.

Current draft direction:

* Prefer backward-compatible migration.
* Prefer forward-fix over unsafe reverse migration.
* Do not modify an already-applied migration.
* Do not automatically assume application rollback restores database state.
* Consider a dedicated migration step or hybrid policy.

Current status:

```text
Open Decision
```

---

# 18. Health-check Policy Requirements

The approved deployment process must distinguish:

* Startup check.
* Liveness.
* Readiness.
* Dependency verification.
* Functional smoke test.
* Observation period.

Deployment success must require:

* Application startup completion.
* Liveness success.
* Readiness success.
* PostgreSQL connectivity where required.
* Migration verification where applicable.
* Smoke-test success.
* Stable operation during the observation period.
* Runtime release-identity verification.

Exact endpoints, timeouts, retry limits and observation duration remain Open Decisions.

---

# 19. Rollback and Recovery Policy Requirements

The final baseline must distinguish:

* Application rollback.
* Configuration rollback.
* Database recovery.
* Infrastructure recovery.
* Forward fix.
* Backup restore.
* Manual data repair.

The policy must not assume rollback is always safe.

Recovery must:

1. Preserve evidence when possible.
2. Identify the failed release.
3. Identify database migration state.
4. Select an explicit recovery strategy.
5. Restore service.
6. Re-run health checks.
7. Re-run smoke tests.
8. Complete an observation period.
9. Record final runtime state.

Current status:

```text
Open Decision
```

---

# 20. Operational Policy Requirements

The final Continuous Deployment Baseline must define:

* Deployment owner.
* Environment owner.
* Database migration owner.
* Approval authority.
* Incident owner.
* Rollback authority.
* Deployment concurrency.
* Maintenance window.
* Deployment cancellation behavior.
* Monitoring.
* Alert destination.
* Escalation path.
* Evidence retention.

Proposal direction:

* Allow only one active deployment per environment.
* Do not allow an application deployment to bypass a migration in progress.
* Do not mark a deployment successful before post-deployment validation.
* Treat cancelled and unknown states as requiring verification.
* Require a clear final environment state.

---

# 21. Required Evidence

Every future deployment must provide at least:

1. Repository.
2. Branch or tag.
3. Full commit SHA.
4. CI workflow reference.
5. Successful verification result.
6. Artifact name.
7. Artifact ID.
8. Artifact version.
9. Target environment.
10. Deployment target.
11. Deployment workflow reference.
12. Trigger identity.
13. Approval evidence where required.
14. Migration result where applicable.
15. Startup-check result.
16. Liveness result.
17. Readiness result.
18. Database verification.
19. Smoke-test result.
20. Observation-period result.
21. Runtime release identity.
22. Final deployment state.
23. Rollback or recovery evidence where applicable.
24. Sanitised deployment logs.

Evidence must not contain secret values.

---

# 22. Consequences

## 22.1 Positive Consequences

Once accepted, this ADR will:

* Prevent deployment implementation based on hidden assumptions.
* Establish a single baseline for deployment tasks.
* Improve security and permission control.
* Improve artifact traceability.
* Reduce deployment inconsistency.
* Define database migration boundaries.
* Require health-based validation.
* Improve rollback and recovery readiness.
* Improve auditability.
* Give Current Task Manager clear constraints for future deployment tasks.

---

## 22.2 Negative Consequences

The governance process introduces:

* Additional decisions before implementation.
* More documentation.
* Approval overhead.
* Possible delay before the first deployment.
* Additional evidence requirements.
* More operational responsibility.
* Possible need for follow-up tasks before deployment is possible.

These costs are accepted as necessary to prevent unmanaged production and infrastructure changes.

---

## 22.3 Risks if No ADR Is Approved

Without an approved Continuous Deployment ADR:

* Deployment platform may be chosen arbitrarily.
* Production may be targeted prematurely.
* Secrets may be handled inconsistently.
* Workflow permissions may be excessive.
* Migration behavior may be unsafe.
* Artifact traceability may be lost.
* Rollback may be assumed but not viable.
* Operational ownership may remain unclear.
* Deployment success may be declared without health evidence.
* Baseline changes may occur without approval.

---

# 23. Rejected Alternatives

No deployment platform is formally rejected by this draft because a final platform decision has not yet been approved.

However, the following approaches are rejected as governance practices:

## 23.1 Deploying Directly from Local Machine

Rejected because:

* Artifact provenance may be unclear.
* Local state may be uncommitted.
* Auditability is weak.
* Credentials may be uncontrolled.
* Deployment may bypass CI.

---

## 23.2 Rebuilding During Deployment

Rejected because:

* The deployed binary may differ from the verified artifact.
* Traceability is broken.
* Reproducibility is reduced.

---

## 23.3 Automatic Production Deployment Without Approved Controls

Rejected because:

* Production scope is not approved.
* Approval policy is not defined.
* Secret policy is not defined.
* Recovery policy is not defined.

---

## 23.4 Automatic Production Migration by Assumption

Rejected because:

* CI migration success does not authorize production migration.
* Runtime permissions and recovery policy are unresolved.
* Destructive migration risk is uncontrolled.

---

## 23.5 Treating Process Startup as Deployment Success

Rejected because:

* Startup does not prove readiness.
* Dependency failures may remain hidden.
* Functional behavior may be broken.

---

## 23.6 Selecting Kubernetes by Default

Rejected as a default assumption because:

* Current scale does not demonstrate a Kubernetes requirement.
* Operational complexity would increase significantly.
* Container and cluster baselines do not yet exist.

This does not prohibit future Kubernetes adoption if supported by approved requirements.

---

# 24. Implementation Constraints

After this ADR is approved, future implementation tasks must:

* Remain within the selected deployment scope.
* Use the selected platform.
* Use the approved environment.
* Use approved secret mechanisms.
* Use approved workflow permissions.
* Consume the verified artifact.
* Implement only the approved trigger policy.
* Apply the approved migration policy.
* Implement health and post-deployment checks.
* Generate required evidence.
* Avoid additional baseline changes unless separately approved.

A deployment implementation task must not silently resolve an ADR Open Decision.

---

# 25. Follow-up Governance Actions

Before this ADR can become `Accepted`, governance review must:

1. Confirm the official ADR identifier.
2. Select the initial deployable component.
3. Select the first environment.
4. Select the hosting and runtime model.
5. Select the deployment trigger.
6. Define branch or tag policy.
7. Define environment protection.
8. Define secret storage.
9. Define workflow authentication.
10. Define minimum permissions.
11. Define database migration policy.
12. Define health-check policy.
13. Define rollback and recovery policy.
14. Define operational ownership.
15. Define evidence retention.
16. Record accepted risks.
17. Record rejected deployment alternatives.
18. Approve final consequences and constraints.

---

# 26. Approval Conditions

This ADR must not be marked `Accepted` until:

* All blocking Open Decisions are resolved.
* The Selected Approach is explicit.
* The first target environment is explicit.
* The first deployment target is explicit.
* Secret and permissions policies are explicit.
* Database migration policy is explicit.
* Health validation is explicit.
* Recovery ownership is explicit.
* Project State Manager accepts the decision.
* The Decision Register is updated.
* Baseline References are updated.
* A new Official Project State version is issued.

---

# 27. Governance Outcome Options

## 27.1 Accepted

All blocking decisions are approved.

Continuous Deployment implementation tasks may become eligible.

---

## 27.2 Accepted with Follow-up Actions

Core baseline decisions are approved, while non-blocking tasks remain.

Implementation may proceed only inside explicitly approved boundaries.

---

## 27.3 Deferred

Important evidence or decisions are missing.

Continuous Deployment remains `Not Started`.

---

## 27.4 Rejected

The proposed architecture or governance approach is rejected.

The rationale must be recorded and alternatives reconsidered.

---

# 28. Current Recommendation

```text
Governance Artifact:
ADR

ADR Status:
Proposed

Decision Status:
Pending Governance Decision

Implementation Authorization:
Not Granted

Continuous Deployment:
Not Started
```

The Governance Proposal and this Draft ADR should be submitted to Project State Manager for review.

Project State Manager should either:

* Resolve the Open Decisions through governance review.
* Request additional evidence.
* Defer the ADR.
* Reject the ADR.
* Approve a completed revision after all blocking decisions have been resolved.

---

# 29. Compliance Declaration

This Draft ADR:

* Does not select a cloud provider.
* Does not select a deployment platform.
* Does not select production as the first environment.
* Does not authorize automatic deployment.
* Does not authorize Flyway on production.
* Does not create a workflow.
* Does not deploy an application.
* Does not provision infrastructure.
* Does not create secrets.
* Does not update Official Project State.
* Does not mark Continuous Deployment as completed.
* Does not self-approve the ADR.

---

# 30. Final Draft State

```text
ADR-002 — Continuous Deployment Baseline
Status: Proposed
Decision: Pending Governance Decision
Implementation: Not Authorized
Continuous Deployment: Not Started
```

# ADR-002 — Continuous Deployment Baseline

## 1. Metadata

| Field                | Value                                                    |
| -------------------- | -------------------------------------------------------- |
| Artifact Type        | Architecture Decision Record                             |
| ADR ID               | ADR-002 — Provisional                                    |
| Title                | Continuous Deployment Baseline                           |
| Status               | Proposed                                                 |
| Decision Status      | Pending Governance Decision                              |
| Source Task          | TASK-009 — Continuous Deployment Baseline Definition     |
| Source Project State | Official Project State v1.1.3                            |
| Current Phase        | Foundation                                               |
| Current Milestone    | Foundation Preparation                                   |
| Author               | Development Workspace                                    |
| Reviewer             | Project State Manager                                    |
| Approver             | Project State Manager or designated governance authority |
| Date                 | 2026-07-17                                               |
| Supersedes           | None                                                     |
| Superseded By        | None                                                     |

> `ADR-002` is a provisional identifier and must be confirmed against the official Decision Register before approval.

---

# 2. Status

```text
PROPOSED
```

This ADR has not been:

* Accepted.
* Approved.
* Added to the official Decision Register.
* Incorporated into Baseline References.
* Used to authorize deployment implementation.

Continuous Deployment remains:

```text
Not Started
```

---

# 3. Context

Official Project State v1.1.3 confirms that the following Foundation deliverables have been completed:

* Repository Initialization.
* Backend Bootstrap.
* Frontend Bootstrap.
* Database Foundation Setup.
* Docker Environment Setup.
* Backend Continuous Integration Pipeline Setup.
* Frontend Continuous Integration Pipeline Setup.
* Backend Continuous Delivery Artifact Pipeline Setup.

The backend pipeline can:

* Verify the backend using PostgreSQL.
* Package an executable Spring Boot JAR.
* Store the packaged JAR as a GitHub Actions artifact.
* Associate the artifact with a source revision and workflow run.

ADR-001 establishes that Spring Boot integration tests requiring application-context startup must use PostgreSQL through GitHub Actions service containers.

However, the project currently has no approved Continuous Deployment Baseline defining:

* Initial deployment scope.
* Target environment.
* Hosting platform.
* Runtime topology.
* Deployment trigger.
* Branch or tag policy.
* Approval policy.
* Secret-management policy.
* Workflow permissions.
* Database migration execution policy.
* Health-check policy.
* Rollback or recovery policy.
* Operational ownership.
* Required deployment evidence.

Creating a deployment workflow without these decisions would introduce unapproved Engineering, Security and Operations Baseline changes.

---

# 4. Problem Statement

The project has an approved mechanism for producing a verified backend artifact, but it has no approved mechanism for deploying that artifact into a runtime environment.

Continuous Deployment is not simply an additional GitHub Actions step.

It requires coordinated decisions involving:

* Application runtime.
* Infrastructure.
* Environment isolation.
* Authentication and authorization.
* Secrets.
* Database migration.
* Availability.
* Health verification.
* Failure recovery.
* Monitoring.
* Audit evidence.

Without a formal baseline, implementation would require Development Workspace to make architectural and operational assumptions beyond its authority.

The project therefore requires an approved Continuous Deployment architecture decision before any deployment implementation task can begin.

---

# 5. Decision Scope

This ADR is intended to establish the baseline for the first application deployment pipeline.

The decision must cover:

1. Initial deployable component.
2. Initial target environment.
3. Hosting and runtime model.
4. Artifact source.
5. Deployment trigger.
6. Branch or release policy.
7. Environment approval policy.
8. Secret-management mechanism.
9. Deployment authentication mechanism.
10. Workflow permissions.
11. Database migration policy.
12. Deployment strategy.
13. Health-check and validation policy.
14. Rollback and recovery policy.
15. Operational ownership.
16. Logging and evidence requirements.

---

# 6. Out of Scope

This ADR does not itself:

* Create a deployment workflow.
* Deploy the backend.
* Deploy the frontend.
* Provision a VM or server.
* Provision a container host.
* Configure Kubernetes.
* Create a cloud account.
* Create a cloud resource.
* Create a production database.
* Run Flyway on staging or production.
* Create production credentials.
* Configure domain, DNS or TLS.
* Publish a Docker image.
* Create a container registry.
* Create a GitHub Release.
* Modify backend source code.
* Modify frontend source code.
* Modify the Official Project State.
* Mark Continuous Deployment as completed.

Implementation must be performed through one or more future Current Tasks after this ADR is approved.

---

# 7. Decision Drivers

The decision must prioritize the project’s established quality goals:

1. Maintainability.
2. Scalability.
3. Performance.
4. Development speed.
5. Cost optimization.

Additional decision drivers are:

* Simplicity suitable for the Foundation Phase.
* Low operational overhead.
* Clear artifact traceability.
* PostgreSQL production parity.
* Least-privilege access.
* Secret isolation.
* Environment isolation.
* Controlled database migration.
* Reliable health verification.
* Recoverability.
* Auditability.
* Avoidance of unnecessary infrastructure complexity.
* Ability to evolve without immediate migration to distributed architecture.

---

# 8. Governance Principles

The approved Continuous Deployment Baseline must satisfy the following principles.

## 8.1 Verified Artifact Only

Deployment must consume an artifact that has already passed the required CI and Continuous Delivery checks.

The deployment stage must not rebuild the application.

---

## 8.2 Immutable Artifact

The artifact deployed must be identifiable and immutable.

The system must not rely solely on an ambiguous label such as:

```text
latest
```

without an associated immutable identifier.

---

## 8.3 Source-to-Runtime Traceability

Every deployment must support the traceability chain:

```text
Repository
→ Branch or Tag
→ Commit SHA
→ CI Workflow
→ Verified Artifact
→ Deployment Run
→ Target Environment
→ Runtime Release Identity
```

---

## 8.4 Environment Isolation

Secrets, credentials, configuration, databases and permissions must be separated by environment.

Production credentials must not be reused in:

* Local development.
* CI.
* Testing.
* Non-production environments.

---

## 8.5 Least Privilege

The deployment workflow must receive only the permissions necessary to deploy to the approved environment.

Runtime application credentials must not automatically receive database schema administration rights.

---

## 8.6 Explicit Database Migration Policy

Successful Flyway execution in CI does not authorize automatic Flyway execution in staging or production.

Database migration must follow an explicitly approved policy.

---

## 8.7 Health-based Deployment Success

A deployment must not be considered successful merely because:

* A process started.
* A container is running.
* A port opened.
* A workflow returned exit code zero.

Deployment success requires approved health checks and post-deployment validation.

---

## 8.8 Explicit Recovery State

A failed, cancelled or interrupted deployment must result in a known runtime state.

`State Unknown` must not be treated as successful.

---

## 8.9 Evidence Required

Every deployment, rollback and recovery must generate traceable and sanitised evidence.

---

# 9. Considered Alternatives

## 9.1 Alternative A — Traditional VM or VPS Deployment

Application artifact is transferred to and executed on a manually or automatically managed VM or VPS.

### Advantages

* Relatively simple runtime model.
* Direct infrastructure control.
* Suitable for a single Spring Boot application.
* Low conceptual overhead.
* Can operate without container orchestration.
* Potentially low cost for a small application.

### Disadvantages

* Configuration drift risk.
* Manual operating-system maintenance.
* Runtime dependencies must be managed.
* Rollback may require custom scripts.
* Scaling and high availability require additional work.
* Security patching remains the operator’s responsibility.

### Status

```text
Considered — Not Selected
```

---

## 9.2 Alternative B — Container-based Deployment

Application is packaged or wrapped as a container and deployed to a container-capable runtime.

### Advantages

* Consistent runtime packaging.
* Improved environment reproducibility.
* Easier version replacement.
* Strong compatibility with future scaling.
* Clear runtime boundaries.

### Disadvantages

* Introduces container-image build and registry concerns.
* Current baseline produces an executable JAR, not an approved container image.
* Requires container host or managed container platform.
* Adds security and artifact-management decisions.
* May require additional CI/CD stages.

### Status

```text
Considered — Not Selected
```

---

## 9.3 Alternative C — Managed Application Platform

Application is deployed to a generic managed hosting or Platform-as-a-Service environment.

### Advantages

* Reduced infrastructure administration.
* Faster initial deployment.
* Built-in operational capabilities may be available.
* Platform may provide health checks, secret handling and rollback features.
* Suitable for a small project and limited operations team.

### Disadvantages

* Vendor-specific constraints.
* Pricing may grow with usage.
* Platform capabilities differ significantly.
* Database and migration behavior require platform-specific analysis.
* Portability may be reduced.

### Status

```text
Considered — Not Selected
```

No provider is selected by this ADR draft.

---

## 9.4 Alternative D — Kubernetes

Application is deployed to a Kubernetes cluster.

### Advantages

* Strong deployment primitives.
* Rolling, blue-green and canary patterns are possible.
* Rich health-probe support.
* Mature scaling and service orchestration.
* Broad industry adoption for distributed workloads.

### Disadvantages

* High operational complexity.
* Significant learning and maintenance overhead.
* Requires cluster governance.
* Requires container-image and registry baseline.
* Disproportionate for the current Foundation Phase.
* Increased infrastructure and observability requirements.

### Status

```text
Considered — Not Selected
```

---

# 10. Comparative Analysis

| Criterion               |         VM/VPS | Container Host |   Managed Platform |    Kubernetes |
| ----------------------- | -------------: | -------------: | -----------------: | ------------: |
| Initial simplicity      |           High |         Medium |               High |           Low |
| Operational control     |           High |           High |             Medium |          High |
| Operational overhead    | Medium to High |         Medium |      Low to Medium |     Very High |
| Environment consistency |         Medium |           High |               High |          High |
| Scaling capability      |         Medium |           High |     Medium to High |     Very High |
| Recovery automation     |         Medium |           High | Platform-dependent |     Very High |
| Cost predictability     |         Medium |         Medium | Platform-dependent | Low to Medium |
| Foundation suitability  |           High | Medium to High |               High |           Low |
| Vendor independence     |           High |           High |      Low to Medium |          High |
| Learning overhead       |         Medium |         Medium |      Low to Medium |     Very High |

---

# 11. Proposed Decision State

## 11.1 Final Decision

```text
PENDING GOVERNANCE DECISION
```

There is not enough approved evidence to select:

* A hosting provider.
* A runtime platform.
* A target environment.
* A deployment strategy.
* An automatic or manual trigger.
* A production deployment model.

No platform is approved by this draft ADR.

---

## 11.2 Direction Supported by Current Evidence

The evidence currently supports the following non-final direction:

* Begin with one deployable component rather than deploying the entire system at once.
* Prefer a non-production environment for the first deployment.
* Reuse the verified backend executable JAR produced by the approved artifact pipeline.
* Avoid rebuilding the application during deployment.
* Require immutable artifact identity.
* Require explicit approval before production deployment.
* Prefer the least operationally complex platform that satisfies security and recovery requirements.
* Avoid Kubernetes during the Foundation Phase unless new requirements justify it.
* Keep database migration separate from deployment success assumptions.
* Require health checks, post-deployment validation and evidence.

This direction is advisory only.

---

# 12. Required Baseline Decisions

The ADR cannot become `Accepted` until the following decisions are resolved.

| ID     | Required Decision                               | Current Status    |
| ------ | ----------------------------------------------- | ----------------- |
| CD-001 | Initial component: Backend, Frontend or both    | Open Decision     |
| CD-002 | First environment: non-production or production | Open Decision     |
| CD-003 | Hosting platform                                | Open Decision     |
| CD-004 | Runtime model                                   | Open Decision     |
| CD-005 | Deployment target                               | Open Decision     |
| CD-006 | Artifact source and retrieval method            | Partially Defined |
| CD-007 | Artifact integrity mechanism                    | Open Decision     |
| CD-008 | Trigger policy                                  | Open Decision     |
| CD-009 | Branch or tag policy                            | Open Decision     |
| CD-010 | Manual approval policy                          | Open Decision     |
| CD-011 | Environment protection policy                   | Open Decision     |
| CD-012 | Secret-storage mechanism                        | Open Decision     |
| CD-013 | Deployment authentication method                | Open Decision     |
| CD-014 | Workflow permission model                       | Open Decision     |
| CD-015 | Database migration execution model              | Open Decision     |
| CD-016 | Destructive migration policy                    | Open Decision     |
| CD-017 | Backup and restore policy                       | Open Decision     |
| CD-018 | Deployment strategy                             | Open Decision     |
| CD-019 | Health-check endpoints and semantics            | Open Decision     |
| CD-020 | Smoke-test policy                               | Open Decision     |
| CD-021 | Observation period                              | Open Decision     |
| CD-022 | Rollback and recovery policy                    | Open Decision     |
| CD-023 | Deployment owner                                | Open Decision     |
| CD-024 | Incident owner                                  | Open Decision     |
| CD-025 | Monitoring and alerting mechanism               | Open Decision     |
| CD-026 | Deployment evidence retention                   | Open Decision     |

---

# 13. Decisions Supported by Existing Baseline

The following constraints are supported by the current project baseline.

## 13.1 PostgreSQL

PostgreSQL is the approved database platform.

---

## 13.2 Flyway

Flyway is the approved database migration tool.

This does not determine how or when Flyway runs in a deployed environment.

---

## 13.3 PostgreSQL-backed Verification

Backend verification that starts the Spring Boot application context must run against PostgreSQL according to ADR-001.

---

## 13.4 Verified Backend Artifact

The backend Continuous Delivery pipeline can create and retain an executable Spring Boot JAR.

This artifact is the currently available deployment candidate.

---

## 13.5 No Continuous Deployment Baseline

Continuous Deployment remains `Not Started`.

No deployment target or runtime environment has been approved.

---

# 14. Artifact Policy

The approved implementation must follow these rules:

* Deploy only an artifact produced by the approved pipeline.
* Do not rebuild in the deployment stage.
* Associate the artifact with a full commit SHA.
* Associate the artifact with its CI workflow run.
* Use a unique artifact identifier.
* Preserve artifact metadata.
* Verify artifact retrieval before deployment.
* Do not deploy from an uncommitted local working tree.
* Do not silently substitute a different artifact.

Artifact checksum or signing remains an Open Decision.

---

# 15. Trigger and Release Policy

The following trigger models remain under consideration:

* Automatic deployment after merge.
* Deployment from an approved branch.
* Deployment from an immutable tag.
* Deployment from a formal release.
* Manual workflow dispatch.
* Scheduled deployment.
* Hybrid trigger by environment.

The ADR must ultimately define:

* Which actor can trigger deployment.
* Which branch, tag or release is eligible.
* Which environment can be targeted.
* Whether approval is required.
* Whether production deployment can be automatic.
* How emergency deployment is handled.

Current status:

```text
Open Decision
```

---

# 16. Security Policy Requirements

The final decision must define:

* Secret-storage mechanism.
* Environment-specific secret scope.
* Secret access ownership.
* Secret rotation responsibility.
* Deployment authentication method.
* Workflow token permissions.
* Environment protection.
* Approval authority.
* Audit-log requirements.
* Credential revocation procedure.

Required principles:

* No production secret in source control.
* No secret in artifacts.
* No secret in deployment evidence.
* No unrestricted administrative workflow identity.
* No reuse of production credentials in CI.
* No public exposure of detailed health information.
* Runtime and migration credentials should be separated when technically feasible.

Current status:

```text
Open Decision
```

---

# 17. Database Migration Policy Requirements

The final baseline must decide whether Flyway is executed through:

* Application startup.
* A dedicated migration job.
* Manual execution.
* A hybrid policy.

The policy must address:

* Migration ownership.
* Migration credentials.
* Backward compatibility.
* Forward-only changes.
* Destructive migration approval.
* Data migration.
* Backup requirements.
* Concurrent deployments.
* Failure handling.
* Schema verification.
* Migration evidence.

Current draft direction:

* Prefer backward-compatible migration.
* Prefer forward-fix over unsafe reverse migration.
* Do not modify an already-applied migration.
* Do not automatically assume application rollback restores database state.
* Consider a dedicated migration step or hybrid policy.

Current status:

```text
Open Decision
```

---

# 18. Health-check Policy Requirements

The approved deployment process must distinguish:

* Startup check.
* Liveness.
* Readiness.
* Dependency verification.
* Functional smoke test.
* Observation period.

Deployment success must require:

* Application startup completion.
* Liveness success.
* Readiness success.
* PostgreSQL connectivity where required.
* Migration verification where applicable.
* Smoke-test success.
* Stable operation during the observation period.
* Runtime release-identity verification.

Exact endpoints, timeouts, retry limits and observation duration remain Open Decisions.

---

# 19. Rollback and Recovery Policy Requirements

The final baseline must distinguish:

* Application rollback.
* Configuration rollback.
* Database recovery.
* Infrastructure recovery.
* Forward fix.
* Backup restore.
* Manual data repair.

The policy must not assume rollback is always safe.

Recovery must:

1. Preserve evidence when possible.
2. Identify the failed release.
3. Identify database migration state.
4. Select an explicit recovery strategy.
5. Restore service.
6. Re-run health checks.
7. Re-run smoke tests.
8. Complete an observation period.
9. Record final runtime state.

Current status:

```text
Open Decision
```

---

# 20. Operational Policy Requirements

The final Continuous Deployment Baseline must define:

* Deployment owner.
* Environment owner.
* Database migration owner.
* Approval authority.
* Incident owner.
* Rollback authority.
* Deployment concurrency.
* Maintenance window.
* Deployment cancellation behavior.
* Monitoring.
* Alert destination.
* Escalation path.
* Evidence retention.

Proposal direction:

* Allow only one active deployment per environment.
* Do not allow an application deployment to bypass a migration in progress.
* Do not mark a deployment successful before post-deployment validation.
* Treat cancelled and unknown states as requiring verification.
* Require a clear final environment state.

---

# 21. Required Evidence

Every future deployment must provide at least:

1. Repository.
2. Branch or tag.
3. Full commit SHA.
4. CI workflow reference.
5. Successful verification result.
6. Artifact name.
7. Artifact ID.
8. Artifact version.
9. Target environment.
10. Deployment target.
11. Deployment workflow reference.
12. Trigger identity.
13. Approval evidence where required.
14. Migration result where applicable.
15. Startup-check result.
16. Liveness result.
17. Readiness result.
18. Database verification.
19. Smoke-test result.
20. Observation-period result.
21. Runtime release identity.
22. Final deployment state.
23. Rollback or recovery evidence where applicable.
24. Sanitised deployment logs.

Evidence must not contain secret values.

---

# 22. Consequences

## 22.1 Positive Consequences

Once accepted, this ADR will:

* Prevent deployment implementation based on hidden assumptions.
* Establish a single baseline for deployment tasks.
* Improve security and permission control.
* Improve artifact traceability.
* Reduce deployment inconsistency.
* Define database migration boundaries.
* Require health-based validation.
* Improve rollback and recovery readiness.
* Improve auditability.
* Give Current Task Manager clear constraints for future deployment tasks.

---

## 22.2 Negative Consequences

The governance process introduces:

* Additional decisions before implementation.
* More documentation.
* Approval overhead.
* Possible delay before the first deployment.
* Additional evidence requirements.
* More operational responsibility.
* Possible need for follow-up tasks before deployment is possible.

These costs are accepted as necessary to prevent unmanaged production and infrastructure changes.

---

## 22.3 Risks if No ADR Is Approved

Without an approved Continuous Deployment ADR:

* Deployment platform may be chosen arbitrarily.
* Production may be targeted prematurely.
* Secrets may be handled inconsistently.
* Workflow permissions may be excessive.
* Migration behavior may be unsafe.
* Artifact traceability may be lost.
* Rollback may be assumed but not viable.
* Operational ownership may remain unclear.
* Deployment success may be declared without health evidence.
* Baseline changes may occur without approval.

---

# 23. Rejected Alternatives

No deployment platform is formally rejected by this draft because a final platform decision has not yet been approved.

However, the following approaches are rejected as governance practices:

## 23.1 Deploying Directly from Local Machine

Rejected because:

* Artifact provenance may be unclear.
* Local state may be uncommitted.
* Auditability is weak.
* Credentials may be uncontrolled.
* Deployment may bypass CI.

---

## 23.2 Rebuilding During Deployment

Rejected because:

* The deployed binary may differ from the verified artifact.
* Traceability is broken.
* Reproducibility is reduced.

---

## 23.3 Automatic Production Deployment Without Approved Controls

Rejected because:

* Production scope is not approved.
* Approval policy is not defined.
* Secret policy is not defined.
* Recovery policy is not defined.

---

## 23.4 Automatic Production Migration by Assumption

Rejected because:

* CI migration success does not authorize production migration.
* Runtime permissions and recovery policy are unresolved.
* Destructive migration risk is uncontrolled.

---

## 23.5 Treating Process Startup as Deployment Success

Rejected because:

* Startup does not prove readiness.
* Dependency failures may remain hidden.
* Functional behavior may be broken.

---

## 23.6 Selecting Kubernetes by Default

Rejected as a default assumption because:

* Current scale does not demonstrate a Kubernetes requirement.
* Operational complexity would increase significantly.
* Container and cluster baselines do not yet exist.

This does not prohibit future Kubernetes adoption if supported by approved requirements.

---

# 24. Implementation Constraints

After this ADR is approved, future implementation tasks must:

* Remain within the selected deployment scope.
* Use the selected platform.
* Use the approved environment.
* Use approved secret mechanisms.
* Use approved workflow permissions.
* Consume the verified artifact.
* Implement only the approved trigger policy.
* Apply the approved migration policy.
* Implement health and post-deployment checks.
* Generate required evidence.
* Avoid additional baseline changes unless separately approved.

A deployment implementation task must not silently resolve an ADR Open Decision.

---

# 25. Follow-up Governance Actions

Before this ADR can become `Accepted`, governance review must:

1. Confirm the official ADR identifier.
2. Select the initial deployable component.
3. Select the first environment.
4. Select the hosting and runtime model.
5. Select the deployment trigger.
6. Define branch or tag policy.
7. Define environment protection.
8. Define secret storage.
9. Define workflow authentication.
10. Define minimum permissions.
11. Define database migration policy.
12. Define health-check policy.
13. Define rollback and recovery policy.
14. Define operational ownership.
15. Define evidence retention.
16. Record accepted risks.
17. Record rejected deployment alternatives.
18. Approve final consequences and constraints.

---

# 26. Approval Conditions

This ADR must not be marked `Accepted` until:

* All blocking Open Decisions are resolved.
* The Selected Approach is explicit.
* The first target environment is explicit.
* The first deployment target is explicit.
* Secret and permissions policies are explicit.
* Database migration policy is explicit.
* Health validation is explicit.
* Recovery ownership is explicit.
* Project State Manager accepts the decision.
* The Decision Register is updated.
* Baseline References are updated.
* A new Official Project State version is issued.

---

# 27. Governance Outcome Options

## 27.1 Accepted

All blocking decisions are approved.

Continuous Deployment implementation tasks may become eligible.

---

## 27.2 Accepted with Follow-up Actions

Core baseline decisions are approved, while non-blocking tasks remain.

Implementation may proceed only inside explicitly approved boundaries.

---

## 27.3 Deferred

Important evidence or decisions are missing.

Continuous Deployment remains `Not Started`.

---

## 27.4 Rejected

The proposed architecture or governance approach is rejected.

The rationale must be recorded and alternatives reconsidered.

---

# 28. Current Recommendation

```text
Governance Artifact:
ADR

ADR Status:
Proposed

Decision Status:
Pending Governance Decision

Implementation Authorization:
Not Granted

Continuous Deployment:
Not Started
```

The Governance Proposal and this Draft ADR should be submitted to Project State Manager for review.

Project State Manager should either:

* Resolve the Open Decisions through governance review.
* Request additional evidence.
* Defer the ADR.
* Reject the ADR.
* Approve a completed revision after all blocking decisions have been resolved.

---

# 29. Compliance Declaration

This Draft ADR:

* Does not select a cloud provider.
* Does not select a deployment platform.
* Does not select production as the first environment.
* Does not authorize automatic deployment.
* Does not authorize Flyway on production.
* Does not create a workflow.
* Does not deploy an application.
* Does not provision infrastructure.
* Does not create secrets.
* Does not update Official Project State.
* Does not mark Continuous Deployment as completed.
* Does not self-approve the ADR.

---

# 30. Final Draft State

```text
ADR-002 — Continuous Deployment Baseline
Status: Proposed
Decision: Pending Governance Decision
Implementation: Not Authorized
Continuous Deployment: Not Started
```

