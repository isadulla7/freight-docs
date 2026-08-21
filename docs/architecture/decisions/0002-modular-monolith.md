# ADR-0002: Modul monolit va hexagonal chegaralar

- Holat: Qabul qilindi
- Sana: 2026-08-21
- Qaror egalari: Loyiha egasi
- Manba: taqdim etilgan boshlang‘ich arxitektura hujjatlari
- Tegishli ADR: [ADR-0001](0001-technology-baseline.md)
- Canonical kontekst: [Architecture Context v1.0](../../../architecture/architecture-context-v1.md)

## Kontekst

MVP bosqichida mikroservislar deployment, networking, observability va consistency murakkabligini mahsulot ehtiyoji isbotlanmasidan oldin oshiradi. Shu bilan birga, keyinchalik ajratish mumkin bo‘lgan qat’iy domen chegaralari kerak.

## Qaror

- backend Spring Modulith bilan modul monolit bo‘ladi;
- DDD-lite domen tilini va aggregate chegaralarini saqlash uchun ishlatiladi;
- hexagonal arxitektura domainni transport, persistence va tashqi providerlardan ajratadi;
- CQRS-lite faqat murakkab read workload uchun ishlatiladi, distributed CQRS emas;
- modul boshqa modulning internal persistence qatlamiga kira olmaydi;
- muhim aggregate domain modeli JPA entity’dan ajratiladi va mapper orqali bog‘lanadi;
- domain Spring MVC, JPA annotation, Redis, provider SDK yoki transport modeliga bog‘lanmaydi;
- trivial reference/config ma’lumotida qiymat bermaydigan boilerplate majburan qo‘shilmaydi.

## Ajratish sharti

Modul faqat trafik, jamoaviy egalik, mustaqil masshtablash yoki operatsion izolyatsiya talabi isbotlanganda alohida servisga chiqariladi.

## Oqibatlar

- bitta deploy va oddiy local runtime saqlanadi;
- transaction chegaralari boshqarilishi oson;
- public API/event intizomi va avtomatik boundary testlar majburiy;
- service extraction yo‘li ochiq, ammo oldindan amalga oshirilmaydi.

## Tekshirish

- Spring Modulith verification testlari;
- cyclic dependency tekshiruvi;
- internal package’larga cross-module importni taqiqlovchi arxitektura testlari.
