# Launcher

The launcher is `skill/scripts/impeccable`, a POSIX `sh` script shipped inside the skill (`impeccable.cmd` is its Windows twin). It runs the first [engine](engine.md) it finds in this order: `$IMPECCABLE_BIN`, the binary under the skill's `scripts/bin/<os>-<arch>/`, `~/.impeccable/bin/impeccable`, the version-pinned cache under `~/.impeccable/bin/<version>/`, and `impeccable` on `PATH`. With none usable, it downloads the version named in `scripts/VERSION` into that cache and checks it against `engine-checksums.txt`.

Not to be confused with the engine itself. The launcher holds no design logic; it only resolves and execs a binary.

See [Engine and launcher](../features/engine-and-launcher.md) and [Update and troubleshoot](../client/update-and-troubleshoot.md). The resolution order is commented at the top of [the launcher](../../skill/scripts/impeccable).
