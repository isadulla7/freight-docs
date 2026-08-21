# Modul chegaralari

## MVP modullari

| Modul | Mas’uliyat |
| --- | --- |
| `identity` | Telefon/OTP autentifikatsiyasi, sessiyalar, qurilmalar va token lifecycle |
| `accounts` | Foydalanuvchi, haydovchi profili, kompaniya, a’zolik, rol va ruxsatlar |
| `fleet` | Transport vositalari, imkoniyatlar, verifikatsiya va mavjud transport e’lonlari |
| `freight` | Yuk, cargo tafsilotlari, route stop, narx niyati, talablar va publication lifecycle |
| `marketplace` | Taklif/bid, taklif lifecycle va matching koordinatsiyasi |
| `shipment` | Taklif qabul qilingandan keyingi real tashish, assignment va holat tarixi |
| `communication` | Chat, notification, SMS/push orchestratsiyasi va aloqa sozlamalari |

Har bir modulning ownership tafsiloti [canonical kontekstning 10-bo‘limida](../../architecture/architecture-context-v1.md#10-backend-module-boundaries) berilgan.

## Bog‘liqlik qoidalari

- modul faqat kichik public API va domain eventlarni ochadi;
- boshqa modulning internal repository yoki persistence entity’siga kirish taqiqlanadi;
- cross-module koordinatsiya public application API yoki event orqali bajariladi;
- cyclic dependency taqiqlanadi;
- `shared` faqat haqiqiy fundamental primitive uchun ishlatiladi;
- biznes mantiq controller yoki persistence adapterda emas, domain/application qatlamida bo‘ladi;
- modul chegaralari Spring Modulith va arxitektura testlari bilan tekshiriladi.

## Ichki tuzilma

Core business modullar odatda quyidagi qatlamlarni saqlaydi:

```text
module/
├── domain/
├── application/
├── adapter/
│   ├── in/
│   └── out/
└── config/
```

Muhim aggregate uchun domain model va JPA persistence entity ajratiladi hamda mapper bilan bog‘lanadi. Oddiy reference/config data uchun foyda bermaydigan ceremony qo‘shilmaydi.

## Muhim domen farqi

`Load != Shipment`

`Load` — marketplace’dagi yuk tashish so‘rovi/e’loni. `Shipment` — taklif qabul qilingach yaratiladigan real tashish jarayoni.
