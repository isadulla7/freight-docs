# Xavfsizlik va maxfiylik tamoyillari

## Xavfsizlik — standart holat

- autentifikatsiyasiz ruxsat faqat ataylab ochilgan endpointlarda mavjud;
- avtorizatsiya deny-by-default va server tomonida bajariladi;
- foydalanuvchi kiritgan barcha ma’lumot ishonchsiz deb qaraladi;
- TLS tashqi va ichki tarmoq siyosatining majburiy qismi;
- secret, token va credential kodda, image’da yoki Git tarixida saqlanmaydi;
- dependency va container image’lar muntazam tekshiriladi;
- audit yozuvlari o‘zgarmas, maqsadga muvofiq va shaxsiy ma’lumotga boy bo‘lmagan shaklda yuritiladi.

## Privacy by design

- faqat use case uchun zarur ma’lumot yig‘iladi;
- maqsad, huquqiy asos, ko‘ruvchilar va saqlash muddati noma’lum bo‘lsa ma’lumot yig‘ilmaydi;
- aniq geolokatsiya yuqori sezgir ma’lumot sifatida ko‘riladi;
- qurilma va foydalanuvchi identifikatorlari maqsadsiz kuzatuv uchun ishlatilmaydi;
- API va loglar ma’lumotni minimallashtiradi;
- test va development muhitida real shaxsiy ma’lumot ishlatilmaydi;
- o‘chirish, eksport va tuzatish jarayonlari domen dizaynida hisobga olinadi.

## Har bir feature uchun savollar

1. Qaysi ma’lumot zarur va qaysi biri olib tashlanishi mumkin?
2. Kim ko‘rishi yoki o‘zgartirishi mumkin?
3. Ma’lumot qancha saqlanadi va qanday o‘chiriladi?
4. Log, analytics, backup va cache’da nima qoladi?
5. Abuse case va ruxsatsiz kirish qanday cheklanadi?
6. Qaror test va audit orqali qanday isbotlanadi?

## Batafsil hujjatlar

- [Security baseline](../security/security-baseline.md)
- [Shaxsiy ma’lumotlar inventari](../privacy/data-inventory.md)
- [Privacy by design](../privacy/privacy-by-design.md)
