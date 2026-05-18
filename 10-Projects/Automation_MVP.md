---
title: Automation_MVP
type: project
status: active
risk: medium
stack:
  - Python
  - FastAPI
  - SQLite (WAL mode)
  - Telegram bot
tags:
  - project/inactive
  - product/tooling
  - product/orchestration
created: 2026
updated: 2026-05-18
---

# Automation_MVP

🟢 **ACTIVE via Strategy OS** — inițial minimal local orchestrator, acum conține panelul comercial Strategy OS + audit/AI/tickets/Alert Intelligence.

## On-disk

`C:\Personal\Trading Bots\Automation_MVP\`

## Purpose (per README)

Minimal local orchestrator for:
- **Composer** as default executor
- **Premium** as planner/reviewer escalation
- **Telegram approvals** pentru sensitive actions
- **SQLite audit trail** (WAL mode + busy timeout)
- **Separate worker process** pentru durability (NOT background thread în FastAPI)

## Fixes vs older demo

- No `threading.Thread` inside FastAPI
- Worker = separate long-running process
- Atomic run claiming (avoid double execution)
- Approval decisions:
  - `RETRY_SAFE` → rerun same path
  - `PLAN_B` → reroute plan B path
  - `ASK_PREMIUM` → reroute to premium planner/reviewer
  - `ABORT` → abort run
- Telegram polling pentru local MVP
- Authorization checks pe `chat_id` și `user_id`
- Repo registry cu explicit local path + allowed check prefixes

## Status

2026-05-18: Strategy OS este activ pe server `207.154.248.232` ca panel/pipeline operațional.

Nou:
- unified `strategy_audit_events`;
- stocks policy/evidence dashboard;
- AI Console;
- Telegram Ops;
- `Alert Intelligence` ca istoric central pentru alerte + daily reviews + weekly consensus timer.

Vezi [[2026-05-18 Alert Intelligence deployed]].

2026-05-18 update:
- added stocks `Volume Confirmation` shadow layer from volume research PDF;
- no live gate yet; it records VWAP/abnormal-volume/turnover context for later outcome analysis.

Vezi [[2026-05-18 Volume Confirmation shadow layer]].

## Layout

| Dir | Rol |
|---|---|
| `adapters/` | Tool adapters (Composer, premium) |
| `contracts/` | API contracts |
| `recipes/` | Workflow recipes |
| `research-labs/` | Adjacent research |
| `strategy-os/` | Strategy OS sub-project (has own README) |
| `templates/` | Run templates |
| `scripts/` | Ops scripts |
| `ops/` | Operational tooling |

## Cross-refs

- Independent tooling (NOT in active trading pipeline)
- Adjacent product: [[strategy-os-landing]] (landing page for strategy-os product spaced)
