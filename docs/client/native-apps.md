# Native apps

This page shows how to point Impeccable at a native iOS or Android app instead of a website, and which parts of the product change when you do. It is for teams building with SwiftUI, Jetpack Compose, React Native, Flutter, or similar.

## Set the platform

Impeccable reads the delivery target from the `## Platform` section of [PRODUCT.md](../glossary/product-md.md). The value is one bare word:

| Value | Use it for | Design guidance loaded |
|---|---|---|
| `web` | Websites and web apps, including responsive mobile web | The general rules only |
| `ios` | A native iOS or iPadOS app | Apple Human Interface Guidelines, distilled |
| `android` | A native Android app | Material Design 3, distilled |
| `adaptive` | One cross-platform codebase that follows each OS's own conventions | Both |

`init` forms a guess from your build files and asks when it cannot tell, then records the answer. You can also edit the section by hand. A missing section means `web`, so projects set up before the field existed keep working unchanged. A line that names both targets, such as `ios, android`, reads as `adaptive`.

Name the design language the app renders, not the toolchain. A Flutter or React Native app that uses one look on both platforms (Material everywhere is Flutter's default) is `android` or `ios`, not `adaptive`. A website wrapped in a native shell is still `web`. An unrecognized value falls back to `web`, and the session prints a warning naming the bad value so you can fix it.

See [platform](../glossary/platform.md) for how this axis relates to a surface's [mode](../glossary/mode.md).

## What changes

The design commands (`polish`, `bolder`, `typeset`, `layout` and the rest) work from the platform's conventions instead of web ones. The guidance is loaded at the start of every session, so you do not have to ask for it.

Two commands switch to native versions:

- `audit` checks the app against native expectations and reads SwiftUI, Compose, React Native or Flutter source directly.
- `adapt` covers device classes, orientation and platform conventions rather than breakpoints.

## What stays web-only

Three parts of Impeccable work on HTML and CSS in a browser, so they do not apply to native code:

- **Live mode and `generate`.** On a native project, `generate` declines and suggests running `bolder` or `quieter` on the source instead.
- **The detector.** The context-aware menu does not run it, and `npx impeccable detect` has no native rules.
- **The design hook.** It skips its scan when PRODUCT.md declares a native platform, even though a React Native project is full of the `.tsx` and `.js` files the hook watches.

## Monorepos with a native app

In a repository that holds both a website and a native app, give the native app its own PRODUCT.md in its folder. One inherited record cannot hold two platforms, and an app that inherits a `web` record from the root gets web guidance. `/impeccable doctor` flags a workspace that carries native build files while inheriting a web record.
