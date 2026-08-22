# Current handoff

| Field | Value |
| --- | --- |
| Repository | `isadulla7/freight-docs` |
| Current branch | `docs/task-003-3.4c-api-error-contract` |
| Base branch | `main` |
| Current phase | `[3] Backend Foundation - IN PROGRESS` |
| Current checkpoint | `[3.4C] API error contract foundation - CURRENT` |
| Last completed checkpoint | `[3.4B] Redis foundation` |

## Work state

- API Contract v1 is COMPLETE.
- Backend Foundation 3.1A is COMPLETE.
- Backend Foundation 3.1B is COMPLETE.
- Backend Foundation 3.2 is COMPLETE.
- Backend Foundation 3.3A is COMPLETE.
- Backend Foundation 3.3B is COMPLETE.
- Backend Foundation 3.4A is COMPLETE (GitHub Actions build/test pipeline, `freight-backend` PR #7, merged).
- Backend Foundation 3.4B is COMPLETE (Redis client foundation, `freight-backend` PR #8, merged).
- Backend Foundation 3.4C (API error contract foundation) is the current bounded task. Implementation has not started.
- No Identity/Auth or business-domain implementation has started.

## Exact next action

Select and confirm the exact bounded scope of Backend Foundation 3.4C (API error contract foundation), then execute it in `freight-backend`.

Before coding:

- inspect current `freight-backend` main
- confirm open PR state
- keep the task small
- preserve Spring Modulith boundaries
- preserve deny-by-default security
- preserve Flyway as schema authority
- do not change accepted architecture decisions
- never commit secrets

Update this file before the next handoff.
