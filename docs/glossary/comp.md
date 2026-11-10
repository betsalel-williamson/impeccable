# Comp

A comp is a generated high-fidelity image of the requested surface, saved under `.impeccable/mocks/`, that sets the composition before any code exists. On a comp-led [build path](build-path.md) the user approves one comp, and the build is then measured against it: `impeccable comp-spec` turns it into region boxes and palettes, and `impeccable comp-diff` scores the built page region by region.

Not to be confused with component review. The `component-review` verb runs the plan and asset review, a checkpoint inside a comp-led build where the user checks the raster plates and which regions code will draw. It reviews assets cut from the approved comp; it does not review UI components.

See [Comp-led build](../features/comp-led-build.md). Measurement detail is in [COMP-FIDELITY.md](../COMP-FIDELITY.md).
