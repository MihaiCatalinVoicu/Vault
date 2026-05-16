---
title: Git workflow
type: reference
tags:
  - reference/process
  - reference/git
updated: 2026-05-16
---

# Git workflow — vault sync cross-laptop

## Setup (one-time)

### Pe Stefanini (locul where vault was created)

```bash
cd C:\Personal\Vault
git config user.name "Mihai Catalin Voicu"
git config user.email "mihai.catalin_voicu@yahoo.com"
# (verify NU email corporate)

# Create private repo on GitHub (one-time, web UI):
# https://github.com/new
# Name: vault
# Visibility: PRIVATE (NEVER public — vault may contain decision rationale you'd not share)
# DO NOT initialize with README

# Add remote:
git remote add origin git@github.com:MihaiCatalinVoicu/vault.git

# First push:
git add .
git commit -m "Initial vault setup"
git push -u origin main
```

### Pe laptop personal (când îl primești)

```bash
# Pick a location consistent cu pattern-ul tău
git clone git@github.com:MihaiCatalinVoicu/vault.git ~/Vault
cd ~/Vault

# Verifică email config:
git config user.name "Mihai Catalin Voicu"
git config user.email "mihai.catalin_voicu@yahoo.com"
```

## Daily workflow

### Începutul sesiunii

```bash
cd C:\Personal\Vault    # sau ~/Vault pe personal
git pull
```

→ Dacă alt laptop a făcut modificări între timp, le primești. Dacă apare merge conflict pe acelaș fișier, fix manual (rare, dar posibil dacă editezi același file pe 2 laptop-uri în paralel).

### În timpul sesiunii

Editezi în Obsidian app (sau Claude editează pentru tine). NU faci `git commit` la fiecare schimbare — devine zgomot.

### Sfârșitul sesiunii (sau la milestones)

```bash
cd C:\Personal\Vault
git add .
git status                          # verifică ce s-a schimbat
git commit -m "<scurt subiect>"     # e.g. "ibkr_bot: 2FA protocol decizie"
git push
```

## Commit message convention

Scurt, low-ceremony (vault e personal, nu open source):

- `home: add Q3 review section`
- `ibkr_bot: tier moved to $400/slot`
- `decisions: 2026-05-20 strategy X killed`
- `inbox: research crypto-carry comeback`
- `template: improve daily note`

## Merge conflicts (rare)

Dacă editezi același fișier pe 2 laptop-uri fără sync:

```bash
git pull        # va arăta conflict
# Deschizi fișierul în Obsidian sau VS Code
# Vezi markers <<<<<<<, =======, >>>>>>>
# Decizi ce păstrezi, ștergi markers
git add <file>
git commit -m "resolve conflict on <file>"
git push
```

Mitigation: `git pull` ALWAYS înainte de a începe edits.

## Backup paranoid

Vault-ul = knowledge work-ul tău. Recomandare:

1. **Primary**: git push către private GitHub (vezi mai sus)
2. **Secondary**: copy periodic pe USB encrypted (e.g. lunar)
3. **Tertiary**: dacă folosești iCloud personal / Dropbox personal, poți simply muta vault-ul acolo în loc de `C:\Personal\` — sync auto între device-uri fără git overhead

**NU** folosi OneDrive Stefanini (corporate) — vault e personal, dispare când returnezi laptopul.

## Ignore patterns

`.gitignore` la root:
- `.obsidian/workspace.json` (per-machine cursor state)
- `.obsidian/cache`
- `*.draft.md` (drafts marcate private)
- `**/private/` (sub-folders private)
- `**/credentials.md`, `**/*.secret.md` (paranoid filter)

## Cross-refs

- [[Claude session boundary]]
- [[Stefanini laptop monitoring]]
