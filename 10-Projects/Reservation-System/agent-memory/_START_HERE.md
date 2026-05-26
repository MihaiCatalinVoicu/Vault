# START HERE — Reservation System Agent Memory

## Citește întâi

1. `00_PROJECT_CONSTITUTION.md`
2. `10_NAVIGATION_CANONICAL_MAP.md`
3. `20_VISIBILITY_AND_PACKAGING.md`
4. `30_NAVIGATION_AUDIT_CHECKLIST.md`
5. `40_KNOWN_DRIFT.md`

## Context stabil

Produsul este VenueHub / Reservation-System.

Suprafețe principale:

| Suprafață | Port | Rol |
|---|---:|---|
| Admin / staff / manager | `3000` | creierul operațional: restaurant, hotel, spa, fitness, accounting, fast ops |
| Client/public | `3001` | momentan pending/dead; nu trebuie folosit ca dependență critică |
| Superadmin | `3100` | control platformă, tenants, packages, billing/operator truth |
| Backend | Spring Boot | API + auth + VPF + business logic |

## Regulă centrală

Meniurile nu se modifică într-un singur fișier. Orice schimbare de meniu/rută trebuie sincronizată în:

1. `frontend/src/router/index.tsx`
2. `frontend/src/components/Header/Header.jsx`
3. `frontend/src/components/AdminLayout.jsx` (`resolveActiveTab`)
4. `frontend/src/admin/visibility/moduleRouteRegistry.ts`
5. `docs/product/VERTICAL_PACKAGING_MATRIX.md`
6. `docs/product/MODULE_VISIBILITY_RULES.md`
7. `docs/agent-memory/10_NAVIGATION_CANONICAL_MAP.md`

Dacă unul rămâne în urmă, produsul se rupe în stilul foarte uman „mergea ieri”.
