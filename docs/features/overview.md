# What Impeccable is

This page names the parts of Impeccable and the one promise each makes. It is for anyone who needs the product's shape before reading a single capability's contract.

Impeccable is a design skill for coding agents, backed by a native binary. The agent reads the skill text; every action the skill asks for runs through that binary. Nothing in the product needs Node, Python or a package install in the user's project.

## The parts

| Part | What it promises | Contract |
|---|---|---|
| The skill | One user-invocable skill, `impeccable`, with 24 [commands](../glossary/command.md) routed from the Commands table in [`skill/SKILL.src.md`](../../skill/SKILL.src.md) | [Modes](modes.md), [Platforms](platforms.md) |
| The [engine](../glossary/engine.md) and [launcher](../glossary/launcher.md) | One static binary per platform, found or fetched by a shell launcher next to the skill | [Engine and launcher](engine-and-launcher.md) |
| The detector | Deterministic [anti-pattern rules](../glossary/anti-pattern-rule.md) over files, URLs and live pages, with stable exit codes | [Detector](detector.md) |
| The [design hook](../glossary/design-hook.md) | The detector, run by the agent's harness after UI edits | [Design hook](design-hook.md) |
| [Live mode](../glossary/live-mode.md) | Variants of a picked element, written to real source and cycled in the browser | [Live mode](live-mode.md) |
| New work | A seeded direction round and decision page before any new visual world is coded | [New work and concept-seed](new-work-and-concept-seed.md) |
| Comp-led build | A build gated by measurement against an approved [comp](../glossary/comp.md) | [Comp-led build](comp-led-build.md) |
| Staleness checks | Reports on project artifacts an older version wrote | [Artifact staleness](artifact-staleness.md) |
| Telemetry | Choice pings for dealt rolls, with opt-outs that never block the feature | [Telemetry](telemetry.md) |
| Rule packs | Downstream crates add rules without forking | [Rule packs](rule-packs.md) |
| Browser extension | The same rules in a DevTools panel | [Extension](extension.md) |

## What the product writes into a project

`PRODUCT.md` holds product truth ([PRODUCT.md](../glossary/product-md.md)). `DESIGN.md` holds durable visual decisions ([DESIGN.md](../glossary/design-md.md)). Per-surface strategy lives in a [surface brief](../glossary/surface-brief.md) under `.impeccable/surfaces/`. Runtime state lives under `.impeccable/`, and the parts of it that hold seed keys or dealt hands ignore themselves in git.

## Where the public product ends

This repository holds the open product: the skill, the engine workspace, the npm shim and the extension. The concept and composition catalogs, the roll API and the impeccable.style site are a private service. Without a signed-in account, the engine reaches that service only for `concept-seed` rolls, choice pings and the update check, and none of those calls blocks a verb when it fails ([Telemetry](telemetry.md)). The full split is in the Repo split section of [CLAUDE.md](../../CLAUDE.md).

Mode, not project, decides how ambitious a surface may be. Read [Modes](modes.md) next.
