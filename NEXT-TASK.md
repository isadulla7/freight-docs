# Next task

Backend Foundation (phase 3) and Local Infrastructure (phase 4) are both COMPLETE. Identity/Auth (phase 5) is IN PROGRESS.

- **Current phase:** Identity/Auth
- **Last completed checkpoint:** `5.4` `Authenticate` public application API ([freight-backend#17](https://github.com/isadulla7/freight-backend/pull/17), merged `63774abf2e1e1239e34bd77ee0003b5dc213158e`)
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

## Proposed next bounded subtask (5.5): remaining session-management public API

Thin public wrappers over the already-built `SessionLifecycleService` (5.3), following the same shape as `Authenticate` (5.4) — same file, same visibility discipline (public use-case class, internal collaborators):

- `RefreshSession(rawRefreshToken)`: wraps `SessionLifecycleService.refresh`, translating its internal `RefreshResult` into a public result type.
- `RevokeSession(sessionId)`, `RevokeAllUserSessions(authIdentityId)`, `ListUserSessions(authIdentityId)`: thin wrappers over the corresponding `SessionLifecycleService` methods; `ListUserSessions` should return a public-safe view (no raw token/hash data) rather than the internal `AuthSession` entity directly.

Still deferred (do not implement yet):

- `VerifyOtpAndRegister` (registration) — needs `accounts.ProvisionUser`, which does not exist (`accounts` is still just `package-info.kt`, Phase 6, out of Phase 5 scope)
- `ResolveAuthenticatedPrincipal` (Spring Security integration) — re-evaluate scope/dependencies against module-boundaries.md when reached
- Any HTTP endpoint — a separate, later subtask category

Confirm this scope against the then-current repo state before coding — do not assume it is still correct if the repository has changed since this was written.
