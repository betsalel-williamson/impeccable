# The design hook

This page states what the [design hook](../glossary/design-hook.md) does after an agent edits a UI file, which [harnesses](../glossary/harness.md) run it, and what it guarantees about noise and cost. It is for anyone deciding whether to turn it on or reading its output.

The hook is the [detector](detector.md), run by the agent's harness instead of by a person. The `hook` verb takes the harness's event on stdin and answers with at most one JSON document; it always exits 0, so a hook failure never blocks the agent (`crates/hook/src/hook.rs`).

## Two passes

| Pass | When | Rules shown |
|---|---|---|
| Per edit | after an Edit or Write to a watched file | the immediate tier only |
| Deep pass | when the agent's turn ends (`Stop`) | every rule, over every UI file touched in the session, minus what the per-edit pass already reported |

The immediate tier is the list `IMMEDIATE_TIER_RULES` in `crates/foundation/src/registry.rs`: broken output, contrast and legibility failures, gradient text, glow shadows and design-system drift. Setting `hook.perEditRules` to `"all"` in `.impeccable/config.json` shows every rule per edit. Harnesses whose stop event cannot feed context back (Cursor, GitHub Copilot) keep the full rule set per edit. The per-harness matrix is in [`skill/reference/hooks.md`](../../skill/reference/hooks.md).

## Guarantees

**It watches UI files only.** The built-in extensions are `.tsx`, `.jsx`, `.html`, `.htm`, `.vue`, `.svelte`, `.astro`, `.css`, `.scss`, `.sass`, `.less`, `.ts` and `.js` (`ALLOWED_EXTS` in `crates/hook/src/hook_lib.rs`). `detector.extensions` in config adds template extensions such as `.blade.php`. Plain `.ts` and `.js` files stay silent unless something is found. Generated paths and secret-looking files are skipped.

**It stands down on native projects.** When `PRODUCT.md` declares `ios`, `android` or `adaptive`, both `hook` and `hook-before-edit` skip the scan and record `native-platform` in the audit entry. See [Platforms](platforms.md).

**It stands down on live previews.** A file holding a [live mode](live-mode.md) variant wrapper or an accept block in progress is skipped.

**It never writes ignores.** Every exception goes through `impeccable hooks ignore-value`, `ignore-file` or `ignore-rule`, which edit the shared or local config. The hook itself only reads them.

**Output is bounded.** Per edit it reports at most 5 findings by default, capped by `hook.limits` in config.

**It is project-scoped and reversible.** `impeccable hooks on`, `off`, `status` and `reset` manage it. `hook.enabled: false` turns it off; `hook.quiet: true` silences clean acknowledgements.

**Cost is part of every rule.** The per-edit pass is a new process on each edit and runs the whole text engine, or the whole static HTML engine for `.html`, cold. The immediate tier only filters what is shown; it does not skip rules. A rule change therefore carries a measured cost; the procedure is in the rules section of [CLAUDE.md](../../CLAUDE.md).

`hook-before-edit` is the older Cursor `preToolUse` gate. New Cursor manifests run `hook` on `postToolUse`, and `hooks on`, `impeccable update` and `impeccable doctor --fix` migrate an existing gate entry. The verb keeps working for projects that have not migrated.

Every observable behavior, from config merging to the audit log, is in Part 3 of [docs/CLI-CONTRACT.md](../CLI-CONTRACT.md), pinned by the `hook-*`, `hbe-*` and `hadmin-*` oracle cases.
