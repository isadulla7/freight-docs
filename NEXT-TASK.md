# Next task

Backend Foundation (phase 3) and Local Infrastructure (phase 4) are both COMPLETE. Identity/Auth (phase 5) is IN PROGRESS.

- **Current phase:** Identity/Auth
- **Last completed checkpoint:** `5.2` OTP challenge and rate-limit Redis primitives ([freight-backend#15](https://github.com/isadulla7/freight-backend/pull/15), merged `832097ef6e94a7eb198909ddb5af8b120ac6fe56`)
- **Task specification:** [tasks/005-identity-auth.md](tasks/005-identity-auth.md)
- **Implementation repository:** [isadulla7/freight-backend](https://github.com/isadulla7/freight-backend)

Before implementation:

1. Read `AGENTS.md`
2. Read `PROJECT-STATUS.md`
3. Read `HANDOFF.md`
4. Read `tasks/005-identity-auth.md`
5. Re-read the `identity` section of [module-boundaries.md](docs/architecture/module-boundaries.md), [ADR-0005](docs/architecture/decisions/0005-authentication-and-sessions.md), and [ADR-0015](docs/architecture/decisions/0015-authorization-and-audit.md)
6. Inspect the current `freight-backend` GitHub `main`
7. Confirm open PR state

Do not rely on previous chat history.

Do not skip ahead into other business modules (accounts, fleet, freight, marketplace, shipment, communication).

## Proposed next bounded subtask (5.3): session lifecycle domain service

Internal (no HTTP endpoints yet) domain/application-layer logic in `identity`, built on the `AuthSession`/`AuthDevice` persistence from checkpoint 5.1:

- Issue a session for an already-resolved `AuthenticationIdentity` id: generate a high-entropy random refresh token, persist only its hash (same pattern as the OTP hash from 5.2), return the raw token to the caller transiently (never logged/persisted).
- Refresh: look up by token hash, validate `ACTIVE` + not expired, rotate (issue a new token, mark the old session `REVOKED`). Per ADR-0005 "reuse detection": presenting a hash that maps to an already-`REVOKED` session is treated as possible token theft and should revoke all sessions for that identity.
- Revoke one session, revoke all sessions for an identity, list sessions for an identity.

This intentionally does not implement `VerifyOtpAndRegister` (needs `accounts.ProvisionUser`, which does not exist yet — `accounts` is still just `package-info.kt` and is out of Phase 5 scope) or any HTTP endpoint. It is scoped to the identity-only, accounts-independent slice of ADR-0005's session lifecycle requirements.

Confirm this scope against the then-current repo state before coding — do not assume it is still correct if the repository has changed since this was written.
