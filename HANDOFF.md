# Current handoff

| Field | Value |
| --- | --- |
| Repository | `isadulla7/freight-docs` |
| Current branch | `docs/task-003-backend-foundation` |
| Base branch | `main` |
| Current phase | `[3] Backend Foundation - IN PROGRESS` |
| Current checkpoint | `[3.4A] CI foundation - CURRENT` |
| Last completed checkpoint | `[3.3B] PostgreSQL/PostGIS integration validation` |

## Work state

- API Contract v1 is COMPLETE.
- Backend Foundation 3.1A is COMPLETE.
- Backend Foundation 3.1B is COMPLETE.
- Backend Foundation 3.2 is COMPLETE.
- Backend Foundation 3.3A is COMPLETE.
- Backend Foundation 3.3B is COMPLETE.
- Backend integration validation runs successfully with Docker/Testcontainers/PostGIS after the second-startup test fix.
- Backend Foundation 3.4A is the current bounded task.
- No Identity/Auth or business-domain implementation has started.

## Exact next action

Complete Backend Foundation 3.4A CI foundation.

Before coding:

- inspect current `freight-backend` main
- confirm open PR state
- keep the task small
- preserve Spring Modulith boundaries
- preserve deny-by-default security
- preserve Flyway as schema authority
- never commit secrets

Update this file before the next handoff.
