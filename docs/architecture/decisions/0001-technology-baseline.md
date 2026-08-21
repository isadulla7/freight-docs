# ADR-0001: Texnologik baza va modul monolit

- Holat: Qabul qilindi
- Sana: 2026-08-21
- Qaror egalari: Loyiha egasi
- Manba: loyiha boshlanish talablari

## Kontekst

O‘zbekiston freight marketplace mahsulotini tez va boshqariladigan tarzda boshlash, domen chegaralarini saqlash hamda erta mikroservis murakkabligidan qochish kerak. Mobil va web mijozlar, geografik so‘rovlar, transactional ma’lumotlar va qisqa muddatli tezkor holat qo‘llab-quvvatlanishi kerak.

## Qaror

- Backend Kotlin va Spring Boot asosidagi modul monolit bo‘ladi.
- Spring Modulith modul chegaralarini ifodalash, tekshirish va modul ichidagi hodisalarni boshqarish uchun ishlatiladi.
- Oddiy aggregate va transactional CRUD uchun JPA, murakkab yoki o‘qishga yo‘naltirilgan SQL uchun jOOQ ishlatiladi.
- Ma’lumotlar bazasi o‘zgarishlari Flyway orqali versiyalanadi.
- Doimiy ma’lumotlar PostgreSQL’da, geografik ma’lumotlar PostGIS orqali saqlanadi va so‘raladi.
- Redis faqat aniq, vaqtinchalik va qayta tiklanadigan use case uchun ishlatiladi; u asosiy ma’lumot manbai emas.
- API shartnomalari OpenAPI orqali hujjatlashtiriladi va tekshiriladi.
- Mobil mijoz Flutter, web mijoz Next.js, React va TypeScript bilan quriladi.
- Lokal ishlab chiqish uchun zarur bog‘liqliklar Docker Compose orqali ishga tushiriladi.

## Modul qoidalari

- har bir modul o‘z domen modeli va persistence tafsilotlariga egalik qiladi;
- boshqa modul faqat e’lon qilingan API yoki hodisadan foydalanadi;
- modulning ichki package’iga to‘g‘ridan-to‘g‘ri bog‘lanish taqiqlanadi;
- cross-module database join odatiy integratsiya mexanizmi emas;
- Modulith va arxitektura testlari build jarayonida chegaralarni tekshiradi.

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

- deny-by-default avtorizatsiya;
- eng kam imtiyoz va ma’lumotlarni minimallashtirish;
- secretlar faqat tashqi secret/config mexanizmi orqali beriladi;
- loglarda token, credential, aniq lokatsiya va keraksiz shaxsiy ma’lumot bo‘lmaydi;
- shaxsiy va geolokatsion ma’lumotlar uchun saqlash muddati va kirish auditi alohida qarorda belgilanadi;
- API kirishlari validatsiya qilinadi va chiqishlar ortiqcha maydonlarni oshkor qilmaydi.

## Tekshirish

- Spring Modulith modul tekshiruv testlari;
- OpenAPI contract validation;
- Flyway migratsiyalarini bo‘sh va oldingi sxemadan sinash;
- dependency va secret scanning;
- security va privacy talablarini pull request tekshiruv ro‘yxatiga kiritish.
