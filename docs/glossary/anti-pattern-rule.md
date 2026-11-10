# Anti-pattern rule

An anti-pattern rule is one detector check, with a stable id, a name, a category and a description, registered in `crates/foundation/src/registry.rs` and implemented in `crates/core`. Rules flag mechanical design problems such as gradient text, low contrast or overflowing content. `impeccable detect` runs them over files, directories or URLs; the [design hook](design-hook.md), [live mode](live-mode.md) and the browser extension run the same rules.

Not to be confused with the design guidance in the skill text, which shapes what the agent builds and is never scanned for. A [rule pack](rule-pack.md) adds rules beyond the built-in set.

See [Detector](../features/detector.md), [Run the detector](../client/run-the-detector.md) and [Add a detector rule](../developer/add-a-detector-rule.md). The `detect` contract is in [CLI-CONTRACT.md](../CLI-CONTRACT.md).
