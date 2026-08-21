# Arxitektura qarorlari

ADR arxitektura qarorining konteksti, tanlovi va oqibatlarini o‘zgarmas tarix sifatida saqlaydi.

## Holatlar

- `Taklif` — muhokama qilinmoqda;
- `Qabul qilindi` — amaldagi qaror;
- `Rad etildi` — ko‘rib chiqilgan, lekin tanlanmagan;
- `Almashtirildi` — yangi ADR bilan o‘rnini bo‘shatgan.

## Indeks

| ADR | Nomi | Holat |
| --- | --- | --- |
| [0001](0001-technology-baseline.md) | Backend texnologik bazasi | Qabul qilindi |
| [0002](0002-modular-monolith.md) | Modul monolit va hexagonal chegaralar | Qabul qilindi |
| [0003](0003-persistence.md) | PostgreSQL/PostGIS, JPA va jOOQ | Qabul qilindi |
| [0004](0004-build-and-local-runtime.md) | Gradle Kotlin DSL va Docker Compose | Qabul qilindi |
| [0005](0005-authentication-and-sessions.md) | MVP autentifikatsiya va sessiyalar | Qabul qilindi |
| [0006](0006-client-platforms.md) | Mobil va web client platformalari | Qabul qilindi |
| [0007](0007-openapi-contract.md) | OpenAPI va typed client shartnomasi | Qabul qilindi |
| [0008](0008-internal-events.md) | Ichki eventlar va background ish | Qabul qilindi |
| [0009](0009-repository-strategy.md) | Multi-repository chegaralari | Qabul qilindi |
| [0010](0010-privacy-and-security-baseline.md) | Privacy va security baseline | Qabul qilindi |
| [0011](0011-domain-data-conventions.md) | Money, vaqt va identifier konvensiyalari | Qabul qilindi |
| [0012](0012-domain-aggregate-boundaries.md) | Domen aggregate va koordinatsiya chegaralari | Qabul qilindi |
| [0013](0013-shared-database-ownership.md) | Shared database schema ownership va cross-module reference | Qabul qilindi |
| [0014](0014-geospatial-representation.md) | Geospatial point representation va SRID | Qabul qilindi |
| [0015](0015-authorization-and-audit.md) | Capability-based scoped authorization va audit | Qabul qilindi |

Yangi qaror uchun [ADR shabloni](0000-template.md) dan foydalaning.
