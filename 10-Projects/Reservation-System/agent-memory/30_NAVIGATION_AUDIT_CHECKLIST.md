# Navigation Audit Checklist

Run before merging any nav/menu/router work.

## Files that must be compared

```text
frontend/src/router/index.tsx
frontend/src/components/Header/Header.jsx
frontend/src/components/AdminLayout.jsx
frontend/src/admin/visibility/moduleRouteRegistry.ts
frontend/src/admin/visibility/navigationBuilder.ts
frontend/src/components/Header/DomainSwitcher.jsx
docs/product/VERTICAL_PACKAGING_MATRIX.md
docs/product/MODULE_VISIBILITY_RULES.md
docs/agent-memory/10_NAVIGATION_CANONICAL_MAP.md
```

## Assertions

For every canonical menu route:

- [ ] route exists in router
- [ ] nav entry exists in either visibility registry or explicit legacy fallback
- [ ] activeTab resolver maps it
- [ ] route guard uses correct module/package
- [ ] menu label is not duplicated under wrong vertical
- [ ] route is hidden for tenants without entitlement
- [ ] direct URL is forbidden when hidden
- [ ] test/smoke covers at least one configured tenant

## Regression cases

- [ ] Restaurant-only: no Hotel/Spa/Fitness menus
- [ ] Hotel-only: no Restaurant/Spa/Fitness menus
- [ ] Spa-only: no Restaurant/Hotel/Fitness menus
- [ ] Fitness-only: Fitness menu visible, Restaurant/Hotel/Spa hidden
- [ ] Hotel+Spa combo: scope selector shows Hotel/Spa only
- [ ] Resort combo: Restaurant/Hotel/Spa visible by scope
- [ ] Fitness full: Business Pulse visible only if `BUSINESS_PULSE` is entitled
- [ ] Basic tenant: VIP/Private Circle hidden
- [ ] VPF_NOT_CONFIGURED: legacy fallback safe, but Fitness must not leak
