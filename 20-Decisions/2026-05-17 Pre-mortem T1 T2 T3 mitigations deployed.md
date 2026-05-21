---
title: Pre-mortem T1 T2 T3 mitigations deployed
type: decision
project: ibkr_bot
date: 2026-05-17
status: deployed
impact: operational
tags:
  - decision/trading
  - product/trading
  - ops/monitoring
  - risk/high
  - venue/ibkr
---

# 2026-05-17 — Pre-mortem T1+T2+T3 mitigations deployed

Toate cele 3 Tigers identificate în [[2026-05-16 Pre-mortem Trading Bots]] au primit mitigation tehnic într-o singură zi pe `ibkr_bot`. Code în branch-uri separate, deployed manual pe server (SFTP + sudo install), verificat live.

## T3 — state.json corruption (Tiger #3)

### Implementare
- `src/state_manager.py`:
  - `fsync(tmp)` înainte de `os.replace` + `fsync(parent_dir)` după → durability sub power-loss
  - Rate-limited hourly snapshots în `.state.json.backups/state.json.<UTC>`, ring buffer 24 fișiere
  - Existing infrastructure păstrat: tmp+replace, fcntl flock, corrupt-detect

### Verificare live
- Backup `/opt/ibkr_bot/backups/state_manager.py.20260517T105321Z`
- `.state.json.backups/` creat la primul save tick
- Service `active`, zero erori în journal

### Git
- Branch `feat/state-json-atomic-writes`, commit `e62bba9`
- Merged via GitHub UI (user manual, GitHub Actions blocked on free tier billing)

## T2 — stocks-bot → ibkr_bot LIVE coupling (Tiger #2)

### Implementare
- `src/signal_validator.py` (modul nou):
  - `validate_signal()` — refuză signal cu: ticker invalid, side non-whitelist, planned_cost out of [$5k, $50k], OMB out of [$0, $500M], hold_days out of [1, 60], entry_date/event_date corupt
  - `check_csv_freshness()` — soft-warn (nu halt) când paper_orders.csv >48h stale
- Plug point în `_discover_new_signals` (între dedup și `_handle_new_signal`)
- Rejection → `state.mark_skipped(reason="t2_validator: <reason>")` — nu re-evaluează la fiecare tick
- 7 env vars `IBKR_BOT_SIG_*` pentru systemd drop-in overrides

### Verificare live
- Sanity check: 55/55 rânduri istorice din `paper_orders.csv` accept, 0 false-positive
- 30 cases unit tests (regex ticker, planned_cost bounds, dates, freshness states)

### Git
- Branch `feat/t2-signal-validator`, commit `87cb8dc`
- Merged

## T1 — IBKR Gateway 2FA SPOF (Tiger #1)

### Implementare
- `ops/ibkr_gateway_watchdog.py` (script standalone, stdlib-only, fără ib_insync):
  - 2 health probes: heartbeat mtime + last_tick_at; TCP connect la 127.0.0.1:4001
  - Notify via NTFY.sh (free push, independent de Telegram → survives DnD/Telegram outage)
  - Notify via SMTP optional (Gmail app password, environment-driven)
  - State machine în `/var/lib/ibkr-watchdog/state.json` cu cooldown 60min
- `ops/ibkr-watchdog.{service,timer}` — oneshot + 5-min cadence

### Verificare live
- Timer `active (waiting)`, fires every 5 min
- Smoke test: healthy run → state.json `last_status: healthy`
- Simulated degrade → real notification trimisă la NTFY topic
- 19/19 unit tests (heartbeat probe variants, TCP probe, state machine, notification mocking)

### NTFY topic (privat)
`ibkr-mihai-1d8e4a93c4a14236` — pe server în `/etc/ibkr-watchdog/env`

### User-side follow-ups (T1 mitigation completă)
- [ ] Install NTFY app pe telefon + subscribe la topic
- [ ] Enable IBKR "Multiple concurrent sessions" în Client Portal
- [ ] Install IBKR Mobile pe device backup + creds în vault personal

### Git
- Branch `feat/t1-external-watchdog`, commit `7b3d545`
- Pending merge (alt thread a stivuit 2 commit-uri unrelated peste — Mirror + Volume)

## Status pre-mortem post-deploy

| Item | Status |
|---|---|
| 🔴 T1 IBKR 2FA SPOF | ✅ Watchdog deployed + NTFY |
| 🔴 T2 stocks-bot coupling | ✅ Validator + freshness deployed + merged |
| 🔴 T3 state.json corruption | ✅ fsync + snapshots deployed + merged |
| 🔴 T4 Bandwidth split | ✅ Resolved by user call (reservation = priority) |
| 🟡 T5 ANAF tax | Următor în pipeline — see [[2026-05-18 ANAF tax extractor shipped]] |

3 din 4 Tigers închise în <24h. Pre-mortem cycle Klein 2007 → effective.

## Lecții

- **CSV freshness check ca soft-warn** e mai bun decât halt. Bot trebuie să managerieze pozițiile deschise chiar dacă upstream e mort.
- **External watchdog independent de bot** e singura modalitate să prinzi bot-dead scenarios. In-loop alerting ratează crash-uri.
- **NTFY > Telegram secondary** pentru bypass DnD / phone-asleep. Free, fără signup, push native.

## Cross-refs

- [[ibkr_bot]]
- [[2026-05-16 Pre-mortem Trading Bots]]
- [[2026-05-15 IBKR 2FA reconnect protocol]]
- [[2026-05-18 ANAF tax extractor shipped]]
