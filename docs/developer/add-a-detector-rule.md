# Add a detector rule

The order of work for adding or changing an [anti-pattern rule](../glossary/anti-pattern-rule.md) in the detector, and what to measure before you open the pull request. For contributors changing rule logic in `crates/`. [Work on the engine](engine-development.md) covers the general build and test loop; [ENGINE.md](../ENGINE.md) maps the crates.

## Where a rule lives

| Path | What it holds |
|---|---|
| `tests/fixtures/antipatterns/<rule-id>.html` | The hand-written fixture: a should-flag column and a should-pass column, unique headings, explicit pixel dimensions. |
| `crates/foundation/src/registry.rs` | The rule registry row: id, category, name, description, and optional scopes and severity. |
| `crates/core` | The check itself, plus the browser adapters over the `Dom` trait and the visual-contrast decisions. |
| `crates/html`, `crates/browser`, `crates/detect` | The engines that call the checks: static HTML, URL through Chrome, and the file walker and text engine. |
| `tests/oracle/cases/detect.mjs` and `tests/oracle/golden/` | The [oracle](../glossary/oracle.md) cases and their recorded [goldens](../glossary/golden.md). |
| `crates/live/assets/detect-antipatterns-browser.js`, `crates/live/assets/antipatterns.json` | The two tracked generated assets: the in-page bundle and the registry as JSON. |
| [CLI-CONTRACT.md](../CLI-CONTRACT.md) | The observable contract of `impeccable detect`, edited by hand. |

## Order of work

1. Write the fixture first, with flag cases and the false-positive shapes you expect.
2. Add the registry row in `crates/foundation/src/registry.rs`.
3. Add failing Rust coverage, then the check in `crates/core`, until it passes against the fixture. Cover each engine path the rule runs in; the native and browser adapters can disagree.
4. Add or update an oracle case in `tests/oracle/cases/detect.mjs`, record it with `node tests/oracle/record.mjs --bin detect-`, and read the golden before committing it.
5. Run `cargo xtask bundle` and commit both files in `crates/live/assets/`. `cargo xtask bundle --check` and CI's registry diff both fail on a stale `antipatterns.json`.
6. Rebuild the binary, then validate:

   ```bash
   cargo build --release -p impeccable
   bun run build
   IMPECCABLE_BIN="$PWD/target/release/impeccable" bun run test
   ```

   `bun run build` checks rule counts quoted in `README.md` and `README.npm.md` against `antipatterns.json`.
7. If the rule carries new design guidance, update `skill/SKILL.src.md` or the relevant `skill/reference/*.md`.

## Measure the hook cost

The [design hook](../glossary/design-hook.md) starts a new process on every Edit or Write and runs the whole text engine, or the static HTML engine for `.html`, cold. `IMMEDIATE_TIER_RULES` only filters which findings the hook shows; it does not skip rules. Every text or static-HTML rule is paid on every edit, regex compilation and lazy tables included.

Measure on `main` and on your branch, and put both numbers in the pull request:

```bash
cargo run --release -p impeccable-html --example rule_cost -- --runs 9 tests/fixtures/antipatterns <real project source>
```

It writes `rule-cost.json` with mean, p50 and p95 milliseconds per profiled unit. Prefer detection that touches less: one regex over many, a check gated on a tag or property over a walk of every element, work that rides an existing pass over a new one. A check that needs layout or pixels belongs in the browser engine, not the hook.

## Rules from outside this repo

A downstream crate adds rules through a [rule pack](../glossary/rule-pack.md) without forking. The shipped `impeccable` binary installs no pack, and nothing in this repository should start doing so. The contract is in [rule packs](../features/rule-packs.md) and [ENGINE.md](../ENGINE.md).
