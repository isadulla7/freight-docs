# Next task

Backend Foundation (phase 3) and Local Infrastructure (phase 4) are both COMPLETE. Identity/Auth (phase 5)'s accounts-independent slice is COMPLETE; the two remaining pieces are BLOCKED pending a decision this session was not authorized to make unilaterally.

- **Current phase:** Identity/Auth
- **Last completed checkpoint:** `5.6` Public API surface lock-down / closure gate ([freight-backend#19](https://github.com/isadulla7/freight-backend/pull/19), merged `865867c78470168acb6179fc378428dcbc89bbd0`)
- **Task specification:** [tasks/005-identity-auth.md](tasks/005-identity-auth.md) — see "Accounts-independent slice: closure assessment"
- **Implementation repository:** [isadulla7/freight-backend](https://github.com/isadulla7/freight-backend)

Before any further implementation:

1. Read `AGENTS.md`
2. Read `PROJECT-STATUS.md`
3. Read `HANDOFF.md`
4. Read `tasks/005-identity-auth.md` in full, especially the closure assessment section
5. Re-read the `identity` section of [module-boundaries.md](docs/architecture/module-boundaries.md), [ADR-0005](docs/architecture/decisions/0005-authentication-and-sessions.md), and [ADR-0015](docs/architecture/decisions/0015-authorization-and-audit.md)
6. Inspect the current `freight-backend` GitHub `main`
7. Confirm open PR state

Do not rely on previous chat history.

## What is done

`Authenticate`, `RefreshSession`, `RevokeSession`, `RevokeAllUserSessions`, `ListUserSessions` are implemented, tested (Testcontainers PostgreSQL/Redis), and the module's public surface is locked to exactly these types plus their result/DTO types. This is everything in `identity`'s accepted public API that does not depend on another module or an undecided security mechanism.

## What is blocked, and why

1. **`VerifyOtpAndRegister`** needs `accounts.ProvisionUser`. `accounts` is still just `package-info.kt` — this is Phase 6 work, explicitly out of Phase 5 scope. Not ambiguous, just sequenced after Phase 6 starts.
2. **`ResolveAuthenticatedPrincipal`** needs an access-token design (signing algorithm, claims, expiry, validation flow — e.g. stateless JWT vs. a server-side lookup). The accepted architecture only names `JWT_PRIVATE_KEY` in an illustrative env-var list, not a real specification. This is a "security model requires a new architecture decision" situation per the project's blocker policy — it needs an explicit decision (ideally a short ADR) before implementation, not a guess.

**Do not implement `accounts` persistence to unblock (1), and do not invent a JWT/token scheme to unblock (2), without the product owner's explicit direction.** Both are recorded here so the decision is visible without needing prior chat context.

## Once unblocked

- If Phase 6 (`accounts`) starts first: implement `VerifyOtpAndRegister` once `accounts.ProvisionUser` exists.
- If an access-token design is settled: implement `ResolveAuthenticatedPrincipal`, then wire real HTTP endpoints for the already-complete `Authenticate`/session-management API (endpoints are a separate, not-yet-started subtask category regardless of the above).
