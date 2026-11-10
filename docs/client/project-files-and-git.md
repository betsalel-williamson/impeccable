# Project files and git

This page lists the files Impeccable writes into your project and says which ones to commit. It is for whoever owns the repository's `.gitignore`.

## Commit these

| File | Written by | Holds |
|---|---|---|
| `PRODUCT.md` | `init` | The product record: users, purpose, constraints, platform |
| `DESIGN.md` | `document`, or a redesign | The visual system |
| `.impeccable/design.json` | `document` | DESIGN.md extensions the Stitch format cannot hold |
| `.impeccable/config.json` | `init`, `hooks`, `ignores` | Shared settings: hook state, detector ignores, `buildPath` |
| `.impeccable/surfaces/*.md` | A request for a new page or screen | One [surface brief](../glossary/surface-brief.md) per page or screen, including its [mode](../glossary/mode.md) |
| `.impeccable/live/config.json` | `init` or first `live` run | Which files live mode injects into |
| `.impeccable/critique/ignore.md` | you | Critique findings the team agreed to ignore |

A blanket `.impeccable/` line in `.gitignore` hides all of this from teammates and from other worktrees. Ignore the runtime files instead.

## Keep these out

`.impeccable/config.local.json` holds per-developer overrides, including your answer to the design hook prompt. When Impeccable writes it, it also adds it to `.git/info/exclude`, so it stays on your machine without a `.gitignore` entry.

Several folders ignore themselves by writing their own `.gitignore` the first time something lands there:

- `.impeccable/questions/` (decision-round state)
- `.impeccable/mocks/` (generated comps)
- `.impeccable/review/` (captures)
- `.impeccable/critique/` and `.impeccable/audit/` (saved reports; `ignore.md` stays tracked)

Saved critique and audit reports stay local by default. Set `"reports": { "commit": true }` in `.impeccable/config.json` to commit them, or `"reports": { "enabled": false }` to stop saving them.

Live mode keeps its runtime files (sessions, previews, the server handshake, injected helper modules) out of git by writing a marked block into `.git/info/exclude`, or into `.gitignore` when the project is not a git checkout. The design hook writes `.impeccable/hook.cache.json` and `.impeccable/hook.pending.json`.

The [README](../../README.md) has a copyable `.gitignore` block that covers the hook files, screenshots and live runtime paths for teams that prefer explicit entries.

## Files committed by mistake

A `.gitignore` cannot untrack a file git already tracks. If you committed one of the runtime files, remove it from the index:

```bash
git rm --cached <path>
```

If `.impeccable/questions/roll.json` is tracked, Impeccable warns once and writes new roll records to `roll.local.json` instead, so the tracked file stops changing.

## Monorepos

Each app can carry its own `PRODUCT.md` and `.impeccable/`. An app without a PRODUCT.md inherits the one above it, up to the repository root. Run `/impeccable doctor` and name the app to see which apps carry their own context, which inherit, and which have none.
