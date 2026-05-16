---
title: ibkr_bot
type: project
status: active-live
stack:
  - Python
  - IB Gateway (server-side, headless)
  - IBKR API
tags:
  - project/active
  - product/trading
  - product/automated
created: 2026
updated: 2026-05-16
---

# ibkr_bot

Live trading bot pe IBKR Gateway, server-side, automated. Bot rulează 24/7 cu watchdog auto-restart.

## On-disk

`C:\Personal\Trading Bots\` (umbrella) — exact subfolder TBD din inventory ulterior.

## Telegram bot

Bot Telegram pentru control + alerts: **bot_id = 8639** (post swap 2026-05-08).
Regula: **1 bot per serviciu** — vezi [[Telegram bots map]].

## Tier & sizing

- **$385 / slot** (tier curent)
- NetLiq ~$966 (15 mai 2026)
- Available ~$726
- Bot running tick=5553+ (15 mai 2026 04:44 UTC)

## Execuții live

- 4 execuții live (2 close) per snapshot 8 mai 2026
- CBZ reconciled
- A1 digest deployed

## Deploy workflow

- Code release: **SFTP + sudo install** (NOT git push to prod server)
- Server-side IB Gateway needs **2FA via IBKR Mobile push** at every reconnect
- Watchdog auto-restart Gateway if >5min down

## 2FA reconnect protocol (CRITICAL)

> [!warning] Important
> Vezi [[2026-05-15 IBKR 2FA reconnect protocol]].
> **TL;DR:** când watchdog repornește Gateway, primești push pe telefon. **TAP pe notification (Allow/Deny dialog)**, NU deschide app + login. Login direct creează sesiune duplicate → kick din IBKR ("Another session already exists") → restart loop.

## Long-term fix opțional

Client Portal web → Settings → User Settings → "Allow multiple concurrent sessions" → ON. Permite IBKR Mobile + Gateway server simultan fără kick. Cere SMS/2FA pentru schimbare.

## Cross-refs

- Status protocol: [[2026-05-15 IBKR 2FA reconnect protocol]]
- Related: [[crypto-bot]] (separate trading bot, multi-strategy)
