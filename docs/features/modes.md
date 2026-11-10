# Modes

This page states what a [mode](../glossary/mode.md) is, where it is stored, and what it changes. It is for anyone reading or changing skill text, surface briefs or `concept-seed` output.

A mode names what the visitor's success looks like on one surface. There are four.

| Mode | The visitor | Typical surfaces | Default build path |
|---|---|---|---|
| Persuade | decides and acts; design is the product | landing pages, marketing, campaigns, pricing | the project's recorded default |
| Operate | completes a task | app UI, dashboards, editors, admin, settings, tools | code-led |
| Read | understands something | docs, articles, guides, help, changelogs | code-led |
| Experience | is inside the work itself | portfolios, galleries, showcases | the project's recorded default |

## Guarantees

**Mode is per surface.** A tool's landing page is Persuade even though the product is Operate; a docs index is Read. The rule lives in the Modes section of [`skill/SKILL.src.md`](../../skill/SKILL.src.md).

**Mode is stored only in the surface brief.** It persists in that surface's brief under `.impeccable/surfaces/` ([surface brief](../glossary/surface-brief.md)), never in `PRODUCT.md`. A `## Register` section left in `PRODUCT.md` by an older version is reported as deprecated and read by nothing: the reason text is `PRODUCT_DEPRECATED_SECTIONS` in `crates/context/src/artifact_schema.rs`, and the report is described in [Artifact staleness](artifact-staleness.md).

**Mode rules reach the agent as engine output.** `impeccable concept-seed --mode <mode>` prints a MODE RULES block with the mode file's `## Directions` and `## Comps` sections verbatim. Persuade and Experience share `skill/reference/mode-persuade.md`; Operate uses `mode-operate.md`; Read uses `mode-read.md` (`mode_rules_file` in `crates/context/src/concept_seed.rs`). The block governs every direction and comp for that surface and wins over shared guidance. When the file or a section is missing, the roll still succeeds and prints a one-line fallback naming the file. The oracle cases `seed-mode-rules-persuade`, `-operate`, `-read`, `-experience`, `-missing-file` and `-missing-section` pin this.

**Operate and Read build in code.** `code_led_mode` returns true for `operate` and `read`, so the PRESENTATION block names a code-led path whatever `buildPath` the project records. The decision page keeps its toggle, so one round can still be flipped to a comp. The recorded default governs Persuade and Experience only. See [Comp-led build](comp-led-build.md) and the [build path](../glossary/build-path.md) entry.

**Ambition scales with mode.** Persuade and Experience get bold, committed work; on Operate and Read, ambition shows as precision and restraint. This is skill text, not engine behavior, so no test enforces it beyond the LLM-backed suite described in [`tests/skill-behavior/README.md`](../../tests/skill-behavior/README.md).

## Why per surface

The old project-wide axis let one product's character steer every page it owned. A per-surface mode lets a single repository carry a loud landing page and a quiet settings screen without either compromising the other. The image comps of working screens also rated below code-led builds of the same briefs, which is why Operate and Read default to code.

Mode is orthogonal to delivery target. See [Platforms](platforms.md).
