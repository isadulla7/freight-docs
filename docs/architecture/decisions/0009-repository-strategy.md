# ADR-0009: Multi-repository chegaralari

- Holat: Qabul qilindi
- Sana: 2026-08-21
- Qaror egalari: Loyiha egasi
- Manba: [Architecture Context v1.0](../../../architecture/architecture-context-v1.md)

## Qaror

Quyidagi alohida repositorylar ishlatiladi:

- `freight-backend`;
- `freight-mobile`;
- `freight-web`;
- `freight-infrastructure`;
- `freight-contracts`;
- `freight-docs`.

Backend, mobile, web va infrastructure bitta yirik monorepo’ga birlashtirilmaydi; o‘zgarish faqat keyingi ADR bilan.

## Chegaralar

- integratsiya versiyalangan contract orqali;
- repository boshqa repositoryning internal kodini nusxalamaydi;
- umumiy library faqat real, takroriy iste’molchi va barqaror semantika bo‘lsa ajratiladi;
- `main` deployable, ishlar qisqa umrli branch va PR orqali.

## Oqibatlar

- deploy va ownership chegaralari aniq;
- cross-repository contract/version koordinatsiyasi talab qilinadi;
- docs va contract repositorylari dumping groundga aylantirilmaydi.
