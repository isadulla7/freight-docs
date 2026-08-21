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
- [Repository strategiyasi](docs/architecture/repository-map.md)
- [Arxitektura qarorlari](docs/architecture/decisions/README.md)
- [Xavfsizlik va maxfiylik](docs/principles/security-and-privacy.md)
- [Glossariy](docs/glossary.md)
- [Hissa qo‘shish qoidalari](CONTRIBUTING.md)

## Holat

Loyiha boshlang‘ich bosqichda. Mavjud ChatGPT suhbatidan olinadigan qarorlar alohida ADR sifatida, manbasi va sanasi ko‘rsatilgan holda kiritiladi. Tasdiqlanmagan taxminlar arxitektura qarori sifatida qabul qilinmaydi.
