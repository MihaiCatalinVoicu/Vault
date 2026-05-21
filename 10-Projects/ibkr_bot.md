---
title: ibkr_bot
type: project
status: live
risk: high
stack:
  - Python
  - IB Gateway (server-side, headless)
  - IBKR API
tags:
  - project/live
  - product/trading
  - risk/high
  - venue/ibkr
created: 2026
updated: 2026-05-19
---

# ibkr_bot

🔴 **LIVE (real money)** — equity/futures via IBKR Gateway, server-side, automated, watchdog-managed.

## On-disk

- **Local repo:** `C:\Personal\Trading Bots\ibkr_bot\`
- **Server deploy:** `207.154.248.232:/opt/ibkr_bot/`
  - ⚠️ ATENȚIE: NU confunda cu `89.167.121.99` (Hetzner, e pentru [[Reservation-System]])
- **State file (server-side):** `/opt/ibkr_bot/state.json` (positions, daily_counters, closed_orders)

## Strategy

**EC_V1** — Event Cluster v1, semnale generate de [[stocks-bot]] (cuplaj cross-project).

## Tier & sizing

- $385 / slot
- NetLiq ~$966 (15 mai 2026)
- Available ~$726
- Bot tick = **60s**
- 4 execuții live (2 close) per snapshot 8 mai 2026
- CBZ reconciled

## Telegram bot

bot_id = **8639** (post swap 2026-05-08). Vezi [[Telegram bots map]].

## Strategy OS alert mirror

2026-05-18: `ibkr_bot` păstrează botul Telegram de execuție, dar trimite mirror către Strategy OS Alert Intelligence pentru:
- buy signal;
- Execute/Skip/Expire;
- gateway down;
- time/early/breakeven risk blocks;
- exit events.

Vezi [[2026-05-18 Alert Intelligence deployed]].

## Pre-mortem mitigations (2026-05-17 → 2026-05-18)

3 din 4 Tigers identificate în [[2026-05-16 Pre-mortem Trading Bots]] închise:

- **T3 state.json corruption** — fsync(tmp+dir) + hourly versioned snapshots în `.state.json.backups/` (24-file ring buffer). Vezi [[2026-05-17 Pre-mortem T1 T2 T3 mitigations deployed]].
- **T2 stocks-bot coupling** — `signal_validator.py` cu sanity gates pe ticker/planned_cost/dates + soft-warn freshness check pe paper_orders.csv stale.
- **T1 Gateway 2FA SPOF** — `ops/ibkr_gateway_watchdog.py` standalone (systemd timer 5min), NTFY push channel independent de Telegram, SMTP email optional. State machine cu cooldown 60min.

Plus fast-follow T5:

- **T5 ANAF tax extractor** — `tools/anaf_tax_export.py` parser Flex XML + FIFO + BNR converter → RON CSVs pentru Form D200. Vezi [[2026-05-18 ANAF tax extractor shipped]].

## Deploy workflow

- Release: **SFTP + sudo install** (NU git push to prod server)
- Service systemd: `ibkr_bot.service` + Gateway runs separately via IBC
- Watchdog auto-restart Gateway dacă >5min down (bot-internal, Telegram-only)
- **External watchdog** (T1): `ibkr-watchdog.timer` → fires every 5min, independent de bot, NTFY push

## 🔴 Diagnostic-first protocol

> [!warning] CRITIC: live money. NU "fix" orb fără log evidence.

Înainte de orice fix:

1. **Tail logs**: `journalctl -u ibkr_bot.service -n 200 --since "1 hour ago"`
2. **State snapshot**: cat `/opt/ibkr_bot/state.json`
3. **IBKR portfolio cross-check**: confirm state local = real positions în Gateway
4. **Timing**: tick = 60s, eveniment reflected după min 1 cycle
5. **NICIODATĂ deploy fix** fără log evidence + cause hypothesis explicit

> [!example] Friction event 2026-05-08
> 698+ crash-loop restarts între 05:33-05:55 din cauza Gateway IBC restart fără decoupling. Fix-ul real (`30-decouple-gateway-restarts.conf`) a venit DUPĂ analiza logs, nu înainte. Lecție: log evidence first, apply fix second.

## 2FA reconnect protocol (CRITICAL)

Vezi [[2026-05-15 IBKR 2FA reconnect protocol]].

**TL;DR:** când watchdog repornește Gateway, primești push pe IBKR Mobile. **TAP pe notification (Allow/Deny)**, NU deschide app + login. Login direct creează sesiune duplicate → kick ("Another session already exists") → restart loop.

## Long-term opțional

Client Portal web → Settings → User Settings → "Allow multiple concurrent sessions" → ON. Permite IBKR Mobile manual + Gateway server simultan fără kick.

## Cross-refs

- Strategy upstream: [[stocks-bot]] (generates EC_V1 signals)
- Multi-venue digest: [[research-orchestrator]] (A1 digest)
- 2FA protocol: [[2026-05-15 IBKR 2FA reconnect protocol]]
- Telegram: [[Telegram bots map]] (bot_id=8639)
- Related (different tech): [[crypto-bot]]
