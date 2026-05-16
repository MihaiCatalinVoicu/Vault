---
title: Claude session boundary
type: reference
tags:
  - reference/claude
  - reference/process
updated: 2026-05-16
---

# Claude session boundary

Cum lucrez eu (Claude Code agent) cu acest vault, între sesiuni, între laptopuri.

## Entry point per sesiune

> [!info] La fiecare sesiune nouă, citește **întâi** `[[Home]]`.
> De acolo navighezi prin wikilinks la projects, decisions, references relevante. NU citi tot vault-ul la pornire (token waste).

## Rules of engagement

### What I read

- `[[Home]]` la pornire — singular entry point
- Pagini referite explicit prin `[[wikilinks]]` din contextul curent
- Decision files relevante când consider un step nou (verifică dacă deja killed)

### What I write

- **Add to existing**: dacă găsesc pagina existentă, append (cu marcaj data + topic)
- **New decisions**: în `20-Decisions/YYYY-MM-DD <title>.md` cu template `[[_templates/Decision]]`
- **New projects**: în `10-Projects/<name>.md` cu template `[[_templates/Project]]`
- **Quick capture (ambiguous)**: `90-Inbox/<topic>.md` — sortez ulterior cu user

### What I do NOT touch fără confirmare

- `00-Index/Home.md` (modify cu grijă — entry point critical, schimbări need user OK)
- Pagini cu tag `#locked` în frontmatter (TBD convenție)

## Skills disponibile pentru vault

Toate `kepano-*` din `~/.claude/skills/`:

- `kepano-obsidian-markdown` — sintaxă Obsidian (wikilinks, callouts, frontmatter)
- `kepano-obsidian-bases` — `.base` files (databases)
- `kepano-json-canvas` — `.canvas` files (visual)
- `kepano-obsidian-cli` — interact cu Obsidian app (opțional)
- `kepano-defuddle` — clean web→markdown (pentru research collection)

Plus `anthropic-skills:consolidate-memory` pentru consolidare periodică pe `MEMORY.md` (separat de vault).

## Cross-laptop continuity

Vault-ul e git repo. La transition Stefanini → personal:

**Stefanini (înainte de switch):**

```bash
cd C:\Personal\Vault
git add . && git commit -m "<topic>" && git push
```

**Personal (după switch):**

```bash
cd ~/Vault    # sau ce locație ai pe personal
git pull
# deschide în Obsidian
```

Eu (Claude) văd starea la fel pe ambele laptopuri după pull. Continuitate naturală.

## Limits

- **Eu nu deschid Obsidian app** — citesc fișierele direct prin filesystem
- **Eu nu instalez plugins Obsidian** — sunt JS, NOT skills; le manage-uiești tu prin Obsidian UI
- **Vault path** trebuie să-mi fie comunicat la prima sesiune pe un laptop nou (`C:\Personal\Vault\` pe Stefanini, TBD pe personal)

## Privacy

- **NU pune secrete în vault** (parole, API keys, tokens). Folosește vault personal (1Password / Bitwarden) pentru asta.
- **NU pune PII clienți** (date GDPR-protected). Conține doar metadata + structured notes.
- **Vault tracked în git** — push doar pe **private** repo. Verifică `git remote -v` înainte de prima push pe laptop nou.

## How to "warm up" me at session start

Bun start de sesiune:

> "Hi, openning Reservation-System work. Vault at `C:\Personal\Vault`. Read Home + Reservation-System project file."

Sau, mai short:

> "Vault path: `C:\Personal\Vault`. Pick up Sprint 5."

Eu citesc Home → Reservation-System → identifc Sprint 5 status → continui de unde am rămas.

## Cross-refs

- [[Home]]
- [[Stefanini laptop monitoring]]
- [[Git workflow]]
