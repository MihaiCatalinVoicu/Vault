---
title: Reservation Intake architecture audit
type: decision
project: Reservation-System
date: 2026-05-19
status: research
impact: strategic
tags:
  - decision/reservation
  - product/reservation
  - analysis
  - research
---

# 2026-05-19 — Reservation Intake architecture audit

User a primit pe alt thread o analiză externă (Claude Small Business plugin? cross-thread?) care propune arhitectură pentru "external reservation integration" — `external_provider_config`, `external_reservation_event`, normalizers, mappers pe vertical, etc. + caz de uz Restaurant/Hotel/Spa. Întrebare: ce există vs ce lipsește? Plus: analiza sare peste **AirBNB** ca platformă obligatorie pentru Hotel.

## Verdict scurt

**Analiza propune arhitectură care în mare parte EXISTĂ DEJA**, livrată în 10 migrații DB (V9005→V9014) și ~123 fișiere Java în `reservationsystem/`. Gap-urile reale sunt altele.

## Ce există (mapat pe propunerile analizei)

| Propunere | Ce ai deja |
|---|---|
| `external_provider_config` | `ExternalReservationInboundRoute` + `ExternalProviderPolicy` |
| `external_reservation_event` (raw box) | `ExternalReservationInboxItem` cu `raw_payload_text/json` |
| `external_reservation` normalized | Inbox cu statusuri PARSED/READY/NEEDS_REVIEW/CONFLICT |
| Idempotency | DB unique index `(tenant, provider, externalReservationId)` |
| Conflict | `ReservationConflictService` + `ExternalReservationConflictReason` |
| Router | `ExternalReservationInboundRouteService` |
| Decision engine | `ExternalReservationDecisionService` + 4 mode enums |
| Table assignment | `ExternalReservationTableAssignmentService` cu `Strategy` enum |
| Promotion (inbox → real) | `ExternalReservationPromotionService` (azi DOAR restaurant) |
| Autopilot framework | `ExternalReservationAutopilot*` (run, ops, triggers, settings) |
| Email pipeline | `ExternalReservationEmailIngestionService` + `NormalizationService` |
| Bulk paste | `ExternalReservationBulkService` + `BulkParserService` |
| Command Center UI | `ExternalReservationCommandCenterService` + Admin controller |
| Hotel iCal channel | `Hotel.icalToken` + `AdminHotelChannelController` (GET/PUT/import) + `PublicHotelCalendarController` (outgoing feed) + `ExternalCalendarService` |
| Sprint S-H-CH-1 (Channel Health Dashboard) | livrat (sprint-150) |

## Provideri în enum azi

```
IALOC, GOOGLE, BOOKING_COM, OPENTABLE, TABLEO, QUANDOO,
INSTAGRAM_DM, FACEBOOK_DM, PHONE, EMAIL, WALK_IN,
WEBSITE_FORM, WHATSAPP, CSV, BULK_PASTE, OTHER
```

**AIRBNB lipsește.** Doar menționat în scenarii test + roadmap "ne lipsește".

## Gap-uri reale identificate

### 🔴 Gap 1: AIRBNB lipsește din `ExternalReservationProvider`
1 line în enum + migration V90xx cu CHECK constraint update.

### 🔴 Gap 2: Hotel iCal single-channel
`Hotel.externalCalendarUrl` = UN câmp. Real-world hotel mic vrea simultan AirBNB iCal + Booking.com iCal + Expedia iCal. Refactor → tabel separat `hotel_external_channel (id, hotel_id, channel_provider, external_calendar_url, last_sync_at, last_sync_status, enabled)`.

### 🔴 Gap 3: No `ExternalReservationToHotelStayMapper`
Promotion service mapează inbox → `TableReservation` (restaurant). Pentru hotel: iCal event AirBNB intră în inbox dar **nu se transformă automat în HotelStayReservation**. Asta e mapper-ul lipsă pentru end-to-end hotel pipeline.

### 🔴 Gap 4: Outbound rate/inventory push lipsește
Outbound iCal feed există (PublicHotelCalendarController publică `.ics`). DAR:
- Fără push prețuri (rate sync)
- Fără push restricții (min stay, closed-to-arrival)
- Fără room-blocks atomic

Pentru hotel mic = overbooking risk în fereastra de lag iCal (3-24h pe canalele majore). Per `docs/PRODUCT_HARDENING_ROADMAP.md`: "Zero API direct Booking/Airbnb/Expedia; zero webhook inbound; zero rate/inventory push".

### 🔴 Gap 5: Adapter framework Tier-3 — schelet, nu implementare
Strategy doc menționează "Sprint 13 — EXT-PROVIDER-001: Provider adapter framework". Grep nu găsește adapter-uri reale (`IalocAdapter`, `BookingComAdapter`, `AirbnbAdapter`). Probabil neîncepute.

### 🟡 Gap 6: Sprinturile core neexecutate
Din 15 sprinturi planificate în [[Reservation Intake Command Center strategy]] (RES-CORE-001 → EXT-HARDEN-001), doar ~5-6 par livrate. Lipsesc:
- RES-CORE-001 (duration & conflict truth — **CRITIC blocker** dacă încă există overbooking)
- EXT-BULK-001 (bulk paste parser real)
- EXT-COMMAND-001 (Reservation Command Center UI complet)
- EXT-PROVIDER-001 (adapter framework)
- EXT-HARDEN-001 (E2E + load + Playwright)

## AirBNB specifically

| | Booking.com | Airbnb |
|---|---|---|
| API public pentru host individual | Nu — Connectivity API doar parteneri certificați, **paused 2024+** | Nu — Channel API doar parteneri PMS/CM agreați |
| iCal export (citim) | Da (per property), read-only, lag minute-hours | Da (per listing), read-only, lag ~3-24h |
| iCal import (le dăm) | Da — Booking acceptă URL extern pentru blocare | Da — Airbnb "Sync calendars" → paste URL |
| Realtime push | Nu fără Connectivity Partner | Nu fără Channel Partner |
| Detalii pe iCal event | "Closed - Imported" + uneori guest reference | Nume, check-in/out, reference; **NU email/phone** |
| Overbooking risk fără 2-way | High | High |

**Reality pentru solo dev fără parteneriat:** ambele = iCal-only, 2-way, lag 30-180 min normal. Pentru hotel mic (3-15 camere), iCal-only e acceptabil dacă declari explicit "lag până la 1h" + implementezi outbound feed corect.

## Sprint propus

`EXT-HOTEL-MULTICHANNEL-001` — ~5-7 zile work, folosește 100% infrastructura existentă (inbox, decision, autopilot):

1. Migration V9015 — `hotel_external_channel` table (multi-row per hotel)
2. Add AIRBNB la `ExternalReservationProvider` enum
3. Refactor `ExternalCalendarService` — import per (hotel, channel) tuple
4. Admin UI: list/add/remove/test pe canal
5. Periodic sync timer (Spring @Scheduled, 15 min) — toate canalele enabled
6. `ExternalReservationToHotelStayMapper` — iCal events → HotelStayReservation
7. Channel Health Dashboard extension — per-canal status
8. E2E test: Airbnb iCal 3 evente → 3 stays vizibile în `:3000` hotel front desk
9. Cross-canal overbooking detection — log + warn (nu blochează)

## Recomandare

Threadului Reservation:

> Analiza pe care ai primit propune arhitectură care în mare parte EXISTĂ DEJA. Înainte de orice "let's build it", citește `docs/strategy/RESERVATION_INTAKE_COMMAND_CENTER.md` și `CLAUDE.md` (anti-patterns secțiunea). Gap-urile reale: AIRBNB enum + multi-channel hotel + HotelStayMapper + outbound rate push + sprinturile core neexecutate. Începe cu RES-CORE-001 dacă încă nu e închis (duration & conflict — blocant pentru tot ce e above).

## Cross-refs

- [[Reservation-System]]
- [[2026-05-16 Reservation-System pre-pilot readiness]]
- [[2026-05-16 Pre-mortem Reservation-System]]
- Strategy doc local: `docs/strategy/RESERVATION_INTAKE_COMMAND_CENTER.md`
- Hardening roadmap local: `docs/PRODUCT_HARDENING_ROADMAP.md`
