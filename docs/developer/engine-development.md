# Work on the engine

How to change the Rust [engine](../glossary/engine.md) in `crates/` and prove the change. For contributors touching verb behavior, the rule engine, the live server or the wasm build. Read [ENGINE.md](../ENGINE.md) first: it maps every crate and the browser-bundle flow.

## What the engine is

Every verb the skill runs goes through the [launcher](../glossary/launcher.md) `skill/scripts/impeccable`, which execs one static binary built from this workspace. The binary's observable behavior (arguments, environment, stdout and stderr, exit codes, files written) is specified in [CLI-CONTRACT.md](../CLI-CONTRACT.md) and pinned by the [oracle](../glossary/oracle.md). The workspace builds offline on the stable channel; `rust-toolchain.toml` adds the `wasm32-unknown-unknown` target, which rustup installs on the first `cargo` call. [RUNTIME-ENV.md](../RUNTIME-ENV.md) lists the environment the binary reads.

What stays JavaScript: the in-page live-mode files `skill/scripts/live-browser*.js` and `modern-screenshot.umd.js`, the page code in `browser-bundle/`, the build and test tooling, the extension shell and the npm shim.

## The loop

1. Add a failing regression in the affected crate.
2. Run it and its neighbors while you work:

   ```bash
   cargo test -p <crate> <filter>
   bun run test:rust-changed
   ```

3. Build the binary and replay the default suite against it, so the oracle checks your source and not a downloaded release:

   ```bash
   cargo build --release -p impeccable
   IMPECCABLE_BIN="$PWD/target/release/impeccable" bun run test
   ```

4. When the contract grows, add an oracle case under `tests/oracle/cases/`, record it with `node tests/oracle/record.mjs --bin <prefix>`, and read the new golden before you commit it. An intended change to existing behavior goes in [DELTAS.md](../../tests/oracle/DELTAS.md) with its reason.

`cargo test --workspace` is slow; the `impeccable-browser` tests drive a real Chrome. Start long cargo runs in the background and wait for them to exit; do not poll with `sleep` loops or `pgrep -f`. CI's `rust` and `rust-windows` jobs run the whole workspace whenever a change touches Rust.

## Clean up after yourself

An agent that creates its own worktree or `CARGO_TARGET_DIR` deletes that target directory when it finishes and runs `git worktree remove`. A stale target directory holds many gigabytes.

## Browser bundle and wasm

`crates/wasm` compiles the rule logic for the extension, the live overlay and the site. `cargo xtask bundle` rebuilds the in-page bundle and the registry JSON in `crates/live/assets/`, and `bun run build:extension` runs it to produce `extension/detector/`. `cargo xtask bundle --check` fails when either tracked asset is stale. The extension build needs `wasm-pack` installed.

## Before a release can ship it

The binary users download is built from the `engine-v<ENGINE_VERSION>` tag, so engine source that changed since that tag blocks a skill or CLI release until the engine is released again. [Releases](releases.md) covers the order.

Conventions for porting and for byte-equal output (JS number formatting, JSON field order, exit codes only in the `cli` crate, no panics on user input, no I/O in `core`) are in [PORTING-GUIDE.md](../PORTING-GUIDE.md). Rule-specific steps are in [add a detector rule](add-a-detector-rule.md).
