# Oracle

The oracle is the behavior gate for the [engine](engine.md): a corpus of cases under `tests/oracle/cases/`, each a verb with arguments, a staged workspace and stdin, replayed against the binary by `tests/oracle/run.mjs`. It captures stdout, stderr, exit code and named files, normalizes machine-specific paths and timestamps, and compares them with the recorded [goldens](golden.md). Byte-equal is the bar. `bun run test` runs it and skips cleanly when no binary is found.

Not to be confused with a golden. The oracle is the harness of cases and runner; a golden is one recorded expected result. Reviewed exceptions live in `tests/oracle/DELTAS.md`.

See [Testing](../developer/testing.md). Full detail is in the [oracle README](../../tests/oracle/README.md).
