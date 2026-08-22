# Next task

Backend Foundation (phase 3) and Local Infrastructure (phase 4) are both COMPLETE. Identity/Auth (phase 5) is IN PROGRESS.

- **Current phase:** Identity/Auth
- **Last completed checkpoint:** `5.1` Identity persistence foundation ([freight-backend#14](https://github.com/isadulla7/freight-backend/pull/14), merged `aad4bd16d8a5c5a5608ceb73d8299595ccf7bd56`)
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

The next bounded Phase 5 subtask must be selected and confirmed from current repository state before coding, the same way checkpoint 5.1 was. Candidates include (not a mandatory order): OTP challenge/rate-limit state in Redis, the `accounts.ProvisionUser` integration seam, or the `VerifyOtpAndRegister`/`Authenticate` application service skeleton — whichever is smallest given what 5.1 already shipped.
