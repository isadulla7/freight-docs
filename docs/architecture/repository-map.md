# Repository strategiyasi

## Qabul qilingan boshlanish

`freight-docs` birinchi repository bo‘lib, barcha repositorylarga ta’sir qiladigan qarorlarni saqlaydi.

## Rejalashtirilgan repositorylar

Quyidagi nomlar ishchi taklifdir; ular tegishli arxitektura qarorlari tasdiqlangach yaratiladi.

| Repository | Mas’uliyat | Holat |
| --- | --- | --- |
| `freight-docs` | Arxitektura, ADR, xavfsizlik va umumiy terminlar | Yaratildi |
| `freight-backend` | Kotlin/Spring Boot modul monolit va OpenAPI | Rejalashtirilgan |
| `freight-mobile` | Flutter mobil ilova | Rejalashtirilgan |
| `freight-web` | Next.js/React/TypeScript web ilova | Rejalashtirilgan |

Docker Compose avval uni ishlatadigan asosiy repositoryda saqlanadi. Alohida `freight-infra` repository faqat mustaqil deployment/infratuzilma hayot sikli zarur bo‘lsa yaratiladi. Bu ortiqcha repository va sinxronlash murakkabligini oldini oladi.

## Chegara qoidalari

- repositorylar bir-birining ichki kodini nusxalamaydi;
- integratsiya versiyalangan shartnomalar orqali amalga oshiriladi;
- umumiy kutubxona faqat kamida ikki real iste’molchi va barqaror semantika bo‘lganda ajratiladi;
- secret va muhitga xos konfiguratsiya Git’ga kiritilmaydi.
