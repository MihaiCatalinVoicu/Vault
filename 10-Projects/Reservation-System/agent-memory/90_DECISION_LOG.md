# Decision Log

## 2026-05-25 — Obsidian/Agent Memory

Decision:
- Use Obsidian as Markdown memory vault.
- Use `AGENTS.md` and `CLAUDE.md` as enforcement entry points.
- Keep canonical menu map in `docs/agent-memory/10_NAVIGATION_CANONICAL_MAP.md`.
- Add nav audit checklist before correction/deploy.

Reason:
- Project menus drift across router/header/visibility registry/activeTab.
- Conversation context alone is not safe enough across agent sessions.

## 2026-05-25 — Menu Canonicalization

Decision:
- Restaurant, Hotel, Spa, Fitness must be mapped separately.
- Shared modules stay shared.
- Business Pulse is addon, not base Fitness.
- VIP/Private Circle remains hidden from basic tenants.
- `:3001` remains pending/dead and must not be dependency for admin/private circle.
