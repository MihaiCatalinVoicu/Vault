# Navigation Canonical Map — Reservation System

Last synthesized: 2026-05-25

## Legendă

- **Menu** = intrare principală vizibilă în nav/sidebar/header.
- **Submenu / route** = pagină copil sau rută operațională sub acel meniu.
- **Shared** = comun între verticalele relevante.
- **Vertical-specific** = doar pentru produsul respectiv.
- **Addon** = apare doar cu entitlement/package dedicat.

---

# 1. Shared Admin Surface — `:3000`

## General

| Menu | Route | Status |
|---|---|---|
| Dashboard | `/` | shared |
| FastOps | `/manager/fastops` | shared shell; modes Restaurant/Hotel/Spa |

## Operațiuni / Shared

| Menu | Route | Notes |
|---|---|---|
| Locații | `/locations` | shared |
| Spații | `/spaces` | shared |
| Echipa | `/users` | shared |
| Clienți | `/customers` | shared |
| Calendar | `/calendar` | shared |
| Billing | `/billing` | shared |
| Accounting Ops | `/admin/accounting-ops` | addon/shared |
| Accounting Ops Preview | `/admin/accounting-ops-preview` | alias/preview |
| Salarizare | `/hr/payroll` | shared/settings permission |
| Audit | `/admin/audit-trail` | shared |
| Rapoarte / Intelligence | `/intelligence` | shared/reporting |
| Revenue Analytics | `/intelligence/revenue` | reporting |
| Food Cost | `/intelligence/food-cost` | reporting, restaurant-heavy |
| Operational Efficiency | `/intelligence/operations` | reporting |
| Stock Intelligence | `/intelligence/stock` | reporting |
| Smart Suggestions | `/intelligence/suggestions` | reporting/action board |
| Menu Engineering | `/intelligence/menu-engineering` | restaurant-heavy |

## Setări

| Menu/Submenu | Route | Notes |
|---|---|---|
| Setări | `/settings` | shared |
| Fiscal | `/settings/fiscal` | duplicate route in current router must be consolidated |
| SAF-T | `/settings/compliance/saft` | compliance |
| Integrări | `/settings/integrations` | shared |
| Alerts / WhatsApp / AI keys | `/settings/integrations/alerts` | shared |
| Website Widget | `/settings/integrations/website` | restaurant/client embed |
| SAGA Export | `/settings/integrations/saga` | accounting |
| WinMentor Export | `/settings/integrations/winmentor` | accounting |
| WizCount Export | `/settings/integrations/wizcount` | accounting |
| Pricing Rules | `/pricing/rules` | shared/restaurant-heavy; currently route exists but nav must be explicit |

## Business Ops Add-on

| Menu | Route | Notes |
|---|---|---|
| Business Pulse | `/admin/business-pulse` | add-on; filters ALL/FITNESS/RESTAURANT/HOTEL/SPA |

Internal sections:
- Executive summary
- Action items
- Warnings
- Dynamic contributor sections
- Source status
- Recent snapshots

---

# 2. Restaurant

## Canonical Restaurant menu

| # | Menu | Route | Module / Notes |
|---:|---|---|---|
| 1 | Mese | `/tables` | restaurant tables |
| 2 | Floor Board | `/restaurant/floorplan` | `RESTAURANT_FLOOR` |
| 3 | Nota / POS | `/restaurant/pos` | `RESTAURANT_POS`; alias `/pos` |
| 4 | Meniu | `/restaurant/menu` | menu catalog |
| 4.1 | Meniu Wizard | `/restaurant/menu/new-wizard` | child route |
| 5 | Politici rezervări | `/admin/restaurant/booking-policy` | booking policy |
| 6 | Rezervări | `/reservations` | `RESTAURANT_TABLE_RESERVATIONS`; alias `/table-reservations` redirects |
| 7 | Intake | `/reservation-intake` | `INTAKE_COMMAND_CENTER` |
| 8 | Waitlist | `/waitlist` | restaurant |
| 9 | Inventar | `/inventory` | `RESTAURANT_INVENTORY` |
| 10 | Sertar / Tură | `/cash-drawer` | cash drawer/shift |
| 11 | KDS Stations | `/manager/kds-stations` | `RESTAURANT_KDS` |
| 12 | Printers | `/manager/printers` | POS/KDS config |

## Restaurant FastOps mode

| Action | Purpose |
|---|---|
| Find Reservation | caută rezervare |
| Seat Guest | așază guest la masă |
| New Walk-in | creează walk-in |
| Add Products | adaugă produse pe sesiune |
| Send to Kitchen | trimite itemele pending la bucătărie |
| Take Payment | cash/card/room charge/voucher |

Workspace:
- table grid
- waitlist/recovery
- active order preview
- session detail rail

---

# 3. Hotel

## Canonical Hotel menu

| # | Menu | Route | Module / Notes |
|---:|---|---|---|
| 1 | Front Desk | `/admin/front-desk` | `HOTEL_ROOM_RESERVATIONS` |
| 2 | Tipuri Cameră | `/admin/hotels/room-types` | `HOTEL_ROOMS` |
| 3 | Politică Rezervare | `/admin/hotels/booking-policy` | hotel policy |
| 4 | Housekeeping | `/admin/housekeeping` | `HOTEL_HOUSEKEEPING` |
| 5 | Inspector | `/admin/housekeeping/inspector` | housekeeping inspector |
| 6 | Group Blocks | `/admin/group-blocks` | B2B/groups |
| 7 | Tourist Register | `/admin/hotels/tourist-register` | RO compliance |
| 8 | Night Audit | `/admin/hotels/night-audit` | hotel close/day audit |
| 9 | KPI Hotel | `/admin/hotels/kpis` | occupancy/ADR/RevPAR |
| 10 | Mentenanță | `/admin/hotels/maintenance` | work orders |
| 11 | Rate Grid | `/admin/hotels/rate-grid` | rates |
| 12 | Reservations Grid | `/admin/hotels/reservations-grid` | reservation grid |
| 13 | Channel Health | `/admin/hotels/channel-health` | `HOTEL_EXTERNAL_CHANNELS` |
| 14 | External Bookings | `/admin/hotels/external-bookings` | `HOTEL_EXTERNAL_BOOKINGS` |
| 15 | Guest CRM 360 | `/admin/hotels/guest-crm-360` | guest intelligence |
| 16 | Accounting Export | `/admin/accounting-export` | hotel/accounting |
| 17 | Accounting Ops | `/admin/accounting-ops` | shared addon |
| 18 | Report Builder | `/admin/hotels/report-builder` | hotel reports |
| 19 | Print Center | `/admin/hotels/print-center` | print docs |
| 20 | Forecast | `/admin/forecast` | forecast / production suggestions |

## Hotel FastOps mode

| Action | Purpose |
|---|---|
| Check-in | marchează sosirea / predă cheia |
| Check-out | închide stay / settlement |
| Find Booking | caută booking |
| Walk-in | creează stay fără rezervare |
| Add Room Charge | adaugă charge pe cameră |
| Room Status | schimbă status cameră |

Workspace:
- room grid
- guest/stay panel
- minibar quick add
- room/service detail rail

---

# 4. Spa

## Canonical Spa menu

| # | Menu | Route | Module / Notes |
|---:|---|---|---|
| 1 | Spa Reservations | `/admin/spa/reservations` | `SPA_RESERVATIONS` |
| 2 | Appointments | `/admin/spa/appointments` | appointment board |
| 3 | External Intake | `/admin/spa/external-appointments` | `SPA_EXTERNAL_APPOINTMENTS` |
| 4 | Servicii | `/admin/spa/services` | `SPA_SERVICES` |
| 5 | Cabinete / Rooms | `/admin/spa/rooms` | `SPA_TREATMENT_ROOMS` |
| 6 | Providers / Terapeuți | `/admin/spa/providers` | `SPA_THERAPISTS` |
| 7 | Calendar | `/admin/spa/calendar` | provider calendar |
| 8 | Booking Rules | `/admin/spa/booking-rules` | rules/policy |
| 9 | Daily Z Report | `/admin/spa/z-report` | fiscal/day report |

## Spa FastOps mode

| Action | Purpose |
|---|---|
| Check-in Client | marchează sosirea clientului |
| Find Appointment | caută programare |
| Assign Therapist | alocă terapeut |
| Assign Room | alocă cabinet |
| Start Service | pornește serviciul |
| Complete & Pay | finalizează și încasează |

Workspace:
- timeline
- resource board
- package/client panel
- service detail rail

---

# 5. Fitness

## Canonical Fitness menu

| # | Menu | Route | Module / Notes |
|---:|---|---|---|
| 1 | Fitness Dashboard / Retention Pulse | `/admin/fitness/dashboard` | `FITNESS_RETENTION_PULSE` |
| 2 | Members | `/admin/fitness/members` | `FITNESS_MEMBERS` |
| 3 | Membership Plans | `/admin/fitness/membership-plans` | `FITNESS_MEMBERSHIPS` |
| 4 | Billing Status | `/admin/fitness/billing` | guarded by `FITNESS_MEMBERSHIPS`; no separate module code |
| 5 | Classes | `/admin/fitness/classes` | `FITNESS_CLASSES` |
| 6 | Trainers | `/admin/fitness/trainers` | `FITNESS_TRAINERS` |
| 7 | Check-ins | `/admin/fitness/checkins` | `FITNESS_CHECKINS` |

Dashboard internal sections:
- Active Members
- Active Subscriptions
- Expired Memberships
- Expiring Soon
- Check-ins Today
- Check-ins This Week
- Classes This Week
- Retention Risk
- Top Classes

## Fitness add-ons

| Menu | Route | Notes |
|---|---|---|
| Business Pulse | `/admin/business-pulse` | only with `BUSINESS_PULSE` entitlement |
| Accounting Ops | `/admin/accounting-ops` | if package includes it |

## Fitness hard rule

Fitness must not depend on Restaurant/Hotel/Spa domain switcher. Fitness-only tenant should see Fitness by default, not an empty menu.
