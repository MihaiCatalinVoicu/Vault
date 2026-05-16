---
title: Automation_MVP
type: project
status: inactive
risk: off
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
updated: 2026-05-16
---

# Automation_MVP

🟢 **OFF (INACTIVE)** — per `Trading Bots/CLAUDE.md`: "Telegram poller mort din mar 2026". Minimal local orchestrator pentru Composer + premium planner escalation cu Telegram approvals.

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

Telegram poller mort din martie 2026. Service inactiv.

**Întrebare deschisă:** mai folosim conceptul? Dacă da, ar trebui revived sau înlocuit cu altă tehnologie? Dacă nu, marchează clar ca abandoned (similar cu strategiile killed în [[crypto-bot]]).

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
