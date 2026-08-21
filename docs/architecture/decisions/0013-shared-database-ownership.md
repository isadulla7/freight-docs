# ADR-0013: Shared database schema ownership va cross-module reference

- Holat: Qabul qilindi
- Sana: 2026-08-21
- Qaror egalari: Loyiha egasi
- Tegishli ADR: [ADR-0002](0002-modular-monolith.md), [ADR-0003](0003-persistence.md)
- Tafsilotlar: [Database ownership](../database-ownership.md)

## Kontekst

Modular monolith bitta PostgreSQL database ishlatadi. Referential integrityni saqlash bilan modulning boshqa modul persistence qatlamiga bog‘lanishini aralashtirmaslik kerak.

## Qaror

- har business modul o‘z PostgreSQL schema va jadvallariga egalik qiladi;
- module repository/JPA/jOOQ mutationi faqat o‘z schema’sida;
- cross-module command/read public application API yoki event-fed owned projection orqali;
- stable current reference uchun cross-module UUID foreign keyga ruxsat;
- cross-module FK faqat `ON DELETE NO ACTION`/`RESTRICT`, hech qachon cascade mutation emas;
- foreign key authorization yoki dependency permission bermaydi;
- database trigger/stored procedure boshqa modul jadvalini mutate qilmaydi;
- historical deletion/anonymization semantikasi retention qarori bilan, FK cascade orqali emas;
- module extraction bo‘lsa FK explicit migration ADR bilan yechiladi.

## Variantlar

- Barcha cross-module FKlarni taqiqlash: extraction osonroq, lekin MVP shared database’da orphan/reference xatosi xavfi ortadi.
- Shared public schema va unrestricted join: qisqa yo‘l, lekin ownershipni yo‘qotadi.
- Tanlangan schema ownership + restrictive FK: integrity va modularityning pragmatic muvozanati.

## Oqibatlar

- migration va table owner aniq;
- physical FK service extractionda migration cost keltiradi;
- cross-module read uchun ad hoc join o‘rniga public contract/projection kerak;
- hard delete kam va explicit bo‘ladi.

## Xavfsizlik va maxfiylik

Schema separation tenant isolation emas. Har request server-side scoped authorization oladi. Database runtime grantlari keyingi defense-in-depth bo‘lishi mumkin, ammo architecture test va application authorizationni almashtirmaydi.

## Tekshirish

- architecture/import testlari;
- Flyway lint/review: owner schema va FK delete action;
- cross-module cascade va orphan integration testlari.
