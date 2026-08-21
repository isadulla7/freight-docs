# ADR-0004: Gradle Kotlin DSL va Docker Compose

- Holat: Qabul qilindi
- Sana: 2026-08-21
- Qaror egalari: Loyiha egasi
- Manba: taqdim etilgan boshlang‘ich arxitektura hujjatlari
- Tegishli ADR: [ADR-0001](0001-technology-baseline.md)

## Qaror

- backend build Gradle Kotlin DSL bilan boshqariladi;
- lokal bog‘liqliklar Docker Compose orqali ishga tushiriladi;
- local baseline: PostgreSQL/PostGIS, Redis va S3-compatible object storage;
- pullik tashqi providerlar uchun imkon qadar mock yoki local adapter ishlatiladi;
- application container image productiongacha o‘sha artefakt sifatida ko‘tariladi;
- muhit farqlari kod orqali emas, environment va secret konfiguratsiyasi orqali beriladi.

## Oqibatlar

- lokal muhit reproduktiv bo‘ladi;
- productionga yaqin container oqimi saqlanadi;
- Compose production orchestrator sifatida majburan qabul qilinmaydi;
- real credential `.env.example` yoki Git’ga kiritilmaydi.

## Tekshirish

- toza muhitda bitta hujjatlashtirilgan buyruq bilan dependency’larni ishga tushirish;
- container healthchecklar;
- `.env.example` faqat nom va xavfsiz defaultlarni saqlashini secret scan bilan tekshirish.
