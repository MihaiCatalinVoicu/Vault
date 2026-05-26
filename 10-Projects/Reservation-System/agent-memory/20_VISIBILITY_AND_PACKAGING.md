# Visibility & Packaging — Canonical Notes

## Shared modules

| Module | Scop |
|---|---|
| `DASHBOARD` | overview tenant/venue |
| `STAFF` | utilizatori, echipă, roluri |
| `CUSTOMERS` | clienți / CRM |
| `BILLING` | billing/commercial/payment tracking |
| `DOCUMENTS` | documente generate/uploadate |
| `REPORTS` | rapoarte / intelligence |
| `SETTINGS` | setări tenant, integrări, module |
| `BUSINESS_PULSE` | add-on cross-vertical, nu bază fitness |
| `ACCOUNTING_OPS` | add-on accounting/compliance |

## Shared only where reservation concept exists

| Module | Aplicație |
|---|---|
| `RESERVATIONS` | Restaurant / Hotel / Spa |
| `INTAKE_COMMAND_CENTER` | Restaurant / Hotel / Spa; nu bază Fitness |

## Vertical packages

| Package | Include |
|---|---|
| `COMMON_OPS_BASIC` | dashboard, staff, customers, docs, reports, settings |
| `RESTAURANT_OPS` | floor, POS, KDS, inventory, suppliers, table reservations |
| `HOTEL_OPS` | rooms, room reservations, channels, external bookings, housekeeping, check-in/out |
| `SPA_OPS` | reservations, external appointments, services, therapists/providers, treatment rooms |
| `FITNESS_OPS` | members, memberships, classes, trainers, check-ins, retention pulse |
| `BUSINESS_OPS_ADDON` | Business Pulse, payment obligations, reminders, margin light |
| `ACCOUNTING_OPS_ADDON` | supplier inbox, document pack, e-Factura preview, accounting exports |

## Enforcement

Backend visibility owns the business decision:

```text
visibleModules = modules
  where module in packageEntitlements
  and module compatible with activeScope.vertical
  and module compatible with activeScope.venue
  and user has permission
  and module is enabled
```

Frontend renders projection. Frontend must not invent visibility.
