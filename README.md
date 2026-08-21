# Freight Docs

O‘zbekiston uchun freight marketplace loyihasining asosiy texnik hujjatlari.

Bu repository quyidagilar uchun yagona ishonchli manba hisoblanadi:

- arxitektura qarorlari (ADR);
- tizim chegaralari va repository xaritasi;
- xavfsizlik va maxfiylik tamoyillari;
- umumiy terminlar.

## Canonical kontekst

[Architecture Context v1.0](architecture/architecture-context-v1.md) loyiha visioni, scope’i va umumiy arxitektura handoff’i uchun canonical manbadir. Arxitektura qarorlarining ustuvorlik tartibi:

1. eng yangi qabul qilingan ADR;
2. canonical architecture context yoki uning vorisi;
3. tematik/modul hujjatlari;
4. OpenAPI shartnomasi;
5. kod va testlar.

Nomuvofiqlik defect hisoblanadi va yashirin talqin bilan emas, ADR hamda hujjat yangilanishi orqali bartaraf etiladi.

## Boshlang‘ich texnologik baza

- backend: Kotlin, Spring Boot, Spring Modulith, JPA, jOOQ, Flyway;
- mobil ilova: Flutter;
- web: Next.js, React, TypeScript;
- ma’lumotlar: PostgreSQL, PostGIS, Redis;
- lokal infratuzilma: Docker Compose;
- API shartnomasi: OpenAPI.

Ushbu ro‘yxatning asoslari va oqibatlari [ADR indeksida](docs/architecture/decisions/README.md) qayd etilgan.

## Hujjatlar xaritasi

- [Arxitektura](docs/architecture/README.md)
- [Tizim konteksti](docs/architecture/system-context.md)
- [Tizim arxitekturasi](docs/architecture/system-overview.md)
- [Modul chegaralari](docs/architecture/module-boundaries.md)
- [Domen modeli](docs/architecture/domain-model.md)
- [Domen invariantlari](docs/architecture/domain-invariants.md)
- [API dizayni](docs/architecture/api-design.md)
- [Holat mashinalari](docs/architecture/state-machines.md)
- [Event katalogi](docs/architecture/event-catalog.md)
- [Database ERD v1](docs/architecture/database-erd.md)
- [Database ownership](docs/architecture/database-ownership.md)
- [Authorization modeli](docs/architecture/authorization-model.md)
- [Architecture v1.0 open questions](docs/architecture/open-questions.md)
- [Repository strategiyasi](docs/architecture/repository-map.md)
- [Arxitektura qarorlari](docs/architecture/decisions/README.md)
- [Xavfsizlik va maxfiylik](docs/principles/security-and-privacy.md)
- [Security baseline](docs/security/security-baseline.md)
- [MVP threat review](docs/security/threat-review-v1.md)
- [Shaxsiy ma’lumotlar inventari](docs/privacy/data-inventory.md)
- [Privacy by design](docs/privacy/privacy-by-design.md)
- [Definition of Done](docs/operations/definition-of-done.md)
- [Quality va delivery](docs/operations/quality-and-delivery.md)
- [Glossariy](docs/glossary.md)
- [Hissa qo‘shish qoidalari](CONTRIBUTING.md)

## Holat

Architecture v1.0 Lock review gate hujjatlari tayyorlangan. [Ochiq savollar](docs/architecture/open-questions.md)da backend foundationni bloklaydigan qaror yo‘q, ammo ushbu lock PR review va merge qilinmaguncha application code yoki yangi repository boshlanmaydi. Hali aniqlanmagan product/legal masala taxmin qilinmaydi.
