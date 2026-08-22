# Next task

Phase 5 (Identity/Auth) accounts-independent slice and Phase 6 checkpoint 6.1 (accounts persistence foundation) are COMPLETE and merged.

- **Current phase:** Accounts (phase 6)
- **Last completed checkpoint:** `6.1` Accounts persistence foundation (Flyway V3 + JPA entities)
- **Next checkpoint:** `6.2` `ProvisionUser` public application API
- **Task specification:** [tasks/006-accounts.md](tasks/006-accounts.md)
- **Implementation repository:** [isadulla7/freight-backend](https://github.com/isadulla7/freight-backend)

Before any implementation:

1. Read `AGENTS.md`
2. Read `PROJECT-STATUS.md`
3. Read `HANDOFF.md`
4. Read `tasks/006-accounts.md` in full
5. Inspect the current `freight-backend` GitHub `main`
6. Confirm no open PRs conflict

Do not rely on previous chat history.

## What is done

**Identity (phase 5):** All accounts-independent public API members are merged: `Authenticate`, `RefreshSession`, `RevokeSession`, `RevokeAllUserSessions`, `ListUserSessions`, `ResolveAuthenticatedPrincipal`. JWT access-token infrastructure (EdDSA/Ed25519, 15m TTL, server-side session validation) per ADR-0016.

**Accounts (phase 6, checkpoint 6.1):** Flyway V3 migration creates all 13 accounts-schema tables per database-erd.md. Matching JPA entities and Spring Data repositories. 10 Testcontainers integration tests. No application services or endpoints yet.

## Checkpoint 6.2: ProvisionUser

Implement `ProvisionUser` as the first accounts public application API member:

1. **Internal domain service** — create an `accounts.User` row (status ACTIVE, auto-generated UUID). Keep it minimal: no roles, no company membership, no consent — those are separate API members.
2. **Public `ProvisionUser` function** — the accounts module's public API entry point, callable cross-module (from `identity`).
3. **Wire `identity.VerifyOtpAndRegister`** — once `ProvisionUser` exists, implement the last remaining identity public API member. `VerifyOtpAndRegister` verifies the OTP, calls `accounts.ProvisionUser`, then issues a session.
4. **Update `identity` module's `allowedDependencies`** in `package-info.kt` to include `accounts` (this is the first cross-module dependency).
5. **Tests** — Testcontainers integration tests for `ProvisionUser` and `VerifyOtpAndRegister`. Update `IdentityPublicApiSurfaceTests` to include `VerifyOtpAndRegister`.

## What remains blocked

Nothing is blocked — `ProvisionUser` unblocks `VerifyOtpAndRegister`, and both can ship as checkpoint 6.2.
