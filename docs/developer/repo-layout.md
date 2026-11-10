# Repository layout

A reference map of the top-level directories in this repository, for contributors deciding where a change belongs. Edit source directories; regenerate everything marked generated.

## Source you edit

| Path | Holds |
|---|---|
| `skill/` | The skill source: `SKILL.src.md` (frontmatter, design laws, the Commands router table), `reference/` (one file per [command](../glossary/command.md) plus shared playbooks and the native platform references), `agents/` (subagent definitions), `scripts/` (the [launcher](../glossary/launcher.md), `VERSION`, `command-metadata.json`, the in-page live-mode JS) and `hermes-plugin/`. |
| `crates/` | The Cargo workspace that builds the [engine](../glossary/engine.md) binary, the rule engine, the wasm module and `cargo xtask`. [ENGINE.md](../ENGINE.md) maps every crate. |
| `browser-bundle/` | The page-side JavaScript the in-page detector bundle is assembled from. |
| `scripts/` | Build, release and test tooling: `build.js`, `release.mjs`, `run-tests.mjs`, `test-suites.mjs`, `fetch-engine.mjs`, `check-engine-release.mjs`, and the provider transformers under `scripts/lib/transformers/`. |
| `cli/` | The npm package: `cli/bin/cli.js`, a thin shim that finds and execs the engine binary, and `cli/platform-packages/`, the templates for the per-platform npm packages. |
| `extension/` | The browser extension shell. Its detector pieces under `extension/detector/` are built at package time and are gitignored. |
| `ui/component-review/` | The shared component review interface. |
| `vscode/` | The VS Code distribution of the GitHub Copilot skill. |
| `tests/` | Bun and Node suites, fixtures, the [oracle](../glossary/oracle.md) under `tests/oracle/`, framework fixtures, and the skill-behavior harness. |
| `docs/` | The four Merlon Docs guides plus the legacy docs. See [the docs system](docs-system.md). |

## Root files that pin things

| File | Role |
|---|---|
| `ENGINE_VERSION` | The engine release the launcher and the npm shim download. The build copies it to `skill/scripts/VERSION`. |
| `Cargo.toml`, `Cargo.lock`, `rust-toolchain.toml` | The workspace, its locked dependencies, and the stable channel with the `wasm32-unknown-unknown` target. |
| `.claude-plugin/plugin.json`, `.claude-plugin/marketplace.json`, `package.json` | The skill and CLI versions. See [versioning](versioning.md). |
| `skills-lock.json` | Pins agent skills installed into this repo for contributors, such as `merlon-docs`. |

## Generated, tracked

The root [harness](../glossary/harness.md) folders (`.agents/`, `.claude/`, `.cursor/`, `.gemini/`, `.github/skills/` and the other dot-directories), `plugin/` and `cursor-plugin/` are build output that stays committed so the repo installs directly. `crates/live/assets/` holds two generated files written by `cargo xtask bundle`. Read [generated provider output](generated-provider-output.md) before staging any of them.

## Generated, ignored

`dist/`, `build/`, `target/`, `skill/scripts/bin/` and `docs/_build/` are never committed.

## Not in this repository

The impeccable.style site, its changelog, the concept catalog and the review labs live in a private service repository. The eval framework lives in a separate private repository too. [CLAUDE.md](../../CLAUDE.md) describes how this repo reaches each of them.
