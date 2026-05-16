# VenueHub Vault

**Owner:** Mihai Catalin Voicu — `mihai.catalin_voicu@yahoo.com` (NU email corporate).
**Created:** 2026-05-16.
**Purpose:** persistent knowledge base across Claude Code sessions, across laptops, across projects.

## What goes here

| Folder | What | When to use |
|---|---|---|
| `00-Index/` | Maps of Content (MOC) — entry points | Start here when picking up work after time away |
| `10-Projects/` | 1 file per active project | Current state, blockers, links to runbooks |
| `20-Decisions/` | Decision log, dated, rationale-heavy | When choosing direction (kill/start/pivot) |
| `30-Daily/` | Daily notes (optional, only when useful) | Quick captures, end-of-day reflections |
| `40-Reference/` | Persistent reference, runbooks, commands | Anything reused across sessions |
| `50-Research/` | External material collected (PDFs, reports) | When consuming research that informs decisions |
| `90-Inbox/` | Quick capture, sorted later | Don't think where it goes — drop here |
| `_attachments/` | Images, PDFs, screenshots | Auto-managed by Obsidian |
| `_templates/` | Daily / Decision / Project templates | Copy via Obsidian Templates plugin |

## How to open

1. Open Obsidian (download from `https://obsidian.md` if not installed)
2. "Open folder as vault" → select `C:\Personal\Vault\`
3. First time only: enable "Templates" core plugin + "Daily notes" core plugin in Settings → Core plugins.

## Cross-laptop sync — git workflow

```bash
# On personal laptop (first time):
git clone <remote-url> ~/Vault
# (or copy folder via USB if no remote yet)

# Daily workflow on either laptop:
cd C:\Personal\Vault            # or ~/Vault on personal
git pull                         # before starting work
# ... edit notes in Obsidian ...
git add . && git commit -m "<topic>" && git push
```

**Setup remote** (one-time): create private repo on GitHub `MihaiCatalinVoicu/vault` and:

```bash
cd C:\Personal\Vault
git remote add origin git@github.com:MihaiCatalinVoicu/vault.git
git push -u origin main
```

## How Claude uses this vault

When starting a new session, point Claude here. Claude reads `[[00-Index/Home]]` as entry point, then navigates via wikilinks to relevant projects/decisions. Claude can write to it too (with `kepano-obsidian-markdown` skill — wikilinks, callouts, frontmatter respected).

See `[[40-Reference/Claude session boundary]]` for the rules of engagement.
