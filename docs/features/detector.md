# The detector

This page states what `impeccable detect` scans, what it reports and which exit codes and filters it guarantees. It is for anyone running the detector by hand, in CI, or from another tool.

The detector runs deterministic [anti-pattern rules](../glossary/anti-pattern-rule.md): no model and no API key. The registry holds 62 rules in two categories, `slop` (tells of generated UI) and `quality` (general design and accessibility problems). The published list is `crates/live/assets/antipatterns.json`; `impeccable detect --list-rules` prints it.

## Engines

| Input | Engine | What runs |
|---|---|---|
| Source files (JSX, TSX, CSS, Vue, Svelte, Astro and similar) | text engine (`crates/detect`) | pattern rules over the raw text |
| `.html` files | static HTML engine (`crates/html`) | a parser and cascade, then element and page rules |
| A URL, or a running app through `--cdp` | browser engine (`crates/browser`) | every rule, including those that need layout or pixels |

Rules that need a rendered page, such as low contrast or tiny text, run only on a URL or attached page. The checks themselves live in `crates/core`; each engine calls the same functions, which is what keeps the [extension](extension.md) and the live overlay consistent with the CLI.

## Guarantees

**Exit codes.** `0` means the scan completed with no primary findings. `2` means it completed with at least one primary finding. `1` means at least one requested target could not be scanned, and it wins over `2` on a partial multi-target scan (`crates/detect/src/cli.rs`).

**Advisory findings never fail a run.** Some rules are advisory: listed in their own section, never counted, never changing the exit code. `--no-advisory` hides them.

**Output streams are fixed.** Human-readable findings go to stderr. `--json` writes JSON to stdout, `--github` writes GitHub Actions annotations to stdout, and the two cannot be combined.

**Project config applies by default.** `.impeccable/config.json` and its per-developer twin `config.local.json` supply `detector.ignoreRules`, `detector.ignoreFiles`, `detector.ignoreValues`, `detector.designSystem.enabled` and `detector.extensions`. `--no-config` ignores all of it, inline ignores and `DESIGN.md` included. `impeccable ignores` edits the same lists.

**Inline waivers travel with a file.** `impeccable-disable <rule>` waives a rule for the whole file; `impeccable-disable-line` and `impeccable-disable-next-line` waive one line. They work in any comment syntax and are honored unless `--no-inline-ignores` or `--no-config` is passed.

**`DESIGN.md` adds drift rules.** With a project design system loaded, the detector also flags fonts, colors, radii and font sizes outside it. `--no-design-system` turns that off.

**Scans are reproducible.** The oracle replays more than 600 recorded `detect-*` cases against the binary (`tests/oracle/golden/`), and each rule has a two-column should-flag and should-pass fixture in `tests/fixtures/antipatterns/<rule-id>.html`.

The full flag table, error strings and URL-scan behavior (consent banners, tours, viewport) are in Part 1 of [docs/CLI-CONTRACT.md](../CLI-CONTRACT.md). The engine layout is in [docs/ENGINE.md](../ENGINE.md).

## Scope

The detector reads web markup and styles. Skill routing does not run it on native projects; see [Platforms](platforms.md). The same rules run after edits through the [design hook](design-hook.md), and third parties can add rules through [rule packs](rule-packs.md).
