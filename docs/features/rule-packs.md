# Rule packs

This page states how a crate outside this repository adds detector rules without forking it, and what the engine guarantees about built-in output when it does. It is for authors of downstream rule sets and anyone reviewing a change to the rule engine's extension points.

A [rule pack](../glossary/rule-pack.md) is one process-lifetime value that carries its own registry rows plus checks for the engine hooks it covers. The built-in [anti-pattern rules](../glossary/anti-pattern-rule.md) are compiled in and always run; a pack only adds. The shipped `impeccable` binary installs no pack.

## The hooks

| Trait | Hook | Engine |
|---|---|---|
| `impeccable_core::rule_pack::RulePack` | `check_text` | text engine |
| | `check_element_dom`, `check_page_dom` | browser and snapshot engines |
| | `registry` | the pack's rule rows |
| `impeccable_html::StaticRulePack` | `check_document` | static HTML engine |

Each `RulePack` check defaults to empty. The static hook is a separate trait because the static document model belongs to `crates/html`. A pack that covers HTML implements both.

## Guarantees

**Built-in output is unchanged.** Every pack hook runs after the built-in checks and before inline ignores, so with no pack installed the output is byte-identical; the oracle replays the built-in goldens to enforce it. Because packs run before inline ignores, `impeccable-disable` waives a pack's rule exactly as it waives a built-in one.

**A pack can never shadow a built-in rule.** `registry::extend` appends a pack's rows after `ANTIPATTERNS`, is idempotent per slice, and panics on an id collision (`crates/foundation/src/registry.rs`). Pack ids are namespaced, such as `mypack/my-rule`.

**Installation names the findings.** `impeccable_core::rule_pack::install(&PACK)` at startup registers the rows, which is what gives a pack's findings a name, description, category and severity. Registration is process-wide and has no undo.

**One pass per HTML file.** A file gets the static hook when the pack supplies one, otherwise the pack's text hook over the raw HTML. A pack that implements both never reports the same file twice.

**A pack reaches every engine through options.** The reference travels in `TextOptions` or `ScanOptions` (text), `DetectHtmlOptions` or `StaticHtmlEngine` (static HTML), and `BrowserConfig` (browser; never serialized from the page).

**A downstream CLI needs no fork.** The `impeccable` package exports a library router, `run(args, io, pack)`; the open binary passes `InstalledPack::NONE`. A workspace that depends on this repository's `impeccable` and `impeccable-common` packages at one Git tag can build its own binary with a pack set, and the router supplies the pack to file, static HTML, URL and hook scans.

**Hosts without a process get the same engines.** `crates/wasm` built with `--features detect` exports `detect_text_json` and `detect_html_source_json`, JSON in and the `detect --json` findings out. A pack reaches them through Rust-only setters in a crate that links `impeccable-wasm`; there is no JavaScript-facing setter.

`crates/core/tests/rule_pack.rs` installs a test pack and checks the hooks. The trait signatures, where each hook runs inside its engine, and how to reuse the browser bundler for a pack's own wasm module are in the Rule packs section of [docs/ENGINE.md](../ENGINE.md).
