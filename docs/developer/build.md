# Build the skill

How to build the provider outputs from `skill/` and read what the build checks. For contributors who edited the skill, the transformers, the counts in user-facing files, or anything the build validates.

## Pick the build

```bash
bun run build            # dist/ only; tracked harness dirs untouched
bun run rebuild          # clean, then build
bun run build:release    # also syncs the root harness dirs, plugin/ and cursor-plugin/
bun run rebuild:release  # clean, then release build
```

Run `bun run build` after any change under `skill/`, `scripts/lib/transformers/`, or a file that quotes a command or rule count. It runs `scripts/build.js` with `--skip-root-sync`, so it writes `dist/` and copies it to `build/_data/dist` without touching tracked files. The release build is for intentional generated-output refreshes; [generated provider output](generated-provider-output.md) says when that is.

## What the build does

`scripts/build.js` copies `ENGINE_VERSION` to `skill/scripts/VERSION`, reads `skill/SKILL.src.md` with its `reference/`, `scripts/` and `agents/` siblings, and runs one transformer per provider from `scripts/lib/transformers/providers.js`. Each transformer substitutes the per-provider placeholders (`{{model}}`, `{{config_file}}`, `{{ask_instruction}}`, `{{command_prefix}}`, `{{available_commands}}`, `{{scripts_path}}`, and `{{reference_path}}` in agent bodies) and writes `dist/<provider>/`. It then stages the OpenAI, Cursor and VS Code packages and zips `dist/universal.zip`.

The skill's `scripts/` payload ships as is: the launcher, `impeccable.cmd`, `VERSION`, `command-metadata.json` and the page JS. Release zips are launcher-only, and the launcher downloads the pinned [engine](../glossary/engine.md) on first run. `IMPECCABLE_BUNDLE_ENGINE=1 bun run build:release` stages every platform binary into the dist copies for offline installs.

## The gates

The build exits non-zero when any of these fail:

| Gate | Checks |
|---|---|
| `generateCounts` | Command and rule counts in `README.md`, `README.npm.md`, `AGENTS.md` and the two `.claude-plugin` manifests against the router table and `crates/live/assets/antipatterns.json`. |
| `validatePluginVersions` | Every plugin manifest and bundled `SKILL.md` against `.claude-plugin/plugin.json`. See [versioning](versioning.md). |
| `validatePluginManifestShape` | The generated plugin manifest uses only keys the Claude Code loader is known to honor. |
| `validateProse`, `validateSkillProse` | The prose denylist. See [writing prose](writing-prose.md). |
| `validateAskInstructionSites` | `{{ask_instruction}}` sits where a full sentence fits. |
| `validateClaudeCodeDescription` | The skill description fits Claude Code's listing budget. |
| `validateBatchLineEndings` | Every emitted `.cmd` file uses CRLF. |

## Add a provider

A provider is one config entry in `scripts/lib/transformers/providers.js` plus its placeholder values in `scripts/lib/utils.js`. [DEVELOP.md](../DEVELOP.md) walks through the fields and [HARNESSES.md](../HARNESSES.md) records what each harness supports.

Keep the named exports in `scripts/lib/transformers/index.js` (`transformCursor`, `transformClaudeCode` and the rest). The build does not call them, but `tests/build.test.js` spies on them.

## Get an engine binary

The build does not need the engine, but most tests do:

```bash
bun run fetch:engine                      # pinned release into skill/scripts/bin/<os>-<arch>/
cargo build --release -p impeccable       # this checkout's source into target/release/
```

[Run the tests](testing.md) explains how suites find the binary.
