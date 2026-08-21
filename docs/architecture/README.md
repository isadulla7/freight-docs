# Arxitektura

## Yo‘nalish

Boshlang‘ich yechim — aniq modul chegaralariga ega modul monolit. Spring Modulith bu chegaralarni ifodalash va avtomatik tekshirish uchun ishlatiladi. Alohida servis faqat mustaqil masshtablash, izolyatsiya yoki jamoaviy egalik ehtiyoji isbotlanganda ajratiladi.

## Majburiy xususiyatlar

- modulning ichki modeli boshqa modulga ochilmaydi;
- modullar faqat e’lon qilingan API va hodisalar orqali hamkorlik qiladi;
- API shartnomalari OpenAPI bilan versiyalanadi;
- sxema o‘zgarishlari faqat Flyway migratsiyasi orqali bajariladi;
- JPA oddiy transactional CRUD uchun, jOOQ murakkab va o‘qishga yo‘naltirilgan so‘rovlar uchun ishlatiladi;
- xavfsizlik va maxfiylik har bir qarorning bir qismi hisoblanadi;
- yangi infratuzilma komponenti aniq ehtiyojsiz qo‘shilmaydi.

## Hujjatlar

- [Tizim konteksti](system-context.md)
- [Tizim arxitekturasi](system-overview.md)
- [Modul chegaralari](module-boundaries.md)
- [Domen modeli](domain-model.md)
- [API dizayni](api-design.md)
- [Holat mashinalari](state-machines.md)
- [Repository strategiyasi](repository-map.md)
- [ADR indeksi](decisions/README.md)
