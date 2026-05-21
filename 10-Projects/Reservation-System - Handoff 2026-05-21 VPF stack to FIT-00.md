---
title: Reservation-System - Handoff 2026-05-21 VPF stack to FIT-00
type: handoff
project: Reservation-System
date: 2026-05-21
status: ready-for-personal-laptop
tags:
  - project/reservation-system
  - handoff
  - vpf
  - fitness
---

# Reservation-System - Handoff 2026-05-21 VPF stack to FIT-00

This is the pickup note for moving work from the Stefanini laptop to the personal laptop. It is intentionally verbose so the local disk can be wiped without losing context.

## Repositories

### Main repo

- GitHub: `git@github.com:MihaiCatalinVoicu/Reservation-System.git`
- Local path on Stefanini laptop: `C:\Personal\Reservation-Systems\Reservation-System`
- Last local branch handled here: `feature/vpf-07-multi-scope-hardening-playwright`
- Last pushed commit: `8fd70d4b test(verticals): harden multi-scope visibility`
- Current local status at handoff time: clean and tracking `origin/feature/vpf-07-multi-scope-hardening-playwright`.

### Vault repo

- GitHub: `git@github.com:MihaiCatalinVoicu/Vault.git`
- Local path on Stefanini laptop: `C:\Personal\Vault`
- This handoff file is committed and pushed in the Vault repo.

## How to restore on personal laptop

```powershell
git clone git@github.com:MihaiCatalinVoicu/Vault.git C:\Personal\Vault
git clone git@github.com:MihaiCatalinVoicu/Reservation-System.git C:\Personal\Reservation-Systems\Reservation-System
cd C:\Personal\Reservation-Systems\Reservation-System
git fetch --all --prune
```

If the PR stack is still open and you need to continue immediately:

```powershell
git checkout feature/vpf-07-multi-scope-hardening-playwright
```

If the stack has been fully merged:

```powershell
git checkout main
git pull --ff-only
```

## Current logical PR stack

Important: GitHub numbers for VPF-06 and VPF-07 are reversed because VPF-07 PR was created first. Logical merge order is what matters.

| Logical order | PR | Title | Branch/base | Status at handoff |
|---:|---|---|---|---|
| 1 | #78 | `docs(intake): audit reservation command center current state` | base stack | open/mergeable from earlier tracker |
| 2 | #79 | `test(intake): characterize cross-vertical intake runtime behavior` | #78 | open/mergeable from earlier tracker |
| 3 | #80 | `feat(hotel): support multi-provider external channels` | #79 | open/mergeable from earlier tracker |
| 4 | #81 | `feat(hotel): map external channel events to room reservations` | #80 | open/mergeable from earlier tracker |
| 5 | #82 | `feat(hotel): add external bookings command center view` | #81 | open/mergeable from earlier tracker |
| 6 | #83 | `feat(spa): map external appointments to spa reservations` | #82 | ready/mergeable from earlier tracker |
| 7 | #84 | `feat(spa): add external appointments command center view` | #83 | ready/mergeable from earlier tracker |
| 8 | #85 | `feat(intake): add cross-vertical intake summary` | #84 | ready/mergeable from earlier tracker |
| 9 | #86 | `chore(runtime): fix local backend smoke profile` | #85 | ready/mergeable from earlier tracker |
| 10 | #87 | `docs(verticals): define vertical packaging foundation ADR` | #86 | ready/mergeable from earlier tracker |
| 11 | #88 | `feat(verticals): add backend packaging foundation` | #87 | ready/mergeable from earlier tracker |
| 12 | #89 | `feat(verticals): add entitlement resolution engine` | #88 | ready/mergeable from earlier tracker |
| 13 | #90 | `feat(verticals): add frontend visibility shell` | #89 | ready/mergeable from earlier tracker |
| 14 | #91 | `feat(verticals): map legacy modules to visibility shell` | #90 | ready/mergeable from earlier tracker |
| 15 | #92 | `feat(verticals): add package templates and tenant bootstrap` | #91 | ready/mergeable from earlier tracker |
| 16 | #94 | `feat(superadmin): manage vertical packages` | base `feature/vpf-05-package-templates-tenant-bootstrap-demo-seeds` | open, ready, mergeable |
| 17 | #93 | `test(verticals): harden multi-scope visibility` | base `feature/vpf-06-superadmin-package-control` | open, ready, mergeable |

Direct links:

- VPF-06 PR #94: `https://github.com/MihaiCatalinVoicu/Reservation-System/pull/94`
- VPF-07 PR #93: `https://github.com/MihaiCatalinVoicu/Reservation-System/pull/93`

Merge sequence near the top of the stack:

```text
#92 -> #94 (VPF-06) -> #93 (VPF-07) -> FIT-00
```

Full stack sequence remains:

```text
#78 -> #79 -> #80 -> #81 -> #82 -> #83 -> #84 -> #85 -> #86 -> #87 -> #88 -> #89 -> #90 -> #91 -> #92 -> #94 -> #93 -> FIT-00
```

## What VPF-06 delivered

Branch:

```text
feature/vpf-06-superadmin-package-control
```

Commit:

```text
cb26879b feat(superadmin): manage vertical packages
```

PR:

```text
#94 feat(superadmin): manage vertical packages
```

Scope delivered:

- Superadmin vertical package-control API/UI.
- Available packages.
- Tenant package entitlements.
- Grant package.
- Expire/suspend/reactivate package entitlements.
- Visibility preview.
- Demo template dry-run/apply through superadmin.

Explicit non-scope:

- No billing.
- No Stripe.
- No self-service purchase.
- No Fitness behavior/pages.
- No removal of `VPF_NOT_CONFIGURED` fallback.

## What VPF-07 delivered

Branch:

```text
feature/vpf-07-multi-scope-hardening-playwright
```

Commit:

```text
8fd70d4b test(verticals): harden multi-scope visibility
```

PR:

```text
#93 test(verticals): harden multi-scope visibility
```

Files changed in VPF-07:

- `docs/01_CURRENT_STATE.md`
- `docs/architecture/ADR_VERTICAL_PACKAGING_FOUNDATION.md`
- `docs/product/MODULE_VISIBILITY_RULES.md`
- `docs/sprints/VPF_07_MULTI_SCOPE_HARDENING_PLAYWRIGHT.md`
- `e2e/staff-desktop/vertical-packaging-multi-scope.spec.ts`
- `frontend/src/admin/visibility/navigationBuilder.ts`
- `frontend/src/admin/visibility/navigationBuilder.test.js`
- `reservationsystem/src/main/java/com/coworking/reservationsystem/service/impl/NavigationProjectionServiceImpl.java`
- `reservationsystem/src/test/java/com/coworking/reservationsystem/controller/SuperadminVerticalPackagingControllerTest.java`
- `reservationsystem/src/test/java/com/coworking/reservationsystem/service/impl/VerticalPackagingMultiScopeVisibilityTest.java`
- `superadmin-frontend/src/pages/TenantVerticalPackagingPanel.test.tsx`

Main hardening decisions:

- Configured VPF tenants no longer fall back to legacy nav when their visible modules are all future/non-renderable. They stay in visibility mode with an empty filtered nav so hidden modules cannot leak.
- `VPF_NOT_CONFIGURED` tenants still keep legacy navigation and legacy access.
- Visibility API failure still keeps legacy navigation.
- Backend navigation projection routes were aligned to current `:3000` frontend routes.
- Fitness remains metadata-only. Backend can expose Fitness metadata; frontend does not render clickable Fitness routes until FIT-00.

## VPF-07 validation evidence

Backend:

```powershell
mvn -pl reservationsystem "-Dtest=VerticalPackagingMultiScopeVisibilityTest,SuperadminVerticalPackagingServiceTest,SuperadminVerticalPackagingControllerTest" test
```

Result: 16 tests passed.

```powershell
mvn -pl reservationsystem "-Dtest=VerticalPackageTemplateServiceTest,VerticalTenantBootstrapServiceTest,VerticalTemplateVisibilityIntegrationTest,VerticalPackagingDemoTemplateControllerTest,AvailableScopeResolverTest,VisibleModuleResolverTest,NavigationProjectionServiceTest,VisibilityContextServiceTest,CurrentUserVisibilityControllerTest,BusinessModuleCatalogServiceImplTest,BusinessPackageCatalogServiceImplTest,BusinessVenueServiceImplTest,TenantPackageEntitlementServiceImplTest,UserScopeAssignmentServiceImplTest" test
```

Result: 54 tests passed.

```powershell
mvn -pl reservationsystem "-Dtest=IntakeOutcomeNormalizerTest,CrossVerticalIntakeSummaryServiceImplTest,AdminCrossVerticalIntakeControllerTest,SpaExternalAppointmentCommandCenterServiceImplTest,AdminSpaExternalAppointmentControllerTest,SpaExternalAppointmentMapperTest,SpaExternalAppointmentPromotionServiceImplTest,SpaReservationBookingServiceImplTest,HotelExternalBookingCommandCenterServiceImplTest,AdminHotelExternalBookingControllerTest,HotelExternalReservationPromotionServiceImplTest,ExternalCalendarServiceImplTest,HotelExternalChannelServiceImplTest,ExternalReservationImportServiceImplTest,ExternalReservationCommandCenterServiceImplTest,ReservationConflictServiceTest,ReservationDurationPolicyServiceTest" test
```

Result: 118 tests passed.

Frontend:

```powershell
cd frontend
npx react-scripts test --watchAll=false Visibility
npm run build
```

Results:

- Visibility tests: 28 passed.
- Frontend build: passed.
- Build warnings are existing/stable: `env.js` script type, stale Browserslist, bcryptjs crypto externalized, dynamic/static import warning, large chunks.

Superadmin frontend:

```powershell
cd superadmin-frontend
npm test -- --watchAll=false TenantVerticalPackagingPanel
npm run build
```

Results:

- Package panel tests: 2 passed.
- Superadmin build: passed with existing ESLint warnings in unrelated files.

Playwright:

```powershell
$env:STAFF_BASE_URL='http://127.0.0.1:3002'
npx playwright test --config=e2e/playwright.config.ts --project=staff-desktop-local e2e/staff-desktop/vertical-packaging-multi-scope.spec.ts
```

Result: 3 passed.

Note:

- The test was run against a clean Vite dev server on `127.0.0.1:3002`.
- Port `3000` had an existing `vite preview` process serving a blank protected-route state, so `STAFF_BASE_URL` was deliberately pointed at the clean dev server.

Runtime smoke:

- Docker compose services were up:
  - `postgres` healthy on `55432 -> 5432`
  - `reservation_backend` up on `8080`
- `/actuator/health` returned `UP`.
- Authenticated endpoints verified:
  - `/api/v1/admin/intake/summary`
  - `/api/v1/me/visibility-context`
  - `/api/v1/me/navigation`
  - `/api/v1/super/vertical-packaging/packages`
  - `/api/v1/super/tenants/1/vertical-packaging/visibility-preview`
  - non-destructive dry-run apply for `demo_hotel_basic`
- Dry-run returned `dryRun=true` and `appliedChanges=0`.

Diff hygiene:

```powershell
git diff --check
git diff --cached --check
```

Both passed.

## Scenario verdict table

| Scenario | VPF-07 verdict |
|---|---|
| `VPF_NOT_CONFIGURED` | Pass: fallback scope/warning, no visible modules, frontend legacy fallback preserved |
| Restaurant-only | Pass: one Restaurant scope, Restaurant modules only |
| Hotel-only | Pass: one Hotel scope, Hotel modules only |
| Spa-only | Pass: one Spa scope, Spa modules only |
| Hotel + Spa | Pass: business-area scopes; Hotel scope shows Hotel modules, Spa scope shows Spa modules |
| Resort combo | Pass: Restaurant/Hotel/Spa scopes; no Fitness leakage |
| Restaurant group | Pass: location scopes; Restaurant modules per venue; no Hotel/Spa/Fitness leakage |
| Fitness metadata-only | Pass: Fitness metadata visible in backend, no clickable frontend routes |

## Deferred on purpose

Full authenticated browser matrix over each VPF demo tenant is deferred.

Reason:

- Backend templates are deterministic.
- Frontend builder tests cover configured/unconfigured navigation behavior.
- Playwright has API-backed smoke coverage.
- The repo still lacks dedicated per-template Playwright auth states or disposable tenant/user UI sessions for Restaurant-only, Hotel-only, Spa-only, Hotel+Spa, Resort, Restaurant group, and Fitness metadata tenants.

This does not block FIT-00 because module isolation is proven at service/builder level and Fitness remains non-clickable in the frontend.

## Current local machine cleanup status

Main repo after VPF-07 push:

```text
feature/vpf-07-multi-scope-hardening-playwright...origin/feature/vpf-07-multi-scope-hardening-playwright
HEAD 8fd70d4b
working tree clean
```

Vault repo after this note is committed/pushed:

```text
main...origin/main
working tree clean
```

No uncommitted code should remain on this laptop.

## Next sprint

Next sprint is:

```text
FIT-00 - Fitness Pack Shell
```

Branch rule:

- If all PRs are merged: branch from `main`.
- If stack is still open: branch from `feature/vpf-07-multi-scope-hardening-playwright`.

Expected FIT-00 scope:

- Add Fitness route/page shell only.
- Enable Fitness visibility for `FITNESS_OPS` tenants.
- Keep Fitness business DB, memberships, classes, check-ins, retention, billing, and Stripe out of scope.
- Prove Restaurant/Hotel/Spa tenants do not see Fitness.

Do not start:

- Billing/Stripe.
- Fitness business behavior.
- Memberships/classes/check-ins.
- Provider APIs.
- Generic rewrite.

## Exact pickup prompt for personal laptop

```text
Continue Reservation-System after VPF-07.

Read first:
- C:\Personal\Vault\10-Projects\Reservation-System - Handoff 2026-05-21 VPF stack to FIT-00.md
- C:\Personal\Reservation-Systems\Reservation-System\CLAUDE.md
- docs/01_CURRENT_STATE.md
- docs/architecture/ADR_VERTICAL_PACKAGING_FOUNDATION.md
- docs/product/MODULE_VISIBILITY_RULES.md
- docs/sprints/VPF_07_MULTI_SCOPE_HARDENING_PLAYWRIGHT.md

Current stack:
- VPF-06 is PR #94.
- VPF-07 is PR #93.
- Numbers are reversed; logical order is #94 then #93.

If the stack is open, branch FIT-00 from:
feature/vpf-07-multi-scope-hardening-playwright

If merged, branch FIT-00 from latest main.

Next sprint:
FIT-00 - Fitness Pack Shell

Hard rules:
- No Fitness business behavior yet.
- No billing/Stripe.
- No self-service package purchase.
- No route/nav redesign.
- Do not remove VPF_NOT_CONFIGURED fallback.
- Keep Restaurant/Hotel/Spa behavior compatible.
```

