# Architecture v1.0 open questions

Faqat canonical context va accepted ADRlardan finalize qilib bo‘lmaydigan qarorlar. Owner/date qo‘yilib hal qilinganda tegishli ADR/domain/privacy hujjati yangilanadi.

## BLOCKING before backend foundation

**None.** Module skeleton, persistence conventions, identity/session baseline, authorization entry pattern, UUID/time/money, schema ownership va PostGIS representation locked. Backend repository yaratish hali ushbu PR review/merge gate’idan keyin.

## NON-BLOCKING before relevant MVP feature implementation

| Decision | Must be resolved before | Why unresolved |
| --- | --- | --- |
| Phone-number change va account recovery proof, cooldown, notification/revoke flow | recovery/change endpoint | Canonical context separate threat review talab qiladi; product/support proof hali yo‘q |
| Driver va vehicle verification evidence, decision states/reason codes, expiry/re-review va verifier operating policy | verification feature | Architecture scope/accessni lock qildi, lekin business/compliance evidence criteria berilmagan |
| Load, Offer va AvailableVehicle default expiry durations va extension policy | scheduled expiry behavior | States locked, business durations intentionally server configuration; product input yo‘q |
| Matchdan keyingi cancellation, failed pickup/delivery, driver/vehicle reassignment va dispute actor/consent/history | shu commands yoki UI | Canonical context alternative flows keyin aniqlanishini aytadi; v1 normal lifecycle ularni taxmin qilmaydi |
| `DELIVERED`dan keyin shipper tasdiqlamasa auto-completion/reminder policy | shipment completion automation | Normal manual confirmation locked; fallback/timing product qarori yo‘q |
| Chat pre-acceptance mavjudmi, message report/moderation, edit/delete va attachment policy | chat feature beyond shipment participant messaging | Canonical chatni rejalashtiradi, lekin exposure/moderation scope bermaydi |
| Notification/push provider selection, transactional vs optional message types va preference override | push adapter/production notification | FCM faqat likely candidate; privacy/provider inventory va product policy kerak |
| Public load/vehicle feedning exact field/coordinate precisioni va anonymous access scope | public feed OpenAPI | Minimization/scoped rule locked; product discovery vs scraping/privacy balance field-by-field tasdiqlanmagan |
| Ratings ownership, eligibility window, one-rating uniqueness va moderation | ratings implementation | Ratings MVP candidate, lekin locked seven modules ichida owner/lifecycle aniqlanmagan; yangi module yoki existing ownership ADR talab qiladi |

## DEFERRED future product/legal questions

| Decision | Trigger |
| --- | --- |
| Uzbekistan-specific lawful basis, category retention periods, deletion/export/correction, business record preservation | public production launch; **OPEN LEGAL DECISION** |
| Third-party processor region, retention, DPA/subprocessor va cross-border treatment | har production provider tanlovi |
| Live GPS consent, sampling, viewer precision, retention va battery/network limits | live tracking scope boshlanishi |
| Payment/escrow/commission, financial permissions, ledger, provider va dispute model | payment feature proposal |
| Enterprise company role catalogue, accountant/report/export controls va partner API | enterprise scope |
| International route, currency/business/legal rules | international expansion |
| Emergency break-glass admin approval/authentication/expiry process | real operational need isbotlansa |
| Dedicated broker/search engine/microservice extraction | measured throughput/isolation/team ownership talabi |

Resolved qarorlar bu fayldan o‘chiriladi va ADR yoki authoritative focused docga ko‘chiriladi; historical debate uchun Git/ADR saqlanadi.
