# Domen invariantlari — Architecture v1.0 Lock

Quyidagi qoidalar UI convenience emas; backend domain/application qatlamida va tegishli database constraint/transaction bilan himoyalanadi.

## Non-negotiable

1. `Load != Shipment`: load marketplace listingi, shipment accepted offerdan keyingi execution.
2. Har bir load uchun ko‘pi bilan bitta accepted/winning `Offer` mavjud.
3. Offer acceptance concurrency-safe: load/offer version yoki row lock, bitta transaction va partial unique accepted-offer constraint ishlatiladi.
4. State machine’da ko‘rsatilmagan lifecycle transition backend tomonidan rad etiladi.
5. `Company` bir nechta `CompanyMember`ga ega bo‘lishi mumkin; company bitta userga bog‘lanmaydi.
6. Role va permission server-side source of truth; UI role labeli ruxsat bermaydi.
7. `AvailableVehicle != Vehicle`: biri vaqt/joy listingi, biri persistent asset.
8. Nearby load/vehicle filtering PostGIS orqali server-side; client barcha recordni olib local filter qilmaydi.
9. Money floating point ishlatmaydi; amount safe integer/minor-unit representation va currency code bilan.
10. Server timestamps UTC’da saqlanadi.
11. Public/domain IDlar UUID; sequential internal ID API identifikatori sifatida ochilmaydi.
12. Sensitive resource har safar actor, action, tenant/resource relationship bo‘yicha scoped authorization talab qiladi.
13. Modul boshqa modul jadvalini to‘g‘ridan-to‘g‘ri mutate qilmaydi.
14. Modul boshqa modulning repository yoki persistence entitysini ishlatmaydi; public API/event contract chegarasi saqlanadi.
15. `shared/common` business policy, repository yoki cross-module entity egasi bo‘lmaydi.
16. Controller va infrastructure domen qarorini qabul qilmaydi.

## Modeldan kelib chiqadigan qo‘shimcha invariantlar

- published load kamida bitta pickup va bitta delivery stopga ega; stop sequence load ichida unique;
- shipment faqat accepted offerdan yaratiladi va accepted offer/load uchun ko‘pi bilan bitta shipment mavjud;
- shipment status transitioni va immutable status-history recordi atomik yoziladi;
- sensitive driver/vehicle document private object storage’da, permanent public URL’siz;
- raw OTP, access token yoki refresh token log/persistence plaintextida saqlanmaydi;
- active company membership `(company_id, user_id)` bo‘yicha unique;
- conversation va message faqat authorized participantlarga ko‘rinadi;
- exact current/availability location public listingda default ochilmaydi; purpose va viewer scope bo‘yicha minimallashtiriladi;
- cross-module foreign key `ON DELETE CASCADE` bilan boshqa modul lifecycleini boshqarmaydi;
- reliable event handler duplicate deliveryga idempotent javob beradi.

Constraint va transition mapping [database ERD](database-erd.md), [state machines](state-machines.md) va [event katalogi](event-catalog.md)da berilgan.
