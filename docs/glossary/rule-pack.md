# Rule pack

A rule pack is a set of extra [anti-pattern rules](anti-pattern-rule.md) that a downstream Rust crate adds without forking the workspace. It implements the `RulePack` trait (and `StaticRulePack` for the static HTML engine), installs itself once at startup and is passed to each engine it should run in. Pack rules run after the built-in rules, and their findings pass through the same ignores and filters.

Not to be confused with the built-in rules, which are compiled in and always run. The shipped `impeccable` binary installs no pack, so its output is the built-in set alone.

See [Rule packs](../features/rule-packs.md). The full contract is in [ENGINE.md](../ENGINE.md) and the trait's own documentation in `crates/foundation/src/rule_pack.rs`.
