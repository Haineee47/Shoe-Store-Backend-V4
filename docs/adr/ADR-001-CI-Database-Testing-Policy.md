# ADR-001 — CI Database Testing Policy

## Metadata

| Field | Value |
|-------|-------|
| ADR ID | ADR-001 |
| Title | CI Database Testing Policy |
| Status | Approved |
| Decision Type | Engineering Baseline |
| Version | 1.0 |
| Author | Development Workspace |
| Reviewer | Project State Manager |
| Approver | Project State Manager |
| Created Date | 2026-07-17 |
| Approved Date | 2026-07-17 |
| Supersedes | None |
| Superseded By | None |

---

# Executive Summary

This ADR defines the official database testing policy for Backend Continuous Integration.

Integration tests that bootstrap the complete Spring Boot Application Context shall execute against a real PostgreSQL runtime provided by GitHub Actions Service Containers.

The objective is to preserve production parity, validate Flyway migrations during CI and prevent environment-specific failures from escaping into later stages.

This ADR resolves the governance dependency identified during TASK-006.

---

# Problem Statement

Backend Continuous Integration failed because integration tests bootstrapped the complete Spring Boot Application Context while no PostgreSQL runtime was available.

Flyway migration initialization failed before test execution.

As a result, Maven verification could not complete successfully.

---

# Background

TASK-006 introduced the backend CI workflow.

Validation demonstrated that:

- Spring Boot integration tests require PostgreSQL.
- Flyway executes during context initialization.
- GitHub-hosted runners do not provide PostgreSQL by default.

A permanent engineering baseline decision is therefore required.

---

# Constraints

- No production database shall be used.
- Flyway validation shall remain enabled.
- Integration tests shall validate production database behavior.
- No change to Technology Baseline.
- No change to Security Baseline.
- No change to Architecture Baseline.

---

# Evidence

- TASK-006 CI validation.
- Backend CI Baseline v1.0.
- Maven verification failure caused by missing PostgreSQL runtime.


---

# Decision Drivers

## Business Drivers

- Stable CI pipeline.
- Reliable release quality.

## Technical Drivers

- Validate PostgreSQL-specific behavior.
- Validate Flyway migrations.

## Security Drivers

- Isolated test credentials.
- No production secrets.

## Operational Drivers

- Deterministic CI execution.

## Quality Attributes

- Reliability.
- Repeatability.
- Production parity.

---

# Options Considered

## Option A

### Description

Use GitHub Actions PostgreSQL Service Container.

### Advantages

- Matches production database engine.
- Executes Flyway normally.
- Detects PostgreSQL-specific issues.
- High production parity.

### Disadvantages

- Longer startup time.
- Additional CI configuration.

### Risks

- Service readiness must be managed.

---

## Option B

### Description

Use H2 compatibility mode.

### Advantages

- Fast startup.

### Disadvantages

- Does not fully emulate PostgreSQL.
- Flyway and SQL behavior may differ.

### Risks

- Production-only failures remain undetected.

---

## Option C

### Description

Disable Flyway or skip integration tests.

### Advantages

- Fast pipeline.

### Disadvantages

- Eliminates migration validation.
- Reduces confidence in CI.

### Risks

- Invalid schema changes may reach production.

---

# Decision

## Selected Option

Option A

## Decision Statement

All CI integration tests that require a full Spring Boot Application Context shall execute against a PostgreSQL runtime provided by GitHub Actions Service Containers.

## Policy Requirements

- Flyway migrations shall execute normally during CI.
- Flyway shall not be disabled to make CI succeed.
- H2 compatibility mode shall not be used for integration tests.
- Integration tests shall validate the same database technology used in production.
- CI shall not skip integration tests to avoid PostgreSQL dependency.
- PostgreSQL Service Container represents only the CI runtime.
- CI credentials shall be dedicated test credentials.
- Production credentials shall never be used.

## Rationale

Using the production database engine during CI provides the highest confidence while preserving deterministic validation.

---

# Scope

Applies only to:

- Integration tests that bootstrap the complete Spring Boot Application Context.

Does not apply to:

- Unit tests.
- Deployment.
- Production database operations.
- Runtime infrastructure.

---

# Rejected Alternatives

## H2 Compatibility Mode

Rejected because it cannot guarantee production-equivalent PostgreSQL behavior.

## Disable Flyway

Rejected because migration validation is a mandatory CI quality gate.

## Skip Tests

Rejected because CI must verify application correctness.

---

# Trade-offs

- Increased CI startup time.
- Dependency on PostgreSQL Service Container.
- Additional readiness and health-check management.
- Slightly higher pipeline operational complexity.

---

# Consequences

## Positive

- Production parity.
- Real Flyway validation.
- PostgreSQL-specific verification.
- Earlier defect detection.
- Higher confidence in integration testing.

## Negative

- Longer pipeline execution.
- Additional CI resources.

## Migration Impact

None.

## Operational Impact

CI must provision PostgreSQL Service Container before integration tests.

## Documentation Impact

Decision Register shall reference ADR-001 after approval.

## Testing Impact

Integration tests require PostgreSQL runtime.

---

# Implementation Guidance

## Required Changes

After ADR approval:

- Update backend CI workflow.
- Add PostgreSQL Service Container.
- Configure CI test database credentials.
- Preserve Flyway execution.

## Out of Scope

- Production deployment.
- Docker production configuration.
- Runtime database architecture.
- Backend source code refactoring.

---

# Validation

This ADR confirms:

- GitHub Actions PostgreSQL Service Container is the selected approach.
- Flyway remains enabled.
- H2 is rejected.
- Skip tests is rejected.
- Policy applies only to Spring Boot integration tests.

---

# Rollback

Rollback requires a new ADR that supersedes ADR-001.

---

# Traceability

| Item | Reference |
|------|-----------|
| Project State | Official Project State v1.0.5 |
| Task | TASK-006 |
| Governance Task | Generated from TASK-006 review |
| Requirement | CI Database Testing Policy |
| Related ADR | None |
| Change Request | None |

---

# Review History

Reviewer: Pending

Review Result: Pending

Comments: Pending

Approval Date: Pending
