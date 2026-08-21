# API Contract v1

Ushbu papka `/api/v1` HTTP contract hujjatlarini saqlaydi. OpenAPI spec authoritative schema source of truth; qo'shimcha hujjatlar contract qoidalarini izohlaydi.

## Hujjatlar

| Fayl | Mazmun |
|---|---|
| [conventions.md](conventions.md) | Umumiy API konvensiyalari: UUID, UTC, Money, GeoPoint, pagination, error model |
| [api-design-standard.md](api-design-standard.md) | Resource naming, HTTP method, versioning, idempotency, backward compatibility |
| [authentication.md](authentication.md) | OTP authentication, session lifecycle, token security |
| [authorization-matrix.md](authorization-matrix.md) | Endpoint-permission-resource matrix |
| [errors.md](errors.md) | Error model, code katalogi, validation format |
| [pagination-filtering.md](pagination-filtering.md) | Cursor pagination, filtering, sorting konvensiyalari |
| [compatibility.md](compatibility.md) | Backward compatibility, versioning, enum evolution |
| [security-abuse.md](security-abuse.md) | Abuse vektorlari, rate-limit, security controls |
| [privacy-boundary.md](privacy-boundary.md) | API field classification, minimization, non-disclosure |

## OpenAPI spec

Canonical contract: [`contracts/openapi/freight-api-v1.yaml`](../../contracts/openapi/freight-api-v1.yaml)

## Architecture references

Contractning architecture bazasi:

- [Architecture Context v1.0](../../architecture/architecture-context-v1.md)
- [Domain model](../architecture/domain-model.md)
- [State machines](../architecture/state-machines.md)
- [Authorization model](../architecture/authorization-model.md)
- [Event catalog](../architecture/event-catalog.md)
- [Database ERD](../architecture/database-erd.md)
- [ADR indeks](../architecture/decisions/README.md)
