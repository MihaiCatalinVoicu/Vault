# Reservation System Agent Memory — Obsidian Setup

## Scop

Acest vault este memoria operațională pentru agenți care lucrează pe `Reservation-System`.

Obsidian NU este mecanism de enforcement singur. Vault-ul este doar un folder cu fișiere Markdown. Enforcement-ul real se face prin:

1. `AGENTS.md` pentru Codex / agenți compatibili.
2. `CLAUDE.md` pentru Claude Code.
3. reguli explicite de lucru: agenții trebuie să citească fișierele din `docs/agent-memory` înainte să modifice meniuri/rute/visibility.
4. teste / script de audit pentru meniu, ca să nu mai existe „a mers într-un branch și a murit în altul”.

## Unde se pune

Recomandare:

```text
Reservation-System/
  AGENTS.md
  CLAUDE.md
  docs/
    agent-memory/
      _START_HERE.md
      00_PROJECT_CONSTITUTION.md
      10_NAVIGATION_CANONICAL_MAP.md
      20_VISIBILITY_AND_PACKAGING.md
      30_NAVIGATION_AUDIT_CHECKLIST.md
      40_KNOWN_DRIFT.md
      90_DECISION_LOG.md
```

Deschide `docs/agent-memory/` ca Obsidian Vault.

## Ce se comite în git

Comite:

```text
docs/agent-memory/*.md
AGENTS.md
CLAUDE.md
```

Opțional ignoră:

```text
docs/agent-memory/.obsidian/workspace.json
docs/agent-memory/.obsidian/workspace-mobile.json
```

Poți comite `.obsidian` doar dacă vrei aceleași pluginuri/setări pe toate mașinile.

## Regulă hard pentru agenți

Înainte de orice task care atinge:

- nav/sidebar/header
- routes
- moduleRouteRegistry
- visibility/VPF
- package entitlements
- onboarding/pachete
- FastOps
- Restaurant/Hotel/Spa/Fitness menu structure

agentul citește, în ordine:

1. `docs/agent-memory/_START_HERE.md`
2. `docs/agent-memory/10_NAVIGATION_CANONICAL_MAP.md`
3. `docs/product/VERTICAL_PACKAGING_MATRIX.md`
4. `docs/product/MODULE_VISIBILITY_RULES.md`

Dacă agentul nu poate confirma explicit că a citit aceste fișiere, task-ul nu este acceptabil.
