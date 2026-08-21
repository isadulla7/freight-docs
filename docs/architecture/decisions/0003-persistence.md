# ADR-0003: PostgreSQL/PostGIS, JPA va jOOQ

- Holat: Qabul qilindi
- Sana: 2026-08-21
- Qaror egalari: Loyiha egasi
- Manba: taqdim etilgan boshlang‘ich arxitektura hujjatlari
- Tegishli ADR: [ADR-0001](0001-technology-baseline.md)
- Canonical kontekst: [Architecture Context v1.0](../../../architecture/architecture-context-v1.md)

## Kontekst

Freight discovery dinamik filtering, geografik qidiruv va transactional write flow’larni talab qiladi. Bitta persistence abstraksiyasi barcha workload uchun birdek qulay emas.

## Qaror

- PostgreSQL/PostGIS source of truth;
- JPA/Hibernate transactional va write-oriented flow uchun;
- jOOQ murakkab, performance-sensitive feed, search va reporting query uchun;
- ikkala yondashuv MVP’da bitta database va transaction boshqaruvini ulashadi;
- Flyway database sxemasining yagona versiyalash mexanizmi;
- pickup, delivery va availability lokatsiyalari PostGIS turi hamda GIST indekslari bilan saqlanadi;
- masofa application/client xotirasida emas, spatial database query orqali hisoblanadi;
- Redis faqat OTP, rate limit, hot cache, session metadata va transient holat uchun;
- MVP’da Elasticsearch/OpenSearch qo‘shilmaydi.

Bu CQRS-lite; alohida read database, event sourcing yoki distributed CQRS emas.

## Oqibatlar

- domain write modeli va optimallashtirilgan read query bir-biriga majburan moslashtirilmaydi;
- mapping va transaction ownership qoidalari aniq bo‘lishi kerak;
- Redis source of truth sifatida ishlatilmaydi.

## Tekshirish

- migratsiyalarni bo‘sh va oldingi sxemadan sinash;
- integration testlar real PostgreSQL/PostGIS bilan;
- performance-sensitive query uchun execution plan va indeks tekshiruvi.
