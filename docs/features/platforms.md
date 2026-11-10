# Platforms

This page states how Impeccable decides a project's delivery target and what each target loads or skips. It is for anyone reading `PRODUCT.md`, the `context` output, or native-specific skill text.

[Platform](../glossary/platform.md) answers "what ships, and whose conventions apply". It is independent of [mode](modes.md).

| Value | Meaning | Native reference inlined by `impeccable context` |
|---|---|---|
| `web` | a website or web app, responsive mobile web included | none |
| `ios` | a native iOS or iPadOS app | `skill/reference/ios.md` |
| `android` | a native Android app | `skill/reference/android.md` |
| `adaptive` | one codebase that adapts its look per OS | both |

A Flutter or React Native app that uses one look on both systems is not `adaptive`; it takes that single platform's value.

## How the value is read

`PRODUCT.md` carries a `## Platform` section with a bare value. `extract_platform` in `crates/context/src/context.rs` parses it:

- A missing or empty section means `web`, so older projects are unaffected.
- `web`, `ios`, `android` and `adaptive` are taken as written, in any case.
- A line that names both `ios` and `android` and nothing else (`ios, android`, `iOS + Android`) reads as `adaptive`.
- Any other value falls back to `web`, and `impeccable context` prints a `WARNING` directive naming the bad value (`crates/context/src/context_cli.rs`). A toolchain name or a typo never silently gets web guidance.

Oracle cases `context-native-ios`, `context-android`, `context-adaptive`, `context-adaptive-word`, `context-bad-platform` and `context-empty-platform-section` pin these outcomes.

## What a native value changes

**References load without a second read.** `impeccable context` inlines the native reference files into its own output, so platform conventions land in the agent's context in the same call.

**Native command variants replace web ones.** Where a command's native guidance diverges too far to share a file, the Commands table lists `reference/<command>.native.md`, which the router loads instead of the web file. The variants are `audit.native.md` and `adapt.native.md`.

**Web-only tools stand down.** The routing reference `skill/reference/routing.md` tells the agent not to lead with `live`, `generate` or `impeccable detect` on a native project. The [design hook](design-hook.md) skips its scan when `PRODUCT.md` declares a native platform, because a React Native project is made of the `.tsx`, `.ts` and `.js` files the hook watches. Both `hook` and `hook-before-edit` check this (`is_native_platform` in `crates/hook/src/hook_lib.rs`), pinned by `hook-native-platform-skip` and `hbe-native-platform`.

## Who records it

The `init` command confirms an ambiguous platform during its product interview and writes the bare value. The staleness check also compares a `web` or missing value with native evidence on disk (a `pubspec.yaml`, an `ios/Podfile`, a `react-native` dependency) in `check_native_platform_evidence` (`crates/context/src/staleness.rs`); see [Artifact staleness](artifact-staleness.md).

The native references are distilled from MIT-licensed upstream material; attribution is in [`NOTICE.md`](../../NOTICE.md).
