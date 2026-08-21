# Repository strategiyasi

## Qabul qilingan boshlanish

`freight-docs` birinchi repository bo‘lib, barcha repositorylarga ta’sir qiladigan qarorlarni saqlaydi.

## Rejalashtirilgan repositorylar

Quyidagi repository chegaralari boshlang‘ich reja sifatida qabul qilingan. `freight-docs` hozir yaratildi; qolganlari tegishli ish boshlanganda yaratiladi.

| Repository | Mas’uliyat | Holat |
| --- | --- | --- |
| `freight-docs` | Arxitektura, ADR, xavfsizlik va umumiy terminlar | Yaratildi |
| `freight-backend` | Kotlin/Spring Boot modul monolit va OpenAPI | Rejalashtirilgan |
| `freight-mobile` | Flutter mobil ilova | Rejalashtirilgan |
| `freight-web` | Next.js/React/TypeScript web ilova | Rejalashtirilgan |
| `freight-infrastructure` | Docker Compose, reverse proxy, deployment, backup, monitoring va muhit konfiguratsiyasi | Rejalashtirilgan |
| `freight-contracts` | OpenAPI artefaktlari va typed client generatsiyasi | Rejalashtirilgan |

`freight-contracts` faqat shartnomalar va client generatsiyasiga egalik qiladi; biznes modeli yoki umumiy utilitalar uchun dumping ground bo‘lmaydi. `freight-infrastructure` application kodini saqlamaydi.

## Branching

- `main` doimo deploy qilish mumkin bo‘lgan holatda saqlanadi;
- qisqa umrli `feature/*` va `fix/*` branchlar ishlatiladi;
- doimiy ajralib ketuvchi `develop` branch faqat aniq release-management ehtiyoji paydo bo‘lsa qo‘shiladi.

## Chegara qoidalari

- repositorylar bir-birining ichki kodini nusxalamaydi;
- integratsiya versiyalangan shartnomalar orqali amalga oshiriladi;
- umumiy kutubxona faqat kamida ikki real iste’molchi va barqaror semantika bo‘lganda ajratiladi;
- secret va muhitga xos konfiguratsiya Git’ga kiritilmaydi.
