---
title: Daily Signal Digest truthfulness fix
type: decision
project: stocks-bot
date: 2026-05-18
status: deployed
tags:
  - trading/stocks
  - ops/telegram
  - bugfix
  - ai-cost
---

# Daily Signal Digest truthfulness fix

## Problem

Daily Signal Digest trimis pe Telegram a raportat poziții și randamente inexistente:
- `ONON`, `TKO`, `ZTS` ca `already_ordered`;
- `Open positions: 4`;
- `Equity: $160,024`;
- `Return: +60.02%`;
- AI review a descris aceste cifre ca portofoliu real.

Acest lucru era fals pentru live. Utilizatorul este în pierdere și nu are poziții închise pe plus.

## Root Cause

`stocks-bot/scripts/send_daily_signal_digest.py` citea:
- `data/paper_shadow_event_cluster_v1/allocator_debug_YYYY-MM-DD.csv`;
- `data/runtime_shadow_event_cluster_v1/last_run_summary.json`;
- `data/paper_shadow_event_cluster_v1/paper_trades.csv`.

Acestea sunt date paper/shadow, dar template-ul le prezenta ca poziții și PnL live. Promptul Claude primea aceeași stare paper/shadow și genera review ca și cum ar fi fost portofoliu real.

În plus, scriptul folosea direct Anthropic cu model hardcodat `claude-sonnet-4-20250514`, separat de Strategy OS budget guardrails.

## Fix Deployed

Repo: `stocks-bot`

Changes:
- paper/shadow portfolio state este marcat `source=paper_shadow_runtime`, `verified=false`;
- digestul omite metrics ne-verificate:
  - nu mai afișează equity/return/open positions din paper ca live;
  - afișează explicit: `Portfolio metrics omitted: no verified live source configured for this digest`;
- `already_ordered` nu mai este prezentat ca poziție live;
- shadow watched rows sunt ascunse default;
- dacă sunt afișate manual, headerul este `SHADOW WATCHED (paper already ordered; not live positions)`;
- AI review este dezactivat default pentru daily digest;
- dacă e activat explicit, folosește Haiku 4.5 și token cap mai mic;
- dry-run nu mai suprascrie arhiva digestului real; scrie în `_digest_dry_run.json`.

Server backup:
- `/root/backups/stocks-bot-digest-truth-20260518T152504Z`

Server dry-run pentru 2026-05-18 după fix:

```text
Strategy OS — Daily Signal Digest
Date: 2026-05-18

No new eligible signals today.

━━ PORTFOLIO ━━

Portfolio metrics omitted: no verified live source configured for this digest.
Source available: paper_shadow_runtime (not used as live PnL/positions).
```

## Tests

```text
SEC_USER_AGENT='InsiderEdge mihai.catalin_voicu@yahoo.com' pytest \
  tests/test_daily_signal_digest.py \
  tests/test_effective_policy.py \
  tests/test_paper_eval_accounting.py

19 passed
```

## Remaining

- Implement verified live portfolio source for digest if we want live PnL in Telegram.
- Source should be IBKR live state / `ibkr_bot` state, not paper shadow runtime.
- Until then, digest must not print live-looking portfolio metrics.
