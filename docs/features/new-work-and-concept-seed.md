# New work and concept-seed

This page states what happens before a new surface or a replacement visual world is coded, and what `impeccable concept-seed` and the decision page guarantee. It is for anyone reading the new-work flow or the engine output it depends on.

The playbook is [`skill/reference/new-work.md`](../../skill/reference/new-work.md). It applies to a new surface or a redesign that replaces the world; a refinement of existing code skips it. `PRODUCT.md` owns product truth, `DESIGN.md` owns durable visual decisions, and a [surface brief](../glossary/surface-brief.md) keeps what belongs to one route.

## The roll

On a new or replacement world, the agent runs [concept-seed](../glossary/concept-seed.md) before writing any artifact code:

```bash
impeccable concept-seed --scope direction --mode <mode>
```

`--scope direction` assigns a direction for a new world and deals catalog challengers. `--scope surface` works inside a settled world and deals three of the agent's own structures for one surface. `--mode` is the surface's [mode](modes.md). The roll exists so every run does not converge on the category default.

The only exception is a direction the user or brief pins whole, naming both its world and its first-surface experience in quotable words. Anything less rolls.

## What the output carries

| Block | Guarantee |
|---|---|
| assignment and challengers | the dealt direction, plus catalog worlds the agent must weigh on audience identification and product clarity |
| MODE RULES | the mode file's `## Directions` and `## Comps`, verbatim ([Modes](modes.md)) |
| SEED REFERENCE | a salted reference to the seed key; the only form committed files may record |
| PRESENTATION | the decision round condensed, with the resolved [build path](../glossary/build-path.md) for this surface |

The PRESENTATION block and the MODE RULES block are printed by the engine (`presentation_block` and `mode_rules_block` in `crates/context/src/concept_seed.rs`) because a printed engine line is followed where an extra file read gets skipped.

## Where the catalog comes from

`concept-seed` has no catalog in this repository. It reads a local catalog (`concept-ingredients.json` and its companions) from `IMPECCABLE_CATALOG_DIR` when set, or from the skill's `scripts/` directory, where a shipped skill has none; otherwise it asks the roll API at impeccable.style. When neither answers, it deals a degraded roll: one text-only card with re-roll. Oracle cases run against `tests/fixtures/concept-catalog/`. The API timeout is `IMPECCABLE_API_TIMEOUT`, default 4000 ms.

## The decision page

`impeccable serve-question --start --payload <file>` serves a local page with the dealt options as cards of equal anatomy, plus re-roll, steer and the category-standard canon card. Guarantees:

- Every option card declares its `kind` (`assigned`, `pick`, `challenger`, `grounded` or `dealt`), and each challenger carries its `catalogId`. `--start` and `--update` refuse a payload without them.
- A re-roll keeps the same server and page open; the agent delivers the next hand with `--update`.
- `--wait --key <key>` returns the answer as JSON; exit 3 means keep waiting, exit 4 means the page closed unanswered.
- Locking a card sets the build path: a locked comp builds comp-led, a locked sketch or wireframe builds code-led ([Comp-led build](comp-led-build.md)).

## Keys never reach git

Committed files such as the surface brief record a salted `seed:` reference, never the seed key, and everything under `.impeccable/questions/` ignores itself in git. The full rule, and what the roll sends over the network, is in [Telemetry](telemetry.md).

The `seed-*` and `question-*` oracle cases pin this output.
