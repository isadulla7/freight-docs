# ADR-0006: Mobil va web client platformalari

- Holat: Qabul qilindi
- Sana: 2026-08-21
- Qaror egalari: Loyiha egasi
- Manba: [Architecture Context v1.0](../../../architecture/architecture-context-v1.md)

## Kontekst

Mobil va desktop-heavy oqimlar bir xil backend shartnomasidan foydalanishi, lekin o‘z platformasiga mos mustaqil client bo‘lishi kerak.

## Qaror

- Android/iOS mobil client Flutter bilan;
- state management Riverpod, routing `go_router` bilan;
- mobil kod feature-first tuziladi;
- UI HTTP clientni bevosita chaqirmaydi: Screen -> Controller/ViewModel -> Repository/Use Case -> generated API client;
- primary web strategiya Next.js, React va TypeScript;
- Flutter Web primary web strategiya emas;
- web va mobil bir-biridan mustaqil, bir xil OpenAPI contract iste’molchilari.

## Oqibatlar

- platformalarning UX va release lifecycle’i mustaqil qoladi;
- shartnoma driftini generated client va contract checklar erta aniqlaydi;
- umumiy business logic clientlar orasida ko‘r-ko‘rona nusxalanmaydi, server qoidalari serverda qoladi.

## Tekshirish

- mobile/web build generated client bilan;
- UI qatlamidan raw HTTP chaqiruvini cheklovchi lint/arxitektura qoidalari;
- contract compatibility CI tekshiruvi.
