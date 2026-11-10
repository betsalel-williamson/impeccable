# Engine

The engine is the single native binary, `impeccable`, behind every verb the skill runs (`context`, `detect`, `hook`, `concept-seed`, the `live*` verbs and the rest). It is built from this repository's Cargo workspace under `crates/`, needs no Node at runtime, and is released on its own as `engine-v<version>`, pinned by the `ENGINE_VERSION` file at the repository root.

Not to be confused with the [launcher](launcher.md), the small shell script that finds or downloads the engine and execs it. Also distinct from the npm package `impeccable`, which is a thin shim that locates the same binary.

See [Engine and launcher](../features/engine-and-launcher.md). Contributors start with [ENGINE.md](../ENGINE.md); the observable behavior of every verb is in [CLI-CONTRACT.md](../CLI-CONTRACT.md).
