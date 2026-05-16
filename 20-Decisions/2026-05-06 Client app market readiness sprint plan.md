---
title: Client app market readiness sprint plan
type: decision
date: 2026-05-06
status: in-execution
impact: product-launch
tags:
  - decision/product
  - decision/launch
  - product/reservation-system
---

# 2026-05-06 — Client app market readiness sprint plan

## Context

[[Reservation-System]] mobile = Phase 3 in monorepo split:
- `mobile/mobile_app/` (legacy `ro.venuehub.mobile`) — currently shipping
- `mobile/apps/client_app/` (target `ro.venuehub.tourist`) — Phase 3 portat 3a-3e
- `mobile/apps/staff_app/` — post-alpha, NOT in this plan

Întrebarea owner-ului: ce mai lipsește până la "Pune client_app pe market"?

## Decizia

Plan 10-sprint, cu **corporate-laptop boundary** explicit:

| # | Sprint | Mediu | Status (2026-05-16) |
|---|---|---|---|
| 1 | BE sanity gate (smoke `/me/favorites` + `/mobile/devices`) | Stefanini OK | ✅ |
| 2 | Firebase project + `google-services.json` | Stefanini OK | ✅ |
| 3 | Release keystore + `key.properties` + SHA256 | **PERSONAL** (redo) | ✅ done on personal |
| 4 | Launcher icons + splash + adaptive icon | Stefanini OK | ✅ |
| 5 | Install Flutter+Android SDK + build AAB end-to-end | **PERSONAL** | 🔴 pending |
| 6 | Privacy + Terms + Cookies URL-uri scurte | Stefanini OK | ✅ code (deploy pending) |
| 7 | `assetlinks.json` extins cu `ro.venuehub.tourist` + SHA256 | mixt | ✅ (SHA256 updated post-redo) |
| 8A | Play Console: cont SRL + KYC | **PERSONAL** | 🔴 pending ($25 + 1-7 zile) |
| 8B | Listing copy + Data Safety doc | Stefanini OK | ✅ — `docs/PLAY_CONSOLE_LISTING_PACK.md` |
| 9 | Smoke device real cap-coadă + capture screenshots | **PERSONAL** | 🔴 pending |
| 10 | Upload AAB Internal Testing track + ledger entry | **PERSONAL** | 🔴 pending |

## Corporate boundary (CRITICAL)

Laptop Stefanini are: Zscaler (TLS inspection), Sophos Endpoint (file mini-filter + osquery), **Rapid7 InsightIDR** (process command-line capture), Microsoft Intune, Adaptiva.

Vectorul real: Rapid7 + Sophos osquery capturează `keytool.exe` command-line args cu `-storepass <PAROLA>`. Keystore generat pe Stefanini = potențial compromis în logs retroactiv.

Boundary:
- ✅ Pe Stefanini OK: cod public oricum (GitHub open source), assets PNG, doc text, Firebase config (API keys oricum publice prin APK)
- 🔴 NUMAI pe personal: keystore, parolă, KYC cont SRL Play Console, build AAB final, smoke device

## On-disk reference

- `docs/CLIENT_APP_MARKET_READINESS_SPRINT.md` — detail per sprint (acceptance, steps, output)
- `docs/PLAY_CONSOLE_LISTING_PACK.md` — listing copy RO+EN + Data Safety form pre-completat + reviewer credentials
- `docs/Roadmap Alpha Android Duală - client_app + staff_app.md` — roadmap mai larg (incl. staff_app post-alpha)

## Standing rules

- **Production keystore** = numai pe laptop personal, vault parola în 1Password/Bitwarden
- **Cont Play Console** = NOVENTRA SYSTEMS S.R.L. (CUI 54594735), login DOAR de pe personal
- **Reviewer credentials Play Console** = user `alpha-reviewer@venuehubhq.com` trebuie creat pe BE prod cu tenantId=1 înainte de submit
- **SHA256 keystore actual** (sandbox de pe Stefanini, în repo curent): `66:0B:B0:36:55:A8:64:64:A3:2B:87:C9:F0:94:71:55:2A:D9:9D:8D:86:33:FE:0A:E4:9B:19:FB:67:5A:69:5E` — must be replaced before Open Track
- **SHA256 keystore production** (pe personal laptop): `79:71:92:6F:7B:06:03:47:62:B8:E9:56:67:4E:B3:B3:AC:46:AB:42:92:63:E3:76:4F:6B:36:89:AE:1A:B5:3E` — observed in assetlinks.json on main

## Next physical action (utilizator)

Pe laptop personal, după ce IBKR e back online:
1. `git clone git@github.com:MihaiCatalinVoicu/Reservation-System.git`
2. Sprint 5: install Flutter SDK + Android SDK + `flutter pub get` + `melos build-client-aab`
3. Sprint 9: smoke device + capture screenshots
4. Sprint 8A: KYC Play Console în paralel
5. Sprint 10: upload AAB

## Cross-refs

- [[Reservation-System]] — proiect părinte
- [[Stefanini laptop monitoring]] — context corporate
- [[Claude session boundary]] — cum continuă Claude între Stefanini și personal
