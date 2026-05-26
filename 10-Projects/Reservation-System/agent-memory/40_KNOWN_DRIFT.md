# Known Current Drift — 2026-05-25 (last update 2026-05-26 post-S22+S23)

## Update history

| Date | Sprint | Change |
|------|--------|--------|
| 2026-05-25 | (baseline) | Initial drift inventory captured. |
| 2026-05-26 | **S21** | Fitness vertical group + Business Pulse restored in Header fallback nav. |
| 2026-05-26 | **S22** | Fitness route guards aligned with Restaurant/Hotel/Spa pattern (no more `FITNESS_*` granular module gate). |
| 2026-05-26 | **S23** | Mainline Continuity OS shipped (ledger + checker + watchdog) — not a drift fix per se, but prevents next "merged ≠ on main" miss. |
| 2026-05-26 | **S24 (deferred)** | NEW finding: Fitness admin GET endpoints return HTTP 500 with valid JWT (surfaced by S22 making routes accessible). Tracked in `docs/ops/BRANCH_STACK.json`. |

## Critical — CLOSED

1. ~~Header fallback does not expose Fitness at all.~~ — **CLOSED S21** (`441a9ed9`). Header.jsx gains `canSee.fitnessOps` + legacy nav group with 7 Fitness items.
2. ~~Header fallback does not expose Business Pulse at all.~~ — **CLOSED S21** (`441a9ed9`). Header.jsx gains `canSee.businessPulse` + legacy nav entry.
11. ~~Fitness route guards use `FITNESS_*` granular modules with `allowVpfFallback={false}`, returning 403 even when role + parent FITNESS vertical match.~~ — **CLOSED S22** (`800067cf`). All 7 Fitness routes now use shared `ProtectedRoute module="FITNESS" requiredRoles=[ADMIN, MANAGER, SUPER_ADMIN]` pattern (same as Restaurant/Hotel/Spa). Regression tripwire test added: `frontend/src/admin/visibility/fitnessRouteGuard.test.js`.

## Critical — OPEN

3. **DomainSwitcher supports only auto/Restaurant/Hotel/Spa; no Fitness option.**
   - Impact: when operator manually narrows scope on a multi-vertical tenant, Fitness disappears from filter UI even if entitled.
   - Owner: TBD.
4. **`moduleRouteRegistry.ts` does not include several real router routes** (see registry gaps section below).
5. **`resolveActiveTab` does not cover several real router routes** — active tab highlight broken for many surfaces.
6. **`/settings/fiscal` appears duplicated in router** and must be consolidated to a single component.
7. **`GROUP_SORT_ORDER` lacks `BUSINESS_OPS`** — Business Pulse group falls to default sort order (last), not where operator expects it.
8. **Spa subroutes all resolve to active tab `spa`** — hides submenu distinction; clicking Calendar/Booking Rules/Z Report shows same tab highlight.
9. **`HOTEL_ROOM_RESERVATIONS` and `HOTEL_CHECKIN_CHECKOUT` share `/admin/front-desk`** — visibility nav de-duplicates by route → will omit one of the two entries.
10. **Restaurant menu/policy/cash drawer/printers are not present in visibility registry** even though they exist in router/header.

## NEW open — S22 surface, tracked separately

12. **`FITNESS-500-INVESTIGATION` — Fitness admin GET endpoints HTTP 500** with valid JWT.
    - Affected: `/api/v1/admin/fitness/{dashboard,members,membership-plans,classes,checkins,trainers,billing}`.
    - Surfaced by: S22 making routes accessible (was previously masked by 403 from `FITNESS_*` route guard).
    - Likely root cause: schema mismatch on local DB (V9068/V9069/V9070 not Flyway-applied; Hibernate `ddl-auto=update` may not have created all columns) OR VPF visibility check throwing on null aggregate.
    - Required evidence: live BE stdout capture for requestId trace (current `reservationsystem/logs/error.log` is stale — caches a startup JWT_SECRET failure from earlier in the day, not runtime exceptions).
    - Tracking: `docs/ops/BRANCH_STACK.json` entry id `FITNESS-500-INVESTIGATION` (status `PLANNED`).
    - Owner: deferred to follow-up sub-sprint.

## Registry gaps to add or consciously classify as shared/config

### Restaurant

- `/tables`
- `/restaurant/menu`
- `/restaurant/menu/new-wizard`
- `/admin/restaurant/booking-policy`
- `/cash-drawer`
- `/manager/printers`

### Hotel

- `/admin/hotels/booking-policy`
- `/admin/group-blocks`
- `/admin/hotels/tourist-register`
- `/admin/hotels/night-audit`
- `/admin/hotels/kpis`
- `/admin/hotels/maintenance`
- `/admin/hotels/rate-grid`
- `/admin/hotels/reservations-grid`
- `/admin/hotels/guest-crm-360`
- `/admin/hotels/report-builder`
- `/admin/hotels/print-center`
- `/admin/forecast`

### Spa

- `/admin/spa/appointments`
- `/admin/spa/calendar`
- `/admin/spa/booking-rules`
- `/admin/spa/z-report`

### Fitness

- `/admin/fitness/billing` must be either:
  - separate registry item using existing `FITNESS_MEMBERSHIPS`, or
  - child submenu under Memberships.
- After S22, the route guard no longer blocks operator entry; this registry entry is still needed for proper navigation visibility (left-nav rendering, scope filtering).

### Business Ops

- `/admin/business-pulse` must be in activeTab resolver and visible nav when entitled. (Header fallback gained the link in S21 but `resolveActiveTab` still missing.)

## Correction priority (updated post-S22)

1. ~~Make canonical map authoritative.~~ — **DONE** (this vault). Canonical map at `10_NAVIGATION_CANONICAL_MAP.md` is now the source of truth.
2. ~~Restore Fitness in Header fallback nav.~~ — **DONE** S21.
3. ~~Align Fitness route guards with Restaurant/Hotel/Spa.~~ — **DONE** S22.
4. **Investigate FITNESS-500-INVESTIGATION** — pre-blocker for Fitness UX on local dev / fresh tenant. New top priority.
5. Update `moduleRouteRegistry.ts` to include all implemented routes (Critical OPEN #4, #10).
6. Add Fitness to DomainSwitcher OR replace DomainSwitcher with VPF scope selector as source of truth (Critical OPEN #3).
7. Add activeTab mappings (Critical OPEN #5, #7, #8, Business Ops registry gap).
8. Consolidate `/settings/fiscal` duplicate (Critical OPEN #6).
9. Resolve `HOTEL_ROOM_RESERVATIONS` / `HOTEL_CHECKIN_CHECKOUT` route collision (Critical OPEN #9).
10. Add nav audit test (CI script that fails when a router route lacks a registry entry or activeTab mapping).
11. Only then polish labels/UI.

---

**Acceptance lessons codified**:

- The pattern "create granular route guards that require BE-published modules + `allowVpfFallback={false}`" creates a route that **looks open in nav but slams 403** when the BE visibility context doesn't publish those granular codes. Fitness was the case study; same pattern would break Restaurant/Hotel/Spa if anyone copied it. S22 reverted to shared `ProtectedRoute module=<vertical>` + role check.
- The pattern "rely on Flyway for schema in production, Hibernate `ddl-auto=update` in local dev" leaves local environments behind on data migrations (UPDATE statements). V9070 has a real-effect UPDATE that NEVER runs locally → tenants lack the `FITNESS` enabled_modules entry → operator hits 500/403 even on supposedly-configured tenants. S22's DB smoke step manually applies the UPDATE-equivalent locally as a workaround.
