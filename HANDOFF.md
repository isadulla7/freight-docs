# Current handoff

| Field | Value |
| --- | --- |
| Repositories | `isadulla7/freight-docs`, `isadulla7/freight-backend` |
| freight-docs branch | `main` |
| freight-backend branch | `main` (synchronized with `origin/main`) |
| Base branch | `main` (both repos) |
| Current phase | Backend phases 1–12 COMPLETE |
| Last completed phase | `[12] API delivery layer` (checkpoints 12.1–12.10) |
| Next phase | Not yet defined |

## Work state

- Phases 1–12 are COMPLETE and merged to `main`.
- Phase 11 (Communication): V8 migration, 7 tables, 7 entities, 8 services, 17 integration tests + public API surface test.
- Phase 12: JWT security, REST controllers for all business modules, STOMP WebSocket, and SpringDoc OpenAPI contract tests.
- Post-Phase 12: international freight enhancements and a layered package architecture refactor were merged.

## GitHub state

| Field | Value |
| --- | --- |
| `freight-backend` main SHA | `db77721` |
| `freight-docs` main SHA | Update after pushing docs |
| Open PRs | None |

## Flyway state

| Version | Module | Tables |
| --- | --- | --- |
| V1 | schemas + PostGIS | 7 schemas created |
| V2 | identity | 3 tables |
| V3 | accounts | 13 tables |
| V4 | fleet | 8 tables |
| V5 | freight | 5 tables |
| V6 | marketplace | 1 table |
| V7 | shipment | 2 tables |
| V8 | communication | 7 tables |
| V9 | freight | structured load-stop location fields |
| V11 | freight | load documents table |
| V12 | accounts | preferred locale field |
| V13 | marketplace | nullable currency compatibility |

V10 is intentionally unused; existing migrations were not renumbered or modified.

Total JPA entities: 40

## Package architecture (implemented)

```
uz.freight.api.<module>                  REST/WebSocket delivery and DTOs
uz.freight.<module>.application         commands, queries, and public use cases
uz.freight.<module>.domain.model        domain entities, enums, and value types
uz.freight.<module>.infrastructure      persistence adapters and repositories
uz.freight.bootstrap                    security and application configuration
uz.freight.shared                       shared error, validation, and web utilities
```

## Tooling notes

`gh` CLI is not authenticated. Use `git push` directly (credentials cached). PRs merged via local `git merge --squash` + push to main.

## Blockers

None. Backend phases are complete through Phase 12. The next main roadmap phase has not yet been defined.
