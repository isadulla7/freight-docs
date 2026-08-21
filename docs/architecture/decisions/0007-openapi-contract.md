# ADR-0007: OpenAPI va typed client shartnomasi

- Holat: Qabul qilindi
- Sana: 2026-08-21
- Qaror egalari: Loyiha egasi
- Manba: [Architecture Context v1.0](../../../architecture/architecture-context-v1.md)

## Qaror

- REST API bazasi `/api/v1`;
- OpenAPI clientlararo API contract source of truth;
- Flutter/Dart va Next.js/TypeScript typed clientlari contractdan generatsiya qilinadi;
- OpenAPI artefaktlari va client-generation konfiguratsiyasiga `freight-contracts` egalik qiladi;
- xatolar barqaror machine-readable formatda;
- katta feedlarda cursor pagination va server-side filtering;
- financial yoki high-risk mutationlarda idempotency talab qilinadi.

## Oqibatlar

- backend contract o‘zgarishi runtime’dan oldin build/testda ko‘rinadi;
- `freight-contracts` biznes modeli yoki umumiy utilitalar repositorysi emas;
- API response faqat use case uchun zarur maydonlarni ochadi.

## Tekshirish

- OpenAPI lint va breaking-change check;
- generated client buildlari;
- implementation-contract integration testlari.
