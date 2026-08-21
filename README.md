# Freight Docs

O‘zbekiston uchun freight marketplace loyihasining asosiy texnik hujjatlari.

Bu repository quyidagilar uchun yagona ishonchli manba hisoblanadi:

- arxitektura qarorlari (ADR);
- tizim chegaralari va repository xaritasi;
- xavfsizlik va maxfiylik tamoyillari;
- umumiy terminlar.

## Boshlang‘ich texnologik baza

- backend: Kotlin, Spring Boot, Spring Modulith, JPA, jOOQ, Flyway;
- mobil ilova: Flutter;
- web: Next.js, React, TypeScript;
- ma’lumotlar: PostgreSQL, PostGIS, Redis;
- lokal infratuzilma: Docker Compose;
- API shartnomasi: OpenAPI.

Ushbu ro‘yxatning asoslari va oqibatlari [ADR-0001](docs/architecture/decisions/0001-technology-baseline.md) da qayd etilgan.

## Hujjatlar xaritasi

- [Arxitektura](docs/architecture/README.md)
- [Tizim konteksti](docs/architecture/system-context.md)
- [Tizim arxitekturasi](docs/architecture/system-overview.md)
- [Modul chegaralari](docs/architecture/module-boundaries.md)
- [Domen modeli](docs/architecture/domain-model.md)
- [API dizayni](docs/architecture/api-design.md)
- [Holat mashinalari](docs/architecture/state-machines.md)
- [Repository strategiyasi](docs/architecture/repository-map.md)
- [Arxitektura qarorlari](docs/architecture/decisions/README.md)
- [Xavfsizlik va maxfiylik](docs/principles/security-and-privacy.md)
- [Security baseline](docs/security/security-baseline.md)
- [Shaxsiy ma’lumotlar inventari](docs/privacy/data-inventory.md)
- [Privacy by design](docs/privacy/privacy-by-design.md)
- [Definition of Done](docs/operations/definition-of-done.md)
- [Glossariy](docs/glossary.md)
- [Hissa qo‘shish qoidalari](CONTRIBUTING.md)

## Holat

Loyiha boshlang‘ich bosqichda. Tasdiqlangan boshlang‘ich qarorlar ADR va tematik hujjatlarga ko‘chirildi. Hali aniqlanmagan masalalar ochiq savol sifatida saqlanadi; taxminlar arxitektura qarori sifatida qabul qilinmaydi.
