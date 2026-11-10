# Run the tests

How to run the default test suite, get it an engine binary, and narrow a run while you iterate. For contributors validating a change before they open a pull request. Suites that cost money or need a browser are in [opt-in suites](opt-in-suites.md).

## The default suite

```bash
bun run test
```

`scripts/run-tests.mjs` runs six suites from `scripts/test-suites.mjs`: `core` (build, transformers, validators, prose gates), `oracle` (golden replay against the engine), `detector` (extension packaging), `live` (live-mode reference and helper checks), `framework` (framework fixtures through the engine) and `plugin-e2e` (installs `plugin/` into a sandboxed Claude Code). Bun runs the unit files; Node's `--test` runs everything that spawns the engine binary. `node scripts/run-tests.mjs --list` prints every suite and its files, and each suite has its own script, such as `bun run test:core` or `bun run test:oracle`.

Each suite command runs as its own process group under a wall-clock cap (`IMPECCABLE_TEST_WALL_CLOCK_MS`, or a suite's `wallClockMs`), so a wedged run ends instead of hanging. After each suite the runner checks for leaked live servers; see [live server hygiene](live-server-hygiene.md).

## Give the tests a binary

Suites that need the [engine](../glossary/engine.md) resolve it through `tests/lib/engine-bin.mjs` in this order: `IMPECCABLE_BIN`, then `skill/scripts/bin/<os>-<arch>/` (written by `bun run fetch:engine`), then `target/release/impeccable`. Without one, those suites skip rather than fail, so a green run on a machine with no binary proves less than it looks.

To test the engine source in your checkout rather than the last release:

```bash
cargo build --release -p impeccable
IMPECCABLE_BIN="$PWD/target/release/impeccable" bun run test
```

`engineEnv` in the same file sets `IMPECCABLE_TELEMETRY_CHANNEL=test` for the spawned binary, so anything a test sends to the roll API is tagged as test traffic. Keep that for any new test that can reach the API.

## The oracle

The [oracle](../glossary/oracle.md) replays recorded cases against the binary and compares stdout, stderr, exit code and named files with each [golden](../glossary/golden.md). It is the gate for observable verb behavior described in [CLI-CONTRACT.md](../CLI-CONTRACT.md). New cases are recorded from the binary with `node tests/oracle/record.mjs --bin <prefix>`, and every golden change is reviewed by hand. Never rerecord a golden to make a regression pass. `tests/oracle/vectors/calls/` is a frozen snapshot that cannot be regenerated. [tests/oracle/README.md](../../tests/oracle/README.md) and [DELTAS.md](../../tests/oracle/DELTAS.md) carry the details.

## Narrow a run

```bash
bun test tests/build.test.js             # one Bun file
node --test tests/oracle.test.mjs        # one Node file
cargo test -p <crate> <filter>           # one crate's Rust tests
bun run test:rust-changed                # crates your change reaches, plus their dependents
```

`bun run test:rust-changed` diffs against the merge base with `origin/main`, working tree included. `--print` shows the crate selection and `--base <ref>` changes the base; the full log goes to `test-rust-changed.log` in the cargo target directory. CI runs `cargo test --workspace` on every PR that touches Rust.

## Where tests go

Name files `*.test.js` or `*.test.mjs` and keep fixtures near the behavior they cover, usually under `tests/fixtures/`. Every test file under `tests/` must be listed in a suite in `scripts/test-suites.mjs`; `tests/test-suites.test.mjs` fails on one that is not. Each suite selects itself in CI when one of its own files changes.
