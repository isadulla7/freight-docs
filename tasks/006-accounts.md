# Task 006: Accounts

| Field | Value |
|---|---|
| Status | **IN PROGRESS** |
| Phase | `[6] Accounts` |
| Prior gate | Identity/Auth (phase 5) accounts-independent slice COMPLETE |
| Architecture baseline | [`architecture-context-v1.md`](../architecture/architecture-context-v1.md), version `v1.0` |

## Objective

Implement the `accounts` module in `freight-backend`, per the accepted [module boundaries](../docs/architecture/module-boundaries.md) (`accounts` section), [ADR-0015](../docs/architecture/decisions/0015-authorization-and-audit.md), and the [domain model](../docs/architecture/domain-model.md) (`Accounts` section).

This file is the task specification. The exact bounded first subtask must be proposed from the current repository state and confirmed before any coding, the same way every prior phase tracked its checkpoints.

## Scope

- `accounts` module: `User`, `DriverProfile`, `Company` + `CompanyMember`, `Role`, `Permission`, driver document/verification metadata, consent records, security audit log.
- Public application API defined in module-boundaries.md: `ProvisionUser`, `GetUserSummary`, `RecordConsent`, `CreateOrUpdateDriverProfile`, `CreateCompany`, `AddCompanyMember`, `RemoveCompanyMember`, `AssignUserRole`, `AssignCompanyMemberRole`, `Authorize`, `GetDriverEligibility`, `AppendSecurityAudit`.
- Published events: `CompanyMemberAdded`, `CompanyMemberRemoved`, `DriverVerificationChanged`.
- Flyway migrations for all `accounts.*` tables per database-erd.md.
- Once `ProvisionUser` exists, wire `identity.VerifyOtpAndRegister` to call it (completing Phase 5's last remaining public API member).

## Out of scope

- OTP/session lifecycle (identity module)
- Vehicle, load, offer, shipment, or communication domain implementation
- HTTP endpoints for accounts (separate later subtask, same as identity)
- Admin UI or dashboard
- Production or cloud deployment topology, Kubernetes
- Changes to accepted Architecture v1.0 decisions without a separately reviewed ADR

## Dependencies

- Identity/Auth (phase 5) accounts-independent slice is COMPLETE.
- `accounts` schema already exists (V1 migration).
- Each subtask starts from the then-current `freight-backend` GitHub `main` with no unresolved conflicting pull request.

## Acceptance criteria (per subtask)

- Each subtask ships as its own small, reviewed pull request in `freight-backend`.
- Each subtask preserves Spring Modulith module boundaries (`accounts`'s allowed-dependency set stays empty, per `ArchitectureTests`), deny-by-default security, and Flyway as the sole schema authority.
- No subtask under this task introduces business/domain code from other modules.

## Required validation

- A reviewed pull request in `freight-backend` covering the subtask's exact scope.
- The subtask's own validation commands (existing and new tests, build, CI pipeline) pass.
- Security-sensitive behavior (authorization, audit logging, consent) is covered by tests, not just implemented.

## Expected Git output

- Implementation happens in `freight-backend`, not in `freight-docs`.
- This file is updated as each subtask starts and completes, the same way prior task files tracked their checkpoints.

## Subtask checkpoints

| Checkpoint | Scope | Status | PR |
| --- | --- | --- | --- |
| 6.1 | Accounts persistence foundation -- Flyway `V3` migration for `accounts.users`, `user_consents`, `driver_profiles`, `driver_documents`, `driver_verifications`, `companies`, `company_members`, `roles`, `permissions`, `role_permissions`, `user_roles`, `company_member_roles`, `security_audit_log`; matching JPA entities/repositories; no cross-schema FKs; no application services/endpoints yet | **COMPLETE** | [#21](https://github.com/isadulla7/freight-backend/pull/21) |
| 6.2 | `ProvisionUser` public application API -- internal domain service to create an `accounts.User` row (status ACTIVE, auto-generated UUID); public `ProvisionUser` function callable from `identity` module; once wired, implement `identity.VerifyOtpAndRegister` to call it; Testcontainers integration tests | **NEXT** | — |
