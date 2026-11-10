# Build path

The build path is how a new surface goes from chosen direction to code: **comp-led** (a [comp](comp.md) is generated and approved first, and the build is measured against it) or **code-led** (built directly, with the ambition carried by a written direction contract). Operate and Read surfaces are always code-led. For Persuade and Experience surfaces the project's `buildPath` in `.impeccable/config.json` sets the default; without one, comp-led is the default when image generation is available. `impeccable concept-seed` prints the resolved path for each surface.

Not to be confused with the repository's own build (`bun run build`), or with `impeccable build-phase`, the verb that tracks the gated phases inside a comp-led build.

See [Comp-led build](../features/comp-led-build.md). The rules are in [new-work.md](../../skill/reference/new-work.md).
