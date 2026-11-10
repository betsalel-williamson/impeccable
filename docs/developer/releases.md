# Releases

How the maintainer tags and publishes each component, in the order the release script enforces. For whoever cuts a release. Version numbers are covered in [versioning](versioning.md); [CLAUDE.md](../../CLAUDE.md) has the long-form notes, including the changelog style.

## Components and tags

| Component | Command | Tag | Attached artifacts | Afterward |
|---|---|---|---|---|
| Engine | `bun run release:engine` | `engine-v<ENGINE_VERSION>` | Built and attached by the `release-engine.yml` workflow: five platform binaries, each with a `.sha256` | `bun run release:platform-packages` publishes the five `@impeccable/cli-<os>-<arch>` npm packages |
| Skill | `bun run release:skill` | `skill-v<version>` | `dist/universal.zip` and its signature `dist/universal.zip.sig.json` | Release the CLI at the same version, then move the published-skill pointer |
| CLI | `bun run release:cli` | `cli-v<version>` | None | `npm publish` |
| Extension | `bun run release:ext` | `ext-v<version>` | `dist/extension.zip`, `dist/extension-firefox.zip` | Upload to the Chrome Web Store and to addons.mozilla.org |

Preview any component first with `node scripts/release.mjs <component> --dry-run`. A dry run neither tags nor signs.

## Release order

The launcher, the npm shim and `impeccable install` all resolve the [engine](../glossary/engine.md) binary for the pinned `ENGINE_VERSION`, so nothing works until that engine exists. The order is fixed:

1. Release the engine. The script checks the platform pins in `package.json`, tags, and pushes; CI builds and publishes the binaries.
2. Publish the five platform packages.
3. Release the skill, then the CLI at the same version. Only now merge a branch that bumps `ENGINE_VERSION`.

`scripts/check-engine-release.mjs` (also `bun run check:engine-release`) verifies steps 1 and 2 for the pinned version and names every missing asset. CI's `engine-release-ready` job runs it on every CI run, so a pin ahead of its release fails CI.

## What the script refuses

For the skill and the CLI, `scripts/release.mjs` refuses to tag when:

- engine source (`crates/`, `Cargo.toml`, `Cargo.lock`, `rust-toolchain.toml`, `browser-bundle/` and the other files listed in `ENGINE_SOURCE_PATHS`) differs from the `engine-v<ENGINE_VERSION>` tag, or that tag is missing on origin;
- any engine release asset or platform package is missing.

For the skill, the CLI and the extension it refuses on a dirty working tree, a HEAD that is ahead of origin, a tag that already exists, or a missing or malformed changelog entry. The engine release makes the same tree, push and tag checks and has no changelog entry. For the skill and the extension it reruns `bun run build:release` or `bun run build:extension` and refuses if that leaves uncommitted changes. The skill release also refuses when `plugin.json` and `marketplace.json` disagree.

The extension is exempt from the engine checks because it ships its own wasm detector. Two bypasses exist for narrow cases: `IMPECCABLE_SKIP_ENGINE_SOURCE_CHECK=1` for engine-tree changes that cannot reach the binary, such as test-only edits, and `IMPECCABLE_SKIP_ENGINE_CHECK=1` when the assets exist but the registry probe cannot reach them.

## The changelog

Release notes come from `site/pages/changelog.astro` in the private site repository; the script reads it from `../impeccable-site/` beside this checkout. It finds the entry by its label: `v<version>` for the skill, `CLI v<version>` for the CLI, `Extension v<version>` for the extension. To fix notes after shipping, run `gh release edit <tag> --notes-file <file>`.

## After a skill release

`npx impeccable install` and `update` serve the version named in the site's signed published-skill pointer, which does not follow a release on its own. The script warns when `impeccable.style/api/version` still reports an older version. Moving the pointer is a site-repository change.
