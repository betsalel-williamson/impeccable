# Write prose for this repo

Which prose rules apply where, and which of them the build enforces. For contributors editing the READMEs, skill text, or these docs. The editorial brief itself, with every banned phrase, its reason and its replacement, is [STYLE.md](../STYLE.md); read it before writing anything a user or an agent will read.

## The two build gates

| Gate | Scans | Fails on |
|---|---|---|
| `validateProse` | `README.md`, `README.npm.md` | Em dashes (the character and its HTML entities), ` -- ` used as a dash, and the full phrase denylist in STYLE.md |
| `validateSkillProse` | `skill/**/*.md` | Em dashes, plus the subset of denylisted phrases that have no technical reading (the list is in `validateSkillProse` in `scripts/build.js`) |

Both run in `bun run build` and print a rationale and a replacement for each hit. The marketing adjectives and verbs on the denylist are allowed in `skill/`, because several of them have real technical uses there. Only Markdown under `skill/` is scanned, so an em dash in a JavaScript comment under `skill/scripts/` passes; an em dash in `skill/reference/*.md` fails the build.

Do not work around a rule by bending the spelling past the regex. If a banned word has earned a real meaning here, propose an amendment to STYLE.md instead.

## What the gates cannot catch

STYLE.md also lists the structural tells that need a human: the negation pivot ("not X, it's Y"), lists that are always three items long, uniform paragraph rhythm, hollow confidence without a number, stacked hedges, and copy that stays true if you swap in a competitor's name. Check every paragraph you write against them.

## Skill text has its own constraints

- Accessibility guidance lives in `skill/reference/audit.md` only. Reminding the model about it at design time makes output timid.
- The mode rules for directions and comps live in `reference/mode-persuade.md`, `mode-operate.md` and `mode-read.md`. Keep mode clauses out of the shared playbooks.
- Native platform guidance lives in `reference/ios.md`, `reference/android.md` and the `*.native.md` variants, not as translation notes inside web references.

## These docs

Shards in `docs/client/`, `docs/features/`, `docs/developer/` and `docs/glossary/` follow STYLE.md too. The build does not scan them; `bun run docs:check` validates their structure and links. [The docs system](docs-system.md) covers the workflow.
