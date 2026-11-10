# Live mode

This page states what [live mode](../glossary/live-mode.md) does in the browser and in the user's source, and what it guarantees about paths, frameworks and cleanup. It is for anyone using `live` or `generate`, or reasoning about what they leave behind.

In live mode the user picks an element in their running dev page, chooses an action, and cycles through real variants the agent writes. Accepting one keeps it in source; discarding restores the original. `generate` runs the same loop on a named element without manual picking. The reasoning behind the design is recorded in the [live variant mode decision record](../adr-live-variant-mode.md); the verb-level contract is Part 4 of [docs/CLI-CONTRACT.md](../CLI-CONTRACT.md).

## The three parties

| Party | Role |
|---|---|
| Browser | the injected page script (`skill/scripts/live-browser*.js`) picks elements, posts events, cycles variants |
| Server | `impeccable live-server`, a local HTTP helper that relays events over SSE and serves the detector overlay as `/detect.js` |
| Agent | long-polls with `impeccable live-poll`, writes variants with `impeccable live-wrap`, replies with `--reply` |

The server is part of the engine (`crates/live`) and is built on the shared HTTP core in `crates/common`. The agent's side is a shell command, so any harness that can run one can drive live mode.

## Guarantees

**Variants are source, not DOM patches.** The agent writes variants into the real file inside a `display: contents` wrapper, so layout relationships hold and the framework's own hot reload shows them. Accept leaves the chosen variant as ordinary code the user can diff.

**Paths come from one manifest.** `impeccable live` resolves the app root, repo root and context root once at boot and writes `.impeccable/live/roots.json` (`crates/live/src/manifests.rs`). Every later live verb re-anchors on that file and changes into the app root, so ambient working directory never decides a path.

**Svelte previews live where SvelteKit can serve them.** Svelte and SvelteKit variants are compiled components under `node_modules/.impeccable-live/`, because SvelteKit's dev server refuses files under `.impeccable/`. Each publish gets a new revision directory (`r<N>/`), so stale modules never shadow fresh ones (`crates/live/src/svelte_component.rs`). The compiler comes from the user's own `node_modules`.

**The browser restores from the server.** On connecting, the page rebuilds its state from the server's `activeSessions` snapshot; browser storage is not the source of truth. Sessions are journaled under `.impeccable/live/sessions/`.

**Completion refuses a dirty source.** `impeccable live-complete` refuses with `source_dirty` while the session's source file still carries live artifacts, such as a leftover variant wrapper, unless `--force` is passed.

**Live state stays out of git.** Live writes a marker-delimited ignore block for its runtime files, `roots.json` and the session journals included, into `.git/info/exclude` (or `.gitignore` outside git).

**It is web-only.** Live mode needs a browser page and HTML rules, so the routing reference does not lead with it on `ios`, `android` or `adaptive` projects ([Platforms](platforms.md)). The [design hook](design-hook.md) also skips files that hold a live wrapper.

## How it is checked

The `live-*` oracle cases pin each verb's output. `tests/live-e2e.test.mjs` drives the whole flow (pick, generate, cycle, accept, cleanup) against framework fixtures under `tests/framework-fixtures/` with real dev servers; how to run it is in [`tests/framework-fixtures/README.md`](../../tests/framework-fixtures/README.md). Every test that starts a live server arms a reaper so no server outlives its test process, pinned by `tests/live-server-leak.test.mjs`.
