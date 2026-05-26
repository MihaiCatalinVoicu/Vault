# Project Constitution — VenueHub / Reservation-System

## Non-negociabile

1. `:3000` este creierul operațional.
2. `:3001` client/public este momentan pending/dead și nu trebuie folosit ca dependență pentru Private Circle, VIP sau operațiuni critice.
3. `:3100` este superadmin/platform control.
4. Tenant onboarding este inițial manual/verbal. Self-onboarding vine ulterior ca acquisition funnel.
5. Pachetele trebuie separate:
   - basic
   - premium/full
   - add-on-uri precum Accounting Ops, Business Pulse, VIP/Private Circle
6. VIP/Private Circle nu se afișează tenant-urilor basic sau localurilor mici.
7. Restaurant, Hotel, Spa, Fitness au module verticale separate.
8. Shared modules se țin comune, nu se dublează aiurea în fiecare verticală.
9. FastOps este o suprafață separată de execuție rapidă, nu înlocuiește adminul complet.
10. Meniurile trebuie să fie determinate de visibility/package/scope, nu de noroc.

## Principiu de navigație

Există 4 surse care trebuie ținute în sync:

- Router: ce pagini există efectiv.
- Header/nav: ce vede userul.
- ActiveTab resolver: ce se marchează activ.
- ModuleRouteRegistry/VPF: ce module apar în visibility nav.

Dacă una din cele 4 spune altceva, acceptarea este NO-GO.
