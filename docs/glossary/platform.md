# Platform

Platform is the delivery target recorded as a bare value under `## Platform` in [PRODUCT.md](product-md.md): `web`, `ios`, `android` or `adaptive` (one codebase that follows each OS's own conventions). A missing value means `web`. A line naming both `ios` and `android` reads as `adaptive`. For a native value, `impeccable context` inlines `reference/ios.md`, `reference/android.md`, or both for `adaptive`.

Not to be confused with [mode](mode.md). Mode is per surface and says what the visitor came to do; platform is per project and says which native conventions apply. A Flutter or React Native app that uses one look on both OSes is not `adaptive`; it takes that single platform's value.

See [Platforms](../features/platforms.md) and [Native apps](../client/native-apps.md).
