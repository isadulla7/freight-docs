# Task 005: Identity/Auth

| Field | Value |
|---|---|
| Status | **NEXT** |
| Phase | `[5] Identity/Auth` |
| Prior gate | Backend Foundation (phase 3) and Local Infrastructure (phase 4) reviewed and COMPLETE |
| Architecture baseline | [`architecture-context-v1.md`](../architecture/architecture-context-v1.md), version `v1.0` |

## Objective

Implement the `identity` module's authentication/session capability in `freight-backend`, per the accepted [module boundaries](../docs/architecture/module-boundaries.md) (`identity` section), [ADR-0005](../docs/architecture/decisions/0005-authentication-and-sessions.md), and [ADR-0015](../docs/architecture/decisions/0015-authorization-and-audit.md).

This file is only the next-phase task specification. It does not select or execute a bounded subtask — the exact bounded first subtask must be proposed from the current repository state and confirmed before any coding, the same way every Backend Foundation and Local Infrastructure subtask was.

## Scope

- `identity` module: `AuthenticationIdentity`, `AuthSession`, `AuthDevice` and the public application API defined in module-boundaries.md (`VerifyOtpAndRegister`, `Authenticate`, `RefreshSession`, `RevokeSession`, `RevokeAllUserSessions`, `ListUserSessions`, `ResolveAuthenticatedPrincipal`).
- OTP challenge state and rate-limit keys in Redis, per the accepted data ownership split (not a PostgreSQL aggregate).
- Flyway migrations for `identity.auth_identities`, `identity.auth_sessions`, `identity.auth_devices` only.

## Out of scope

- Profile, role/permission, company, vehicle, load, or shipment decisions; business authorization (explicitly forbidden to `identity` per module-boundaries.md)
- Any other business/domain module implementation (accounts, fleet, freight, marketplace, shipment, communication)
- Raw OTP/token logging or persistent plaintext storage
- Production or cloud deployment topology, Kubernetes
- OpenAPI/contract integration into `freight-backend` (owned by `freight-contracts` per ADR-0007)
- Changes to accepted Architecture v1.0 decisions without a separately reviewed ADR

## Dependencies

- Backend Foundation (phase 3, `freight-docs` task 003) is COMPLETE.
- Local Infrastructure (phase 4, `freight-docs` task 004) is COMPLETE — PostgreSQL/PostGIS and Redis are available for local development.
- Each subtask starts from the then-current `freight-backend` GitHub `main` with no unresolved conflicting pull request.

## Acceptance criteria (per subtask)

- Each subtask ships as its own small, reviewed pull request in `freight-backend`.
- Each subtask preserves Spring Modulith module boundaries (`identity`'s allowed-dependency set stays `accounts` only, per `ArchitectureTests`), deny-by-default security, and Flyway as the sole schema authority.
- Raw OTP values, tokens, and passwords are never logged or stored in plaintext.
- No subtask under this task introduces business/domain code from other modules.

## Required validation

- A reviewed pull request in `freight-backend` covering the subtask's exact scope.
- The subtask's own validation commands (existing and new tests, build, CI pipeline) pass.
- Security-sensitive behavior (session revocation, rate limiting, non-disclosure on lookup) is covered by tests, not just implemented.

## Expected Git output

- Implementation happens in `freight-backend`, not in `freight-docs`.
- This file is updated as each subtask starts and completes, the same way `tasks/003-backend-foundation.md` and `tasks/004-local-infrastructure.md` tracked their checkpoints.

## Subtask checkpoints

| Checkpoint | Scope | Status | PR |
| --- | --- | --- | --- |
| 5.1 | Identity persistence foundation — Flyway `V2` migration for `identity.auth_identities`/`auth_devices`/`auth_sessions`, matching JPA entities/repositories, no cross-schema FKs, refresh tokens stored as hashes only, no application services/endpoints yet | **COMPLETE** | [freight-backend#14](https://github.com/isadulla7/freight-backend/pull/14) (merged `aad4bd16d8a5c5a5608ceb73d8299595ccf7bd56`) |
| 5.2 | OTP challenge and rate-limit Redis primitives — `OtpChallengeStore` (salted-hash, single-use, TTL-bound OTP challenge under `identity:otp:challenge:{phone}`) and `OtpRateLimiter` (fixed-window abuse-protection counter under `identity:otp:rate-limit:{phone}`); raw OTP never stored; no application services/endpoints/accounts dependency yet | **COMPLETE** | [freight-backend#15](https://github.com/isadulla7/freight-backend/pull/15) (merged `832097ef6e94a7eb198909ddb5af8b120ac6fe56`) |
| 5.3 | Session lifecycle domain service — `SessionLifecycleService` (issue/refresh-with-rotation/reuse-detection/revoke/revokeAll/list) on top of the 5.1 `AuthSession`/`AuthDevice` persistence; accounts-independent, no HTTP endpoints | **COMPLETE** | [freight-backend#16](https://github.com/isadulla7/freight-backend/pull/16) (merged `4d72de6711b8c5ae274f62ac7c6c7a5cc60a03e8`) |
| 5.4 | `Authenticate` public application API — composes `OtpChallengeStore` verification (5.2) + `SessionLifecycleService.issue` (5.3) for an already-registered identity; rate-limited; non-disclosure on failure | **IN PROGRESS** | [freight-backend#17](https://github.com/isadulla7/freight-backend/pull/17) |
