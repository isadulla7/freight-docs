# ADR-0001: Backend texnologik bazasi

- Holat: Qabul qilindi
- Sana: 2026-08-21
- Qaror egalari: Loyiha egasi
- Manba: loyiha boshlanish talablari
- Canonical kontekst: [Architecture Context v1.0](../../../architecture/architecture-context-v1.md)

## Kontekst

O‘zbekiston freight marketplace mahsulotini tez va boshqariladigan tarzda boshlash, domen chegaralarini saqlash hamda erta mikroservis murakkabligidan qochish kerak. Mobil va web mijozlar, geografik so‘rovlar, transactional ma’lumotlar va qisqa muddatli tezkor holat qo‘llab-quvvatlanishi kerak.

## Qaror

- Backend Kotlin, Java 25 LTS va Spring Boot asosida quriladi.
- Spring Security autentifikatsiya va avtorizatsiya uchun ishlatiladi.
- Spring Modulith modul chegaralarini ifodalash va tekshirish uchun ishlatiladi; batafsil arxitektura [ADR-0002](0002-modular-monolith.md) da.
- Oddiy aggregate va transactional CRUD uchun JPA, murakkab yoki o‘qishga yo‘naltirilgan SQL uchun jOOQ ishlatiladi.
- Ma’lumotlar bazasi o‘zgarishlari Flyway orqali versiyalanadi.
- Doimiy ma’lumotlar PostgreSQL’da, geografik ma’lumotlar PostGIS orqali saqlanadi va so‘raladi.
- Redis faqat aniq, vaqtinchalik va qayta tiklanadigan use case uchun ishlatiladi; u asosiy ma’lumot manbai emas.
- API shartnomalari OpenAPI orqali hujjatlashtiriladi va tekshiriladi; ownership [ADR-0007](0007-openapi-contract.md) da.
- Testlar JUnit va real dependency containerlari uchun Testcontainers bilan yoziladi.
- Lokal ishlab chiqish uchun zarur bog‘liqliklar Docker Compose orqali ishga tushiriladi.

## Oqibatlar

### Ijobiy

- bitta deployment birligi bilan operatsion murakkablik past bo‘ladi;
- transactionlar va lokal ishlab chiqish sodda qoladi;
- qat’iy modul chegaralari keyinchalik kerakli qismlarni ajratish imkonini saqlaydi;
- Kotlin/Spring ekotizimi security, persistence va observability uchun yetuk vositalar beradi.

### Salbiy

- modul chegaralarini intizom va avtomatik testlar bilan faol himoya qilish kerak;
- JPA va jOOQ birgalikda ishlatilganda transaction va mapping qoidalari aniq bo‘lishi kerak;
- bitta deploy ayrim modullarni mustaqil masshtablash imkonini bermaydi.

## Xavfsizlik va maxfiylik

Umumiy privacy/security baseline [ADR-0010](0010-privacy-and-security-baseline.md) da boshqariladi. Ushbu texnologik stack deny-by-default avtorizatsiya, least privilege, data minimization va tashqi secret konfiguratsiyasini qo‘llab-quvvatlashi shart.

## Tekshirish

- Spring Modulith modul tekshiruv testlari;
- OpenAPI contract validation;
- Flyway migratsiyalarini bo‘sh va oldingi sxemadan sinash;
- dependency va secret scanning;
- security va privacy talablarini pull request tekshiruv ro‘yxatiga kiritish.
