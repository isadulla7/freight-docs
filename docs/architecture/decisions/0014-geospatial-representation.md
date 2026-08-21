# ADR-0014: Geospatial point representation va SRID

- Holat: Qabul qilindi
- Sana: 2026-08-21
- Qaror egalari: Loyiha egasi
- Tegishli ADR: [ADR-0003](0003-persistence.md)
- Tafsilotlar: [Database ERD](../database-erd.md#postgis-v1)

## Kontekst

Nearby load/vehicle querylari metrda radius, O‘zbekiston va keyingi xalqaro kengayish uchun yer yuzidagi koordinata semantikasiga muhtoj. Alohida latitude/longitude va spatial column drift qilishi mumkin.

## Qaror

- pickup, delivery va availability pointlar `geography(Point,4326)`;
- SRID 4326 (WGS 84 longitude/latitude);
- GiST indeks required;
- geography column authoritative, alohida stored latitude/longitude takrorlanmaydi;
- API/read projection koordinatani spatial columndan chiqaradi;
- distance/radius `ST_DWithin` kabi PostGIS query bilan metrda, server-side;
- exact location purpose/scoped authorization bilan minimallashtiriladi.

## Variantlar

- decimal lat/lon: simple, ammo index/distance va integrity zaif.
- `geometry(Point,4326)`: planar operationlar qulay, lekin radius unit/earth-distance handlingda ehtiyotkor transform talab qiladi.
- `geography(Point,4326)`: MVP radius use case’i uchun metr semantikasi aniq.

## Oqibatlar

- nearby query sodda va to‘g‘ri unitda;
- ayrim advanced route/geometry operationlari keyin explicit cast/projection talab qilishi mumkin;
- spatial integration va execution-plan testlari majburiy.

## Xavfsizlik va maxfiylik

GiST index access control emas. Query radiusi, result limit va response precision server tomonidan cheklanadi; exact availability/contact location mass listing response’da default ochilmaydi.

## Tekshirish

- SRID/check migration testlari;
- real PostGIS Testcontainers radius/boundary testlari;
- query plan GiST ishlatishini tekshirish;
- API location minimization authorization testlari.
