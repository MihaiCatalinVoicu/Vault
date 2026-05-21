---
title: Alert Intelligence Self-Improvement Loop v1
type: decision
date: 2026-05-19
status: deployed
impact: strategy-os
tags:
  - decision/trading
  - product/strategy-os
  - product/alerts
  - product/self-improvement
  - lane/stocks
---

# 2026-05-19 — Alert Intelligence Self-Improvement Loop v1

## Decision

Alert Intelligence nu mai este doar inbox de alerte. V1 devine loop de învățare:

```text
alertă -> observation -> outcome -> pattern -> shadow test -> weekly consensus -> ticket/draft -> audit -> observed impact
```

Prima țintă este **Stocks EC_V1**, pentru că afectează capital real. Daily analyzer rămâne deterministic și ieftin. Weekly consensus poate folosi agenți/LLM, dar doar pe pattern-uri candidate și sub budget guardrails. Nicio recomandare nu schimbă live config automat.

## Implementat + deployat

- `Automation_MVP` / Strategy OS:
  - `self_improvement_shadow_tests` în DB.
  - API-uri:
    - `GET /api/workspaces/{workspace_id}/self-improvement/patterns`
    - `GET /api/workspaces/{workspace_id}/self-improvement/shadow-tests`
    - `POST /api/workspaces/{workspace_id}/self-improvement/shadow-tests/{id}/run`
    - `POST /api/workspaces/{workspace_id}/self-improvement/patterns/{id}/false-positive`
  - Daily analyzer v2 detectează:
    - `taken_vs_skipped`
    - `signal_starvation`
    - `exit_policy_drag`
    - `alpha_decay_warning`
    - `alpha_decay_research_gap`
    - plus pattern-urile existente de blockers/ops/data quality.
  - Pattern-urile eligibile stocks creează `shadow_test_ticket` și `self_improvement_shadow_tests`.
  - `run shadow test` este evidence-only dacă nu există runner extern:
    - `live_policy_mutated=false`
    - `live_orders_created=0`
    - audit event `shadow_test_completed`
  - Hook opțional pentru runner extern:
    - `STRATEGY_OS_SHADOW_TEST_RUNNER_ARGV_JSON`
    - `STRATEGY_OS_SHADOW_TEST_RUNNER_CMD`
    - runner-ul primește JSON pe stdin și returnează metrics JSON pe stdout;
    - indiferent ce returnează runner-ul, Strategy OS forțează `live_policy_mutated=false` și `live_orders_created=0`.
  - `false-positive` marchează pattern-ul și scrie audit event.
  - Weekly consensus, când ajunge la `TEST`/`TEST_SMALL` pentru pattern eligibil stocks, creează/continuă shadow test, nu ticket generic.
  - Panel `Alert Intelligence` are secțiune nouă `Self-Improvement / Shadow Tests`.
  - Telegram Ops:
    - route nou `strategy_os.self_improvement`;
    - callback `RUN_SHADOW_TEST` pentru `self_improvement_shadow_test`;
    - callback `MARK_FALSE_POSITIVE` pentru `alert_pattern`;
    - acțiunile sunt idempotente prin `telegram_actions`.
  - Outcome metrics extinse pentru `taken_vs_skipped`:
    - `taken_mae_avg`
    - `taken_mfe_avg`
    - `taken_slippage_bps_avg`
    - `skipped_counterfactual_mae_avg`
    - `skipped_counterfactual_mfe_avg`
    - `policy_versions`

## Decay-aware research

Din analiza de alpha decay:

- EC_V1: daily analyzer poate detecta degradarea pe `entry_age`, `entry_drift`, outcome.
- Options overlay: `options_microstructure_overlay_v1` intră doar ca research/shadow evidence. Dacă lipsește istoric de half-life/alpha decay, apare `alpha_decay_research_gap`, nu semnal executabil.
- Regula rămâne: `state/regime`, `trigger`, `execution gate` sunt separate; overlay-ul nu execută ordine.

Clarificare de evidență inițială: analiza din `Alpha Decay în overlay-uri de microstructură pentru opțiuni.docx` **nu era implementată complet** după primul sprint. Atunci era implementat doar stratul de infrastructură:

- pattern `alpha_decay_warning` pentru EC_V1 pe entry age/drift + outcome;
- pattern `alpha_decay_research_gap` pentru options overlay;
- timestamp/outcome discipline pregătită prin `alert_observations`;
- hook de runner shadow/replay.

## Alpha Decay Dashboard v1 — implementat și deployat

Update 2026-05-19: partea de bază pentru alpha decay a fost implementată și deployată în Strategy OS.

Implementat:

- DB:
  - `alpha_decay_profiles`
  - `options_chain_snapshots`
- Backend:
  - modul nou `strategy_os.alpha_decay`;
  - calcul IC Pearson pe orizonturi;
  - calcul Rank IC pe orizonturi;
  - estimare half-life pe baza degradării absolute a IC;
  - estimare `alpha_remaining` pentru 1/3/5/10/21 bars;
  - provider abstraction v1 pentru options chain snapshots;
  - suport Tradier options chain + Greeks, research-only;
  - `/health` expune status non-secret pentru `options_data`.
- API:
  - `GET /api/workspaces/{workspace_id}/alpha-decay/summary`
  - `POST /api/workspaces/{workspace_id}/alpha-decay/rebuild`
  - `POST /api/workspaces/{workspace_id}/alpha-decay/options-chain/fetch`
- Panel:
  - tab nou `Alpha Decay`;
  - sumar provider/profiles/snapshots;
  - tabel cu Decay Profiles: feature, N, IC, Rank IC, half-life, alpha remaining, status;
  - form pentru fetch options chain când providerul este configurat;
  - suprafața este explicit research-only, fără execuție și fără mutații live.
- Teste:
  - `tests/test_alpha_decay_dashboard.py`;
  - test pentru IC/Rank IC/half-life/alpha remaining;
  - test pentru Tradier fetch + snapshot storage fără leak de token;
  - test pentru empty dashboard state.

Verificare locală:

- `pytest tests/test_alpha_decay_dashboard.py tests/test_alert_intelligence.py tests/test_position_health_telegram_ops.py tests/test_stocks_evidence_dashboard.py tests/test_stocks_policy_control_plane.py -q` -> `26 passed`.
- `node --check strategy-os/frontend/app.js`.
- `py_compile` pentru `db.py`, `alpha_decay.py`, `api.py`.
- Smoke browser local pe `http://127.0.0.1:8015/#alpha-decay` OK.

Deploy server:

- Commit/push: `a4e9d8d Add alpha decay dashboard and options provider`.
- DB backup: `/root/backups/codex-alpha-decay-20260519T150507Z/orchestrator.sqlite.bak.gz`.
- Migrare DB OK: `alpha_decay_profiles`, `options_chain_snapshots`.
- `strategy-os.service` activ.
- `strategy-os-telegram-ops-poller.service` activ.
- `/health` OK.

Important: codul pentru provider real options data este live, dar pe server providerul nu este încă configurat:

```text
options_data.provider = none
options_data.configured = false
```

Pentru a folosi fetch real de options chain trebuie setate în EnvironmentFile:

```text
STRATEGY_OS_OPTIONS_DATA_PROVIDER=tradier
STRATEGY_OS_TRADIER_ACCESS_TOKEN=...
STRATEGY_OS_TRADIER_BASE_URL=https://api.tradier.com/v1
```

Nu am pus token în repo, DB, loguri sau panel.

Update 2026-05-20: am adăugat guardrails înainte de a porni orice fetch real sau timer:

- `STRATEGY_OS_OPTIONS_ALLOWED_SYMBOLS`, default MVP: `SPY,QQQ,IWM`.
- `STRATEGY_OS_OPTIONS_MAX_FETCHES_PER_DAY`, default: `5`.
- `STRATEGY_OS_OPTIONS_MIN_REFRESH_INTERVAL_MINUTES`, default: `60`.
- `STRATEGY_OS_OPTIONS_AUTO_REFRESH_ENABLED`, default: `false`.
- Fetch-ul respinge simboluri în afara allowlist-ului.
- Fetch-ul respinge depășirea limitei zilnice.
- Fetch-ul respinge refetch pe același `symbol + expiration` înainte de intervalul minim.
- Panelul `Alpha Decay` afișează guardrails: manual/auto, fetches/day, interval minim, simboluri permise.
- Teste dedicate acoperă allowlist, daily limit, refresh interval și status API.

Provider ladder decis:

1. **Tradier** — provider MVP pentru snapshots controlate.
   - Când: acum, pentru chain + Greeks pe `SPY/QQQ/IWM`, manual fetch, research-only.
   - Motiv: codul există deja, complexitate mică, risc operațional mic.
   - Limită: nu este suficient pentru istoric serios de alpha decay sau Vanna completă.
2. **ThetaData** — nivelul următor pentru research istoric.
   - Când: după ce avem nevoie reală de istoric pe 30-60+ zile, Greeks avansate, Vanna, surface/flow mai complet.
   - Motiv: mai bun pentru cercetare robustă și backtest decay, dar nu este necesar pentru primul smoke MVP.
3. **Databento OPRA** — prea serios/scump pentru MVP snapshots.
   - Când: doar dacă vrem pipeline OPRA mai aproape de instituțional, cu volum mare și infrastructură dedicată.
   - Nu pentru droplet/manual MVP.
4. **Cboe/DataShop** — instituțional/surfaces curate.
   - Când: dacă vrem volatility surfaces oficiale/curate pentru research sau produs premium.
   - Nu pentru primul layer de validare.

Regulă operațională: nu activăm auto-refresh până nu există provider real configurat, smoke manual reușit și cost/rate limit validate.

## Guardrails

- Auto shadow tests sunt permise.
- Live policy mutation este interzisă în v1.
- Telegram/panel pot crea/porni shadow checkpoints, dar nu pot aproba buy/sell/live config prin acest flow.
- Pentru `N < 30`, pattern-ul poate crea review/shadow evidence, nu `PROMOTE`.
- Daily analyzer nu folosește LLM.

## Teste

- Local, pe laptop:
  - `python -m pytest tests/test_alert_intelligence.py tests/test_position_health_telegram_ops.py tests/test_stocks_evidence_dashboard.py tests/test_stocks_policy_control_plane.py -q` -> `23 passed`.
  - `py_compile` pentru `db.py`, `alert_intelligence.py`, `alerting.py`, `api.py`.
  - `node --check strategy-os/frontend/app.js`.
  - API smoke local cu DB temporar.

- Server:
  - DB backup consistent: `/root/backups/codex-alert-self-improvement-20260519T142057Z/orchestrator.sqlite.bak.gz`.
  - Migrare DB: tabelele `alert_observations`, `alert_daily_reviews`, `alert_patterns`, `self_improvement_shadow_tests`, `telegram_actions` verificate OK.
  - Restart `strategy-os.service` și `strategy-os-telegram-ops-poller.service`.
  - `/health` OK:
    - `billing_configured=true`
    - `ai_configured=true`
    - `telegram_ops_configured=true`
    - `runtime_alert_ingest_configured=true`
    - `alert_intelligence.learning_enabled=true`
    - `alert_intelligence.consensus_enabled=true`
    - `consensus_provider=mock`
  - Timere active:
    - `strategy-os-alert-check.timer`
    - `strategy-os-alert-learning-daily.timer`
    - `strategy-os-alert-consensus-weekly.timer`

- `tests/test_alert_intelligence.py` acoperă:
  - HMAC intake/idempotency.
  - daily repeated blocker.
  - weekly consensus sample-size veto.
  - weekly consensus -> stocks shadow test.
  - crypto CSV import.
  - `taken_vs_skipped` outcome pattern.
  - `signal_starvation`.
  - shadow test run + audit.
  - false-positive audit.
  - options overlay alpha-decay research gap.
  - Telegram `RUN_SHADOW_TEST`.
  - Telegram `MARK_FALSE_POSITIVE`.
  - runner extern de shadow/replay cu guardrail anti-live-mutation.

## Ce rămâne

1. Configurare provider real options data pe server și smoke controlat cu un simbol lichid, de exemplu `SPY`, după ce alegem providerul și cost cap-ul.
2. Timer/job pentru refresh controlat al options snapshots, cu rate limit și budget guardrail; momentan fetch-ul este manual/API.
3. Dashboard alpha decay v2:
   - curbe vizuale half-life, nu doar JSON/tabel;
   - alpha brut/net pe orizonturi;
   - separare clară stocks EC_V1 vs options overlay vs ETF overlay;
   - status de confidence bazat pe sample size și data quality.
4. Să legăm un runner real EC_V1 replay/backtest când suportul este stabil; hook-ul există, dar runner-ul real nu.
5. Outcome linking și mai complet:
   - skipped-stock counterfactuals din price history real;
   - MAE/MFE/slippage din live fills pentru toate pozițiile;
   - policy attribution pe `observed_7d/30d`, nu doar pattern metrics.
6. Crypto/Polymarket să reutilizeze același loop după ce au outcome complet și policy/runtime mai mature.
7. Weekly consensus încă este `mock`; trecerea pe Anthropic real trebuie făcută cu cost cap strict și după ce verificăm consumul.

## Cross-refs

- [[2026-05-18 Alert Intelligence deployed]]
- [[2026-05-19 Options Microstructure Overlay shadow MVP]]
- [[Automation_MVP]]
- [[ibkr_bot]]
- [[crypto-bot]]
