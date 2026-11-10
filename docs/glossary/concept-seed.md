# Concept seed

`impeccable concept-seed` is the verb that deals design directions for new work. It rolls a hand from a catalog of visual worlds, assigns one direction to lead, and prints the [mode](mode.md) rules, the [build path](build-path.md) and the presentation procedure the agent follows on the decision page. It reads a local catalog from `IMPECCABLE_CATALOG_DIR`, then calls the roll API at impeccable.style, and falls back to a degraded roll with no challengers when neither is reachable.

Not to be confused with the seed key, the random value that makes a roll repeatable. Committed files never hold the key; they hold a seed reference, a salted hash that resolves through a local ledger.

See [New work and concept seed](../features/new-work-and-concept-seed.md) and [Privacy and telemetry](../client/privacy-and-telemetry.md). The procedure is in [new-work.md](../../skill/reference/new-work.md).
