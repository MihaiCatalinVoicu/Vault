---
title: Telegram bots map
type: reference
tags:
  - reference/integrations
  - reference/operations
updated: 2026-05-16
---

# Telegram bots map

Tracking care bot Telegram aparține cărui serviciu, post swap 2026-05-08. Regula standing: **1 bot per serviciu** (no shared bots, evită noise / confusion).

## Active bots

| Bot ID | Serviciu | Rol |
|---|---|---|
| **8639** | [[ibkr_bot]] | Status, alerts, 2FA push reminders, restart_gateway commands |
| **8736** | [[crypto-bot]] | Status (zero semnale azi), arhivă alerts (DORMANT) |

## Standing rule

> [!warning] NU shared bots între servicii.

Dacă apare serviciu nou care vrea Telegram integration → bot nou cu ID propriu. Evită debugging confuz "de ce alert-ul X a apărut pe bot Y?".

## Cross-refs

- [[ibkr_bot]]
- [[crypto-bot]]
