# Next task

Backend Foundation (phase 3), Local Infrastructure (phase 4), and Identity/Auth (phase 5, accounts-independent slice) are all COMPLETE. The access-token design was decided and recorded as [ADR-0016](docs/architecture/decisions/0016-access-token-design.md). Checkpoints 5.7 and 5.8 are pushed but the PR has not yet been created (requires `gh auth login`).

- **Current phase:** Identity/Auth
- **Last completed checkpoint:** `5.8` ResolveAuthenticatedPrincipal + JWT access-token infrastructure (branch `feat/identity-access-token-infrastructure` pushed to origin)
- **Task specification:** [tasks/005-identity-auth.md](tasks/005-identity-auth.md) — see "Phase 5 status"
- **Implementation repository:** [isadulla7/freight-backend](https://github.com/isadulla7/freight-backend)

Before any further implementation:

1. Read `AGENTS.md`
2. Read `PROJECT-STATUS.md`
3. Read `HANDOFF.md`
4. Read `tasks/005-identity-auth.md` in full
5. Inspect the current `freight-backend` GitHub `main`
6. Confirm open PR state

Do not rely on previous chat history.

## What is done

All `identity` public application API members that do not depend on `accounts` are implemented and tested:
`Authenticate`, `RefreshSession`, `RevokeSession`, `RevokeAllUserSessions`, `ListUserSessions`, `ResolveAuthenticatedPrincipal`.

Supporting infrastructure: Flyway schema, JPA entities/repositories, OTP challenge/rate-limit Redis state, session lifecycle with rotation and reuse detection, JWT access-token issuance and verification (EdDSA/Ed25519, 15-minute TTL, server-side session validation). All covered by Testcontainers integration tests plus a source-level public API surface lock-down test.

## Immediate next action

1. **Create the PR** for branch `feat/identity-access-token-infrastructure` — requires `gh auth login` (the CLI is installed but not authenticated).
2. **Merge** the PR once CI is green.
3. **Choose the next work:** either start Phase 6 (`accounts`), or implement `identity` HTTP endpoints for the already-complete public API.

## What remains blocked

1. **`VerifyOtpAndRegister`** needs `accounts.ProvisionUser`. `accounts` is still just `package-info.kt` — this is Phase 6 work, explicitly out of Phase 5 scope. Not ambiguous, just sequenced after Phase 6 starts.

No HTTP endpoints exist yet anywhere in `identity` — a separate, later subtask category.
