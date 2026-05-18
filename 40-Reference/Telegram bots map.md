---
title: Telegram bots map
type: reference
tags:
  - reference/integrations
  - reference/operations
updated: 2026-05-18
---

# Telegram bots map

Tracking care bot Telegram aparține cărui serviciu, post swap 2026-05-08. Regula standing: **1 bot per serviciu** (no shared bots, evită noise / confusion).

## Active bots

| Bot ID | Serviciu | Rol |
|---|---|---|
| **8639** | [[ibkr_bot]] | Status, alerts, 2FA push reminders, restart_gateway commands |
| **8736** | [[crypto-bot]] | Status (zero semnale azi), arhivă alerts (DORMANT) |
| **sanityCheckProBot** | [[Automation_MVP]] / Strategy OS Ops | Position health, AI review/tickets, Alert Intelligence digests |

## Standing rule

> [!warning] NU shared bots între servicii.

Dacă apare serviciu nou care vrea Telegram integration → bot nou cu ID propriu. Evită debugging confuz "de ce alert-ul X a apărut pe bot Y?".

## Known issue

2026-05-18: `crypto-bot` token este valid pentru `CryptoBotSimBot`, dar `getChat(1194534321)` returnează `Bad Request: chat not found`. Fix probabil: deschide `@CryptoBotSimBot`, trimite `/start`, apoi re-verifică.

## Cross-refs

- [[ibkr_bot]]
- [[crypto-bot]]
