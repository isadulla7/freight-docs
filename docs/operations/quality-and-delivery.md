# Quality va delivery baseline

## Testlar

- backend: JUnit;
- integration: Testcontainers bilan real PostgreSQL/PostGIS va zarur dependencylar;
- H2 PostgreSQL/PostGIS o‘rnini bosuvchi integration database sifatida ishlatilmaydi;
- module boundary: Spring Modulith verification va cyclic/internal import testlari;
- migration: bo‘sh va oldingi sxemadan Flyway testlari;
- API: OpenAPI contract va generated client buildlari.

## CI/CD

GitHub Actions quyidagilarni bosqichma-bosqich majburiy qiladi:

- build;
- qiymat beradigan lint/static analysis;
- unit va integration testlar;
- architecture testlar;
- migration check;
- contract check;
- secret scanning.

Normal workflow’da critical validationni jim chetlab o‘tish mumkin emas. Linter konfiguratsiyasi developmentni qiymatsiz sekinlashtiradigan darajada murakkablashtirilmaydi.

## Performance baseline

- to‘g‘ri relational va GIST indekslar;
- spatial/filter query database’da;
- N+1 nazorati;
- cursor pagination va kichik payload;
- read-heavy yo‘llarda jOOQ;
- Redis faqat hot/transient data;
- sekin non-critical ishlar background bajarilishi mumkin.
