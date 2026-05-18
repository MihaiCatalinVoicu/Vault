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
- `Automation_MVP`: `c0533dc` — `Log position health AI usage`
- `ibkr_bot`: `d543f06` — `Mirror live alerts to Strategy OS`
- `Crypto_bot`: `c3b759b` — `Mirror crypto alerts to Strategy OS`

## Observații

- `crypto-bot` Telegram a fost reparat operațional:
  - userul a pornit chatul cu `@CryptoBotSimBot`;
  - `getChat(1194534321)` trece;
  - `sendMessage` trece;
  - `telegram.enabled=true` și `reporting.daily_alerts_enabled=true` în `/opt/crypto-bot/current/config.yaml`;
  - `crypto-bot.service` restartat și activ.
- `ai_usage_events` era gol, iar Position Health folosea provider real fără usage logging. Fix implementat/deployat:
  - `ai_provider.py` returnează usage pentru Position Health;
  - `position_health.py` scrie review usage în `ai_usage_events`;
  - test: `tests/test_position_health_telegram_ops.py::test_position_health_review_logs_ai_usage`.
- Anthropic:
  - cheia de pe server este standard API key, nu Admin key;
  - Anthropic Admin Usage Report API nu poate fi folosit cu cheia curentă;
  - Strategy OS AI model a fost schimbat temporar la `claude-haiku-4-5-20251001` ca măsură de cost control;
  - weekly alert consensus rămâne `mock`.
- Stripe:
  - serverul are `STRIPE_SECRET_KEY` test și `STRIPE_WEBHOOK_SECRET`;
  - lipsea `STRIPE_PRICE_ID`;
  - setat `STRIPE_PRICE_ID=price_1TMPSlQ5Ho1FJ6k8lhDtw8AV` (`Founding Pilot`, 149 EUR/month, test mode);
  - `/health` arată acum `billing_configured=true`;
  - rămâne necesar live mode înainte de promovare publică.

## Next

1. Stripe live readiness: live key, live prices, portal, webhook, legal pages, cancellation/refund/risk disclosure.
2. Anthropic cost attribution: export din Dashboard sau Admin key pentru usage report; apoi buget zilnic și rate limits.
3. Weekly consensus real: activează doar după cost caps + usage logging complet; daily rămâne deterministic.
4. Crypto Telegram: monitorizează următorul scan real; dacă nu apare mesaj, verifică `reporting.daily_alerts_enabled` și logs.

## Cross-refs

- [[Automation_MVP]]
- [[ibkr_bot]]
- [[crypto-bot]]
- [[Telegram bots map]]
- [[Pre-mortem Trading Bots]]
