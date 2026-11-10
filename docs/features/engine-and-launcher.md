# Engine and launcher

This page states how a skill command reaches the native binary and what that path guarantees about versions, verification and offline use. It is for anyone installing, packaging or debugging Impeccable.

Every command the skill text runs is `<scripts>/impeccable <verb>`. That file is the [launcher](../glossary/launcher.md), a POSIX `sh` script (`skill/scripts/impeccable`) with a Windows twin (`impeccable.cmd`) for shells without `sh`. The launcher finds one static [engine](../glossary/engine.md) binary and execs it. No Node or other runtime is needed at run time. The architecture map is [docs/ENGINE.md](../ENGINE.md); the behavior of every verb is [docs/CLI-CONTRACT.md](../CLI-CONTRACT.md).

## Resolution order

The launcher tries these in order and execs the first that qualifies:

| Candidate | Condition |
|---|---|
| `$IMPECCABLE_BIN` | executable; used without any probe |
| `scripts/bin/<os>-<arch>/impeccable` next to the launcher | its `engine-probe` reports the version in `scripts/VERSION`; otherwise skipped with a warning |
| `~/.impeccable/bin/impeccable` | answers `engine-probe` |
| `<cache>/bin/<VERSION>/impeccable` | its SHA-256 matches `scripts/engine-checksums.txt` |
| `impeccable` on `PATH` | answers `engine-probe` |
| download into the version cache | the checksum manifest has an entry for this version and target |

`<cache>` is `$IMPECCABLE_HOME`, default `~/.impeccable`. The probe exists because the retired 3.x npm CLI also installed a binary named `impeccable`, and exec'ing it would fail every verb.

## Guarantees

**One version pin.** `ENGINE_VERSION` at the repo root names the engine release. The build copies it to `skill/scripts/VERSION`, which names the cache directory and the download. The npm shim reads the same version from the `@impeccable/cli-<os>-<arch>` pins in `package.json` `optionalDependencies`.

**Downloads fail closed.** A cached or downloaded binary is verified against `skill/scripts/engine-checksums.txt`, never against a sidecar published beside it. With no checksum for this version and target, the launcher refuses before any network traffic. A `<binary>.verified` stamp holding the digest and size skips rehashing until either changes, because hooks run the launcher on every edit. `tests/launcher-download.test.mjs` covers this path.

**The binary knows its home.** Before any exec, the launcher exports `IMPECCABLE_SKILL_DIR` (the skill directory, for `reference/*.md` and `command-metadata.json`) and `IMPECCABLE_SELF` (how the binary spells itself in commands it prints). Other variables the binary reads are listed in [docs/RUNTIME-ENV.md](../RUNTIME-ENV.md).

**Binaries are never tracked.** `skill/scripts/bin/` is gitignored, so installed skill copies ship launcher-only and fetch the engine on first run. A self-contained release zip is an opt-in build step for maintainers.

**The npm shim takes the same path.** `cli/bin/cli.js` tries `$IMPECCABLE_BIN`, the platform optional dependency, the version cache, then a checksum-verified download, and execs the binary with argv. `tests/cli-shim.test.mjs` covers it.

**Behavior is pinned.** The oracle under `tests/oracle/` replays recorded [goldens](../glossary/golden.md) against the binary ([oracle](../glossary/oracle.md)); see [`tests/oracle/README.md`](../../tests/oracle/README.md).

## What the binary contains

The engine is this repository's Cargo workspace, Apache-2.0, built offline on stable Rust with `cargo build --release -p impeccable`. The rule logic lives in `crates/core`; `crates/wasm` compiles the same source for the [extension](extension.md) and the live overlay. Verb names are the old script basenames, with `signals` and `hooks` as aliases. A scan verb run by a signed-in, entitled user may hand off to a separately signed Pro build; anything missing or failing runs the open engine unchanged. That hand-off is described under Pro dispatch in [docs/RUNTIME-ENV.md](../RUNTIME-ENV.md).
