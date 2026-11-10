# Golden

A golden is the recorded expected output of one [oracle](oracle.md) case, stored as a JSON file under `tests/oracle/golden/`. The original goldens were recorded from the JavaScript scripts before they left the tree; new ones are recorded from the [engine](engine.md) with `node tests/oracle/record.mjs --bin <prefix>` and reviewed by hand, because a bug in the binary would otherwise be frozen into the contract.

Not to be confused with the oracle, which runs the cases and compares output against goldens. Also distinct from the frozen function-level vectors under `tests/oracle/vectors/`, which cannot be regenerated.

See [Testing](../developer/testing.md). Recording and review steps are in the [oracle README](../../tests/oracle/README.md); accepted differences are in [DELTAS.md](../../tests/oracle/DELTAS.md).
