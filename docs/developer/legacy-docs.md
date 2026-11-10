# Legacy docs reference

A lookup table of every standalone document registered in `standaloneGuides` in `docs/merlon.config.json`: the Markdown that lives outside the four Merlon guides and stays at its path because other files and agent instructions link to it. For contributors deciding where to link for depth, or which file to update alongside a shard. [The docs system](docs-system.md) explains why these files stay put.

## Repository root

| Path | Audience | Page type | Purpose |
|---|---|---|---|
| [README.md](../../README.md) | Users | Overview | The project front page: what Impeccable is, how to install it, the command list. |
| [README.npm.md](../../README.npm.md) | npm users | Overview | The npm package README for the CLI, swapped in for `README.md` when the package is packed. |
| [AGENTS.md](../../AGENTS.md) | Coding agents and contributors | Instructions | Repository guidelines for agents: layout, commands, testing, rule workflow, PR review. |
| [CLAUDE.md](../../CLAUDE.md) | Claude Code and maintainers | Instructions | The most complete project instructions: architecture, build, tests, versioning, releases. |
| [NOTICE.md](../../NOTICE.md) | Everyone | Legal notice | Third-party attribution, including the source of the iOS and Android references. |

## docs/

| Path | Audience | Page type | Purpose |
|---|---|---|---|
| [AGENTS-ENGINE.md](../AGENTS-ENGINE.md) | Engine contributors | Reference | The JSON interface and safety rules of the `impeccable agents` configuration engine. |
| [BUNDLE-SIGNING.md](../BUNDLE-SIGNING.md) | Maintainers | Reference | How skill ZIP signatures are made and checked before `install`, `update` and `check` extract a bundle. |
| [CLI-CONTRACT.md](../CLI-CONTRACT.md) | Engine contributors | Specification | The observable behavior of every `impeccable` verb. |
| [COMP-FIDELITY.md](../COMP-FIDELITY.md) | Skill and engine contributors | Design note | Measuring a comp-led build against its comp. |
| [COMP-GATE-INTEGRITY.md](../COMP-GATE-INTEGRITY.md) | Engine contributors | Reference | Evidence and integrity requirements of the comp fidelity gate. |
| [CURSOR-PLUGIN.md](../CURSOR-PLUGIN.md) | Cursor users | Overview | Source of the Cursor plugin README; the build copies it into the plugin. |
| [DEVELOP.md](../DEVELOP.md) | Contributors | Guide | Provider transformer architecture and how to add a provider. |
| [ENGINE.md](../ENGINE.md) | Engine contributors | Explanation | The crate map, the browser-bundle flow, rule packs. |
| [HARNESSES.md](../HARNESSES.md) | Build contributors | Reference | What each coding-agent harness supports: frontmatter, directories, hooks, placeholders. |
| [LIVE-REWRITE-PLAN.md](../LIVE-REWRITE-PLAN.md) | Live-mode contributors | Design record | The architecture behind live mode's roots manifest and preview handling. |
| [NATIVE-CAPTURE.md](../NATIVE-CAPTURE.md) | Engine contributors | Reference | Native static-entry capture and the sandbox transport for the comp gate. |
| [PLAN-REVIEW.md](../PLAN-REVIEW.md) | Skill and engine contributors | Design note | The plan and asset review step in comp-led builds. |
| [PORTING-GUIDE.md](../PORTING-GUIDE.md) | Engine contributors | Conventions | Rules for byte-equal ports and crate boundaries. |
| [RELEASE-4.4.0.md](../RELEASE-4.4.0.md) | Maintainers | Release notes | Notes for one release candidate. |
| [REVIEW-4.4.0-READINESS.md](../REVIEW-4.4.0-READINESS.md) | Maintainers | Review handoff | Behavior to preserve when reviewing one release candidate. |
| [RUNTIME-ENV.md](../RUNTIME-ENV.md) | Engine contributors | Reference | Environment variables and paths the binary reads. |
| [STYLE.md](../STYLE.md) | Anyone writing copy | Style guide | The editorial brief and the prose denylist. |
| [VSCODE-EXTENSION.md](../VSCODE-EXTENSION.md) | Maintainers | Guide | Packaging and publishing the VS Code skill extension. |
| [WINDOWS-SIGNING.md](../WINDOWS-SIGNING.md) | Maintainers | Reference | Signing the Windows engine binary in releases. |
| [adr-live-variant-mode.md](../adr-live-variant-mode.md) | Contributors | ADR | The decision record for live variant mode. |
| [openai-plugin-submission.md](../openai-plugin-submission.md) | Maintainers | Checklist | The submission packet for the OpenAI plugin directory. |

## Elsewhere in the tree

| Path | Audience | Page type | Purpose |
|---|---|---|---|
| [browser-bundle/README.md](../../browser-bundle/README.md) | Engine contributors | Reference | The page-side JavaScript of the detector and how it is bundled. |
| [cli/platform-packages/README.md](../../cli/platform-packages/README.md) | Maintainers | Reference | Templates for the per-platform npm packages. |
| [crates/live/assets/README.md](../../crates/live/assets/README.md) | Engine contributors | Reference | The two generated live assets and what writes them. |
| [cursor-plugin/README.md](../../cursor-plugin/README.md) | Cursor users | Overview | The generated copy of `docs/CURSOR-PLUGIN.md` inside the Cursor plugin. |
| [extension/STORE_LISTING.md](../../extension/STORE_LISTING.md) | Maintainers | Listing copy | Chrome Web Store dashboard fields. |
| [tests/oracle/README.md](../../tests/oracle/README.md) | Contributors | Reference | How oracle cases run, record and compare. |
| [tests/oracle/DELTAS.md](../../tests/oracle/DELTAS.md) | Contributors | Reference | Reviewed, intentional differences from recorded goldens. |
| [tests/oracle/vectors/README.md](../../tests/oracle/vectors/README.md) | Contributors | Reference | The frozen function-level vectors. |
| [tests/skill-behavior/README.md](../../tests/skill-behavior/README.md) | Contributors | Reference | Skill-behavior scenarios and their baseline. |
| [tests/framework-fixtures/README.md](../../tests/framework-fixtures/README.md) | Contributors | Reference | Fixture layout and the `fixture.json` schema. |
| [tests/new-work-e2e/README.md](../../tests/new-work-e2e/README.md) | Contributors | Reference | The new-work browser smoke suite. |
| [tests/fixtures/extension-scan/README.md](../../tests/fixtures/extension-scan/README.md) | Contributors | How-to | Manual smoke test for the extension scanner. |
| [ui/component-review/README.md](../../ui/component-review/README.md) | Contributors | Reference | The shared component review interface and its data model. |
| [vscode/README.md](../../vscode/README.md) | VS Code users | Overview | The VS Code Marketplace README for the Copilot skill. |
| [.github/PULL_REQUEST_TEMPLATE.md](../../.github/PULL_REQUEST_TEMPLATE.md) | Contributors | Template | The pull request form. |
| [.github/ISSUE_TEMPLATE/bug_report.md](../../.github/ISSUE_TEMPLATE/bug_report.md) | Users | Template | The bug report form. |
| [.github/ISSUE_TEMPLATE/feature_request.md](../../.github/ISSUE_TEMPLATE/feature_request.md) | Users | Template | The feature request form. |
