# Boshlang‘ich domen modeli

| Tushuncha | Ta’rif |
| --- | --- |
| `User` | Platformadan foydalanadigan shaxsning barqaror identifikatori |
| `Company` | Chegaralangan ruxsatli bir nechta a’zoga ega biznes akkaunt |
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

Bu model boshlang‘ich chegaralarni belgilaydi. Fieldlar va invariantlar implementation use case’lari bilan aniqlashtiriladi.
