# Versioning

Which manifest carries which component's version, which numbers must agree, and when a version changes. A reference for contributors and for the maintainer cutting a release. [CLAUDE.md](../../CLAUDE.md) holds the full maintainer notes.

## Feature changes never bump versions

A feature pull request does not touch a manifest version or add a changelog entry. A version in a feature branch conflicts with every other open branch, and a changelog entry describes a release that has not happened. The maintainer bumps versions and writes the changelog when cutting the release; the only pull request that edits a version is the release itself.

## Where each version lives

| Component | Version source | Must agree with |
|---|---|---|
| Skill | `.claude-plugin/plugin.json` `version` | `.claude-plugin/marketplace.json` `plugins[0].version`, `package.json` `version`, `plugin/.claude-plugin/plugin.json`, the `version` in `plugin/skills/impeccable/SKILL.md`, the Cursor plugin manifest and skill in `cursor-plugin/` and `dist/cursor-plugin/`, and the Codex plugin manifest under `dist/openai/` |
| CLI (npm) | `package.json` `version` | The skill version. The skill and the CLI always move together. |
| Engine pin | `ENGINE_VERSION` | `skill/scripts/VERSION` (the build copies it), every `@impeccable/cli-<os>-<arch>` entry in `package.json` `optionalDependencies`, and `skill/scripts/engine-checksums.txt` |
| Extension | `extension/manifest.json` `version` | Nothing; it versions on its own. |

## The checks that enforce it

`validatePluginVersions` in `scripts/build.js` (logic in `scripts/lib/validate-plugin-versions.js`) reads `.claude-plugin/plugin.json` and fails `bun run build` when any file in the skill row above disagrees. It skips a file that does not exist yet, and it checks `package.json` only when that file is the CLI, recognized by its `@impeccable/cli-` optional dependencies. After bumping the skill version, run `bun run build:release` so the committed `plugin/` and `cursor-plugin/` copies regenerate at the new number.

`scripts/release.mjs` adds its own checks: the skill release refuses when `plugin.json` and `marketplace.json` disagree, and the engine release refuses when the `optionalDependencies` pins differ from `ENGINE_VERSION`.

## When each one changes

- **Skill and CLI:** when skill content changes (`skill/`, references, command metadata) or the npm shim changes (`cli/bin/cli.js`, `cli/platform-packages/`). Either change moves both numbers.
- **Engine pin:** when a new engine release is published. Keep the `optionalDependencies` at the same version, run `bun run build` to rewrite `skill/scripts/VERSION`, and run `node scripts/check-engine-release.mjs --record` to rewrite the checksums the launcher and the npm shim verify engine downloads against. A skill release that needs the new engine bumps the pin with it.
- **Extension:** when extension code changes, or a rule change alters what the shipped detector finds.

Tagging and publishing are in [releases](releases.md).
