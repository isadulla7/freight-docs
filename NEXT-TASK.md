# Next task

Backend Foundation (phase 3) and Local Infrastructure (phase 4) are both COMPLETE. Identity/Auth (phase 5) is IN PROGRESS.

- **Current phase:** Identity/Auth
- **Last completed checkpoint:** `5.3` Session lifecycle domain service ([freight-backend#16](https://github.com/isadulla7/freight-backend/pull/16), merged `4d72de6711b8c5ae274f62ac7c6c7a5cc60a03e8`)
- **In flight:** `5.4` `Authenticate` public application API — [freight-backend#17](https://github.com/isadulla7/freight-backend/pull/17) (open, CI pending at time of writing)
- **Task specification:** [tasks/005-identity-auth.md](tasks/005-identity-auth.md)
- **Implementation repository:** [isadulla7/freight-backend](https://github.com/isadulla7/freight-backend)

Before implementation:

1. Read `AGENTS.md`
2. Read `PROJECT-STATUS.md`
3. Read `HANDOFF.md`
4. Read `tasks/005-identity-auth.md`
5. Re-read the `identity` section of [module-boundaries.md](docs/architecture/module-boundaries.md), [ADR-0005](docs/architecture/decisions/0005-authentication-and-sessions.md), and [ADR-0015](docs/architecture/decisions/0015-authorization-and-audit.md)
6. Inspect the current `freight-backend` GitHub `main`
7. Confirm open PR state — check whether freight-backend#17 has merged before starting new work

Do not rely on previous chat history.

Do not skip ahead into other business modules (accounts, fleet, freight, marketplace, shipment, communication).

## After 5.4 merges: proposed subtask 5.5

Public `RefreshSession`, `RevokeSession`, `RevokeAllUserSessions`, `ListUserSessions` wrappers over the already-built `SessionLifecycleService` (5.3), following the same thin-facade shape as `Authenticate` (5.4). These complete the accounts-independent slice of the `identity` public application API.

Still deferred until `accounts.ProvisionUser` exists (Phase 6, out of Phase 5 scope): `VerifyOtpAndRegister` (registration) and `ResolveAuthenticatedPrincipal` (Spring Security integration — may or may not need accounts, re-evaluate against module-boundaries.md when reached). No HTTP endpoints have been wired up yet for anything in `identity`; that remains a separate, later subtask category.

Confirm this scope against the then-current repo state before coding.
