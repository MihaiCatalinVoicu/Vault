---
title: Setup Obsidian vault
type: decision
date: 2026-05-16
status: executed
impact: long-term
tags:
  - decision/infrastructure
  - decision/knowledge-management
---

# 2026-05-16 — Setup Obsidian vault

## Context

Folosim Claude Code auto-memory (`~/.claude/projects/.../memory/MEMORY.md`) ca persistent memory between sessions. Are 13 entries (proiecte active, strategii killed, reguli standing). Funcționează pentru surface facts ("nu folosi email Stefanini"), dar nu pentru knowledge work cu rationale lung.

Cum apare 3+ proiecte active (Reservation-System, ibkr_bot, crypto-bot) + decizii inverse acumulate (Lane A killed, crypto-carry abandoned, pullback killed), MEMORY.md flat devine insufficient:
- Nu poate ține rationale-ul "de ce am decis X" (devine prea lung)
- Nu poate lega "pattern comun între toate strategiile killed"
- Nu poate cross-link cu research extern (BIS, BitMEX 2025)
- Nu suportă navigation prin grafr (wikilinks + backlinks)

## Decizia

Setez **Obsidian vault** la `C:\Personal\Vault\` ca extension a memoriei native — NU înlocuire.

Layered:
1. **MEMORY.md** rămâne pentru surface facts, rules, pointers (loaded la fiecare sesiune fără effort)
2. **Vault Obsidian** pentru knowledge graph (citit on-demand, când Claude e pointed acolo)
3. **`anthropic-skills:consolidate-memory`** skill pentru curățenie periodică pe MEMORY.md

## Setup concret

- Locație: `C:\Personal\Vault\` (consistent cu `C:\Personal\Trading Bots`, `Dating App` pattern)
- Folder structure (numbered prefix pentru ordine deterministică):
  - `00-Index/` — Home, MOC (Maps of Content)
  - `10-Projects/` — Reservation-System, ibkr_bot, crypto-bot
  - `20-Decisions/` — decision log
  - `30-Daily/` — daily notes opțional
  - `40-Reference/` — runbooks, monitoring, session boundary
  - `50-Research/` — extern research collected
  - `90-Inbox/` — quick capture
  - `_attachments/`, `_templates/`
- Git-initialized → cross-laptop sync via private GitHub repo (TBD)
- Sintaxă: Obsidian Flavored Markdown (wikilinks `[[]]`, callouts `> [!note]`, frontmatter YAML)

## Skills relevante (deja installed)

- `kepano-obsidian-markdown` — editare conform sintaxei Obsidian
- `kepano-obsidian-bases` — pentru `.base` files (TBD usage)
- `kepano-json-canvas` — pentru `.canvas` files (TBD usage)
- `kepano-obsidian-cli` — interact cu Obsidian app via CLI (opțional)
- `kepano-defuddle` — clean HTML→markdown pentru research collection (util)

## Cross-laptop sync (TBD owner action)

Vault e git repo. Următorul pas: create private GitHub repo `MihaiCatalinVoicu/vault` + `git remote add origin` + first push. Apoi pe laptop personal: `git clone` într-o locație simetrică.

## Done gate

- [x] Vault folder created at `C:\Personal\Vault\`
- [x] Skeleton subfolders + git init
- [x] Initial Home + MOC + 3 project files
- [x] 3 decision files populated din MEMORY.md
- [x] Reference files (Stefanini monitoring, Claude session boundary)
- [ ] Owner installs Obsidian app + opens vault (manual UI step)
- [ ] Owner creates private GitHub remote + first push
- [ ] Owner clones on personal laptop (next session)

## Rollback

Dacă vault-ul nu se dovedește util în 4 săptămâni: `rm -rf C:\Personal\Vault\` + revine la MEMORY.md only. Zero loss — MEMORY.md rămâne sursa primary, vault e extension.

## Cross-refs

- [[Home]]
- [[Claude session boundary]]
- Skills installed: `~/.claude/skills/kepano-*/`
