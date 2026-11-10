# Browser extension

This page states what the Impeccable browser extension does, how it runs the same rules as the CLI, and what it needs from the page. It is for anyone installing the extension, reviewing its permissions, or changing a rule that it ships.

The extension scans any open page for the detector's [anti-pattern rules](../glossary/anti-pattern-rule.md) and draws its findings over the page. It adds an Impeccable panel to DevTools, a sidebar pane in the Elements panel for the selected element, and a toolbar popup. Opening DevTools starts a scan.

## How a scan runs

| Step | Where | Piece |
|---|---|---|
| Measure the page into a snapshot | content script in the page | `detector/snapshot.js` |
| Run the rules over the snapshot | an extension page that hosts WebAssembly | `detector/core.js`, `detector/core_bg.wasm` |
| Draw the findings | content script in the page | `detector/overlay.js` |

The service worker (`extension/background/service-worker.js`) routes messages between the popup, the DevTools panel and the content script, and keeps per-tab state and the badge. Chrome hosts the rule core in an offscreen document (`extension/offscreen/offscreen.html`); Firefox loads the same document in a hidden iframe inside its background page.

## Guarantees

**Same rules as the CLI.** The rule core is `crates/wasm`, compiled from the same `crates/core` source the [detector](detector.md) and the live overlay run. `detector/antipatterns.json` is the same registry the CLI lists.

**The page's security policy does not matter.** Rules run in an extension page whose own policy allows WebAssembly (`'wasm-unsafe-eval'` in `manifest.json`), so a page that forbids WebAssembly can still be scanned. The page only ever runs the snapshot and overlay scripts.

**It never runs the engine binary.** The extension carries its own WebAssembly core, so its release does not wait on an engine release.

**The detector pieces are generated, never tracked.** `extension/detector/` is gitignored. `bun run build:extension` runs `cargo xtask bundle` to write the five pieces, checks that every path the manifest and service worker name exists, and packages `extension.zip` for the Chrome Web Store and `extension-firefox.zip` for Firefox add-ons, deriving the Firefox manifest at build time (`scripts/build-extension.js`). Building it needs a Rust toolchain and `wasm-pack`.

**Permissions are fixed in the manifest.** `activeTab`, `scripting`, `storage`, `webNavigation` and `offscreen`, with host access to all URLs so a scan can inject into the inspected page.

The bundling step and its staleness check are described in the browser bundle section of [docs/ENGINE.md](../ENGINE.md). `tests/extension-build.test.mjs`, `tests/extension-devtools.test.mjs`, `tests/extension-popup.test.mjs` and `tests/extension-report.test.mjs` cover the build and the UI; the store copy is [`extension/STORE_LISTING.md`](../../extension/STORE_LISTING.md).
