# Generated provider output

What the committed [harness](../glossary/harness.md) folders are, why they are tracked, and when a contributor should stage them. Read this before a commit that shows changes under a dot-directory, `plugin/` or `cursor-plugin/`.

## What is generated

`bun run build:release` writes the transformed skill into the root harness folders (`.agents/skills/`, `.claude/skills/`, `.cursor/skills/`, `.gemini/skills/`, `.github/skills/`, `.kiro/skills/`, `.opencode/skills/` and the rest named in `scripts/lib/transformers/providers.js`), their `agents/` folders where the provider has subagents, the Claude Code plugin subtree in `plugin/`, and the Cursor plugin in `cursor-plugin/`. The `.codex` provider is built into `dist/` only.

These folders are committed on purpose. `npx skills` reads them straight from the repository, direct GitHub installs and git submodules use them, and the Claude Code marketplace installs from `plugin/`. Never gitignore them, and never hand-edit them: fix the source in `skill/`, `scripts/` or `cli/` and rebuild.

## Source-first commits

A feature change stages source only: `skill/`, `crates/`, `browser-bundle/`, `scripts/`, `cli/`, `extension/`, `tests/` and `docs/`. Validate with `bun run build`, which leaves the tracked folders alone. Stage regenerated harness output only when the change is a release, a generated-output sync, or a build-system change whose point is the output.

After source lands on `main`, the `sync-generated-output.yml` workflow runs `bun run build:release` and commits any drift in the generated paths back to `main`. CI also runs `git diff --exit-code` over the main harness folders and `plugin/` after `bun run build`, so a source-first build that rewrites them fails the PR.

## What the sync leaves alone

The release build replaces only the entries it generates inside each provider's `skills/` folder. Other skills in the same folder survive a rebuild, which is how `.agents/skills/merlon-docs/` and its `.claude/skills/merlon-docs` symlink coexist with the generated `impeccable` skill. Per-project script artifacts inside a generated skill are stashed and restored around the copy. Provider `agents/` folders are different: the sync deletes and rewrites them whole, so nothing hand-placed belongs there.

## Two tracked engine assets

`crates/live/assets/detect-antipatterns-browser.js` and `crates/live/assets/antipatterns.json` are generated too, but they follow the rule workflow: `cargo xtask bundle` rewrites them and you commit both with the rule change. See [add a detector rule](add-a-detector-rule.md).

## Local state

Files a harness writes for itself inside these folders, such as `.claude/settings.local.json` and `.claude/scheduled_tasks.lock`, are gitignored.
