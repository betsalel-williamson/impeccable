# The docs system

How this repository's documentation is organized with Merlon Docs, which commands validate it, and how to keep it current. For contributors and coding agents who add or change docs.

## Four guides plus the legacy docs

| Guide | Audience | Holds |
|---|---|---|
| `docs/client/` | People who install Impeccable into their own project through a coding agent | Install, setup, running commands, the detector, live mode, privacy, troubleshooting |
| `docs/features/` | Anyone who needs to know what the product does and guarantees | Capabilities, contracts, design decisions |
| `docs/developer/` | Contributors to this repository | Layout, build, tests, workflows, releases, these docs |
| `docs/glossary/` | Everyone | One term per shard |

Each guide is a folder of small Markdown shards with an `index.md` that orders them. A shard has one audience and one job: it explains, or instructs, or serves as a lookup. When a page grows a second job, split it. Shards describe current behavior only; dates, plans, tickets and status belong in the issue tracker.

The older docs stay where they are, because files and agent instructions link to them by path: `README.md`, `AGENTS.md`, `CLAUDE.md`, the files in `docs/*.md`, and several READMEs under `tests/` and elsewhere. [Legacy docs](legacy-docs.md) lists them. A shard links to a legacy doc for depth rather than copying it.

## Commands

```bash
bun run docs:compile   # stitch each guide into docs/_build/<guide>.md
bun run docs:check     # full validation gate; must pass before you commit docs
bun run docs:review    # report-only sprawl signals: oversized shards, long indexes, duplicated passages
```

`docs/_build/` is generated and gitignored; never edit it. The `merlon` CLI comes from the `@merlon-docs/merlon-docs-cli` dev dependency, so `bun install` provides it. `docs:review` exits 0 whatever it finds; read its output and act on it.

## Configuration

`docs/merlon.config.json` sets the guide order and the review thresholds, and runs a strict coverage scan: `docs:check` fails on any Markdown file in the repository that no guide covers and that is not registered.

- `standaloneGuides` registers the legacy docs, so the scan counts them as covered. A new hand-written Markdown file outside the four guides needs an entry here, or it fails the check.
- `scan.ignore` excludes generated and fixture Markdown: the skill source and its generated copies in `plugin/`, `cursor-plugin/` and every harness dot-directory, test fixtures and oracle workspaces, demos, and the repository's own `PRODUCT.md` and `DESIGN.md`.

## The merlon-docs agent skill

Agents in this repository get the Merlon Docs workflow from the `merlon-docs` skill, installed at `.agents/skills/merlon-docs/`, symlinked from `.claude/skills/merlon-docs`, and pinned by hash in `skills-lock.json`. It is vendor-managed: never hand-edit it. To reinstall or update it:

```bash
npx skills add Merlon-Docs/merlon --skill merlon-docs
```

`bun run build:release` writes the generated `impeccable` skill into the same `skills/` folders but replaces only the entries it generates, so the `merlon-docs` skill and its symlink survive a release build. See [generated provider output](generated-provider-output.md).

## Keeping the docs current

Update the shard in the same change as the behavior it describes. When a change removes or renames a command, flag or term, search the four guides and the legacy docs for the old name before committing.

A periodic whole-set review is advisory. Nothing in CI or the release process schedules it, and no role owns it. Any contributor or agent who touches docs may run `bun run docs:review`, ideally about once a week, and work through what it reports. A run that finds something worth fixing leaves a commit with the fixes; a run that finds nothing leaves nothing behind.
