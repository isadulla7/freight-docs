# Boshlang‘ich domen modeli

| Tushuncha | Ta’rif |
| --- | --- |
| `User` | Platformadan foydalanadigan shaxsning barqaror identifikatori |
| `Company` | Chegaralangan ruxsatli bir nechta a’zoga ega biznes akkaunt |
| `CompanyMember` | User va Company orasidagi scoped rol/permission a’zoligi |
| `DriverProfile` | Foydalanuvchiga biriktirilgan haydovchi ma’lumotlari |
| `Vehicle` | Foydalanuvchi yoki kompaniya boshqaradigan transport aktivi |
| `AvailableVehicle` | Transportning vaqt va joy bilan chegaralangan mavjudlik bayonoti |
| `Load` | Shaxs yoki kompaniya yaratgan yuk tashish so‘rovi/e’loni |
| `LoadStop` | Tartiblangan pickup, delivery yoki oraliq nuqta; bir nechta stop boshidan qo‘llanadi |
| `Offer` | Haydovchi/tashuvchining vehicle va narxni o‘z ichiga olgan taklifi |
| `Shipment` | Taklif qabul qilingach yaratiladigan bajarilish jarayoni |
| `Rating` | Yakunlangan tranzaksiyadan keyingi baholash |

## Asosiy value objectlar

- `Money(amount, currency)`;
- `GeoPoint`;
- `RouteStop`;
- `Weight`;
- `Volume`;
- typed ID: `UserId`, `CompanyId`, `LoadId`, `OfferId`, `ShipmentId`.

`Money`, timestamp va identifier qoidalari [ADR-0011](decisions/0011-domain-data-conventions.md) da.

## Invariantlar

- `Load` faqat `from/to` maydonlari bilan cheklanmaydi; tartiblangan `LoadStop` pickup, intermediate va delivery nuqtalarini saqlaydi;
- `Vehicle` doimiy aktiv, `AvailableVehicle` vaqt/joy/direction bilan cheklangan e’lon;
- user type fragile numeric flag bilan emas, rol va capability permissionlar bilan;
- Company bitta userga bog‘lanmaydi, `CompanyMember` orqali ko‘p a’zoli;
- faqat bitta `Offer` load uchun g‘olib bo‘ladi, acceptance concurrency’dan transactional himoyalanadi;
- `Shipment` faqat accepted offerdan keyin yaratiladi.

Bu model boshlang‘ich chegaralarni belgilaydi. Fieldlar va invariantlar implementation use case’lari bilan aniqlashtiriladi.
