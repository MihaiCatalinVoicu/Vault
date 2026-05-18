---
title: Alert Intelligence deployed
type: decision
date: 2026-05-18
status: executed
impact: operational
tags:
  - decision/trading
  - product/strategy-os
  - product/alerts
  - product/audit
---

# 2026-05-18 — Alert Intelligence deployed

## Decision

Strategy OS DB devine sursa de adevăr pentru istoricul alertelor. Telegram rămâne canal de notificare/acțiune rapidă, dar alerta trebuie să ajungă în DB ca eveniment structurat, cu outcome legat ulterior.

## Implementat

- `Automation_MVP` / Strategy OS:
  - `alert_observations`, `alert_daily_reviews`, `alert_patterns`, `alert_pattern_outcomes`, `agent_consensus_*`.
  - `/api/internal/alert-intake` cu HMAC.
  - `Alert Intelligence` tab în panel.
  - daily deterministic review timer.
  - weekly consensus timer, momentan `mock`.
  - backfill din `telegram_alert_events`, `raw_lifecycle_events`, `strategy_audit_events`, `ops_attention_incidents`, crypto CSV.
- `ibkr_bot`:
  - mirror către Strategy OS pentru buy signal, Execute/Skip/Expire, gateway down, risk blocks, exit events.
  - păstrează Telegram exec bot separat.
- `crypto-bot`:
  - mirror către Strategy OS pentru signal/decision/exit events.
  - `decision_log.csv` și `trades.csv` importate în Strategy OS.
  - Telegram send devine best-effort ca `BadRequest: Chat not found` să nu rupă jobul.

## Server

- Server: `207.154.248.232`
- Backup DB înainte de migration:
  - `/root/backups/codex-alert-intelligence-20260518T093914Z/orchestrator.db`
- Timers active:
  - `strategy-os-alert-learning-daily.timer`
  - `strategy-os-alert-consensus-weekly.timer`
- Health verified:
  - `runtime_alert_ingest_configured=true`
  - `learning_enabled=true`
  - `consensus_enabled=true`

## Git commits

- `Automation_MVP`: `d902de5` — `Add alert intelligence pipeline`
- `ibkr_bot`: `d543f06` — `Mirror live alerts to Strategy OS`
- `Crypto_bot`: `c3b759b` — `Mirror crypto alerts to Strategy OS`

## Observații

- `crypto-bot` Telegram token este valid pentru `CryptoBotSimBot`, dar `getChat(1194534321)` returnează `Bad Request: chat not found`. Cel mai probabil userul nu a inițiat conversația cu acel bot specific sau botul a fost blocat. Fix operațional: deschide `@CryptoBotSimBot`, trimite `/start`, apoi verifică `getChat`.
- `ai_usage_events` este gol pentru AI Console; poziția health folosește provider real, dar nu loghează încă usage/cost. Trebuie adăugat usage logging și cost cap înainte de weekly consensus real.
- `/health` arată `billing_configured=false`; înainte de promovare publică trebuie Stripe live + legal/commercial hardening.

## Next

1. Fix Telegram crypto chat binding.
2. Add AI usage/cost logging pentru position health + consensus.
3. Decide provider/cost policy: Anthropic real doar cu buget și rate limits; mock/deterministic pentru daily.
4. Stripe live readiness: products/prices, checkout, portal, webhook, legal pages, owner email non-work.

## Cross-refs

- [[Automation_MVP]]
- [[ibkr_bot]]
- [[crypto-bot]]
- [[Telegram bots map]]
- [[Pre-mortem Trading Bots]]
