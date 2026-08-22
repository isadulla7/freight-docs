# Next task

Continue with:

- **Current phase:** Backend Foundation
- **Current checkpoint:** 3.4D — Backend Foundation closure gate
- **Previous checkpoint:** 3.4C API error contract foundation — COMPLETE
- **Task specification:** [tasks/003-backend-foundation.md](tasks/003-backend-foundation.md)
- **Implementation repository:** [isadulla7/freight-backend](https://github.com/isadulla7/freight-backend)

Before implementation:

1. Read `AGENTS.md`
2. Read `PROJECT-STATUS.md`
3. Read `HANDOFF.md`
4. Read `tasks/003-backend-foundation.md`
5. Re-read [module-boundaries.md](docs/architecture/module-boundaries.md) and the security/persistence ADRs to confirm nothing drifted across 3.1A–3.4C
6. Inspect the current `freight-backend` GitHub `main`
7. Confirm open PR state

Do not rely on previous chat history.

Do not skip ahead into Identity/Auth or business modules until Backend Foundation 3.4 is explicitly completed and reviewed.

The current bounded subtask is:

- **3.4D — Backend Foundation closure gate**: a review-only checkpoint confirming checkpoints 3.1A through 3.4C are all still consistent and green together (module boundaries, deny-by-default security, Flyway-only schema authority, CI, Redis and error-contract foundations), before Backend Foundation (phase 3) is marked COMPLETE and Phase 4 (Local Infrastructure) is selected as the next phase.

No new production code is expected for 3.4D. Do not start Phase 4 or later work until 3.4D is reviewed and Backend Foundation is marked COMPLETE.
